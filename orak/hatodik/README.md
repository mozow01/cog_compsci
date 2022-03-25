# Multinomiális eloszlás

Legyen adva k hely, ahova n = 1 vagy 2 vagy 3 ... k golyót rakunk le. Ezzel megadjuk, hogy egy kategóriális változó mely értékeit jelüljük ki. Például van egy kérdés, amelyekre az a) -- b) -- c) -- d) -- ... válaszokat választhatjuk ki. Ha több válasz is, de mindig ugyanannyi megadható, akkor n > 1. Valahonnan tudjuk, hogy az egyes lehetőségeket az emberek a p<sub>1</sub>,...,p<sub>k</sub> valószínűségekkel választják. (Persze p<sub>1</sub>+...+p<sub>k</sub> = 1.) A kérdés, hogy mi annak a valószínűség hogy valaki az a)-t éppen n<sub>a</sub>, a b)-t n<sub>b</sub>, ... db-szor választotta. 

## Érettségi osztályzatok

Az egyik csoportban az alábbi jegyek születtek (egy adat egy jegyet kódon a (kettes, hármas, négyes, ötös) vektor egy koordinátájának kiválasztásával). Ezt összevetjük a másik csoport eredényeivel, mint priorral (4 kettes, 6, hármas, 4 jó és 2 jeles). Mennyire magyarázza a második csoport eredménye, mint prior modell az első csoport eredményét. (Meg, ugye, mihez képest :) )


<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/ketevi_2.png" width=500>

````javascript

var multiModel = function() {  

  var data =[{v: [1,0,0,0]}, {v: [1,0,0,0]}, {v: [1,0,0,0]}, {v: [1,0,0,0]}, 

             {v: [1,0,0,0]}, {v: [1,0,0,0]}, {v: [1,0,0,0]}, {v: [1,0,0,0]},

             {v: [0,1,0,0]}, {v: [0,1,0,0]}, {v: [0,1,0,0]}, {v: [0,1,0,0]},

             {v: [0,1,0,0]}, {v: [0,1,0,0]}, {v: [0,1,0,0]}, {v: [0,1,0,0]},

             {v: [0,1,0,0]}, {v: [0,1,0,0]}, {v: [0,0,1,0]}, {v: [0,0,1,0]},

             {v: [0,0,1,0]}, {v: [0,0,1,0]}, {v: [0,0,0,1]}, {v: [0,0,0,1]},

             {v: [0,0,0,1]}, {v: [0,0,0,1]}, {v: [0,0,0,1]},

            ];


    var vector = Vector([4,6,4,2]);
    
    //[6, 13, 44, 73]
    
    //[8, 10, 4, 4]
    
    var x = dirichlet(vector);

    var x1 = (x.data)[0];

    var x2 = (x.data)[1];

    var x3 = (x.data)[2];

    var x4 = (x.data)[3];

   var s = x1 + x2 + x3 + x4;


map(function(d){observe(Multinomial({ps: [x1/s,x2/s,x3/s,x4/s], n: 1}), d.v)}, data);

   
  var w = multinomial({ps: [x1/s,x2/s,x3/s,x4/s], n: 1});

    return {
      PredictivePosterior: w
    };

}      


var opts = {method: 'MCMC', samples: 10000}

var output_1 = Infer(opts,multiModel)

````

A modell "kritika" egy változata, ha megnézzük kvalitatív módon, hogy vannak-e olyan helyek az eloszlásban, ahol vannak a modell nem jól jósol. 

## Adattér kategorikus és multinomiális esetén

Multinomiálisnál egyre kell, hogy összeződjenek a ps-ek, kategorikusnál ez nem megszorítás.

````javascript
var cat1 = Infer({method: 'enumerate'}, function(){
  
    //var Probs = dirichlet(Vector([2, 3, 1, 1, 1]))
    return categorical({vs: ['2-es', '3-as', '4-es', '5-ös'], 
                        ps: [0.2756,  0.3516, 0.2076, 0.1663]})
  })

viz.auto(cat1, {xLabel: 'poszterior; B prior', yLabel: 'gyakoriság'});
````



````javascript
var cat1 = Infer({method: 'enumerate'}, function(){
  
    return multinomial({ ps: [0.2,  0.1, 0.4, 0.25, 0.05], n: 1})
  })


viz.hist(cat1, {xLabel: 'esetek', yLabel: 'valószínűség'});
  
````




````javascript
var cat1 = Infer({method: 'enumerate'}, function(){
  
    return multinomial({ ps: [0.2,  0.1, 0.7], n: 3})
  })


viz.hist(cat1, {xLabel: 'esetek', yLabel: 'valószínűség'});
````


# Bayes-i hipotézisvizsgálat


## Halmazjelölések -- külvárosi és menő gimi

* Egy 24 fős átlagos gimnáziumi osztályban a diákok közül 6 válaszolta (25%), hogy nem volt gondja a matekkal. Egy elitgimnáziumban ugyanez a szám 31-ből 17. Két binomiális eloszlást szimuláló modellel élünk. M<sub>1</sub> szerint a prior 0.25 várható értékű (E(X)=a/(a+b)) dogmatikus beta eloszlás (beta(30,90), kis szórás), amellyel azt feltételezzük, hogy tényleg az átlagos gimnázium adja az átlagot, M<sub>2</sub> pedig egyenletes és bármi kijöhetett volna, mert a gimnáziumok között igen nagy eltérések is lehetnek. Melyik modell magyarázza jobban a megfigyelt 7/31 értéket?

Az ordinális skálán működő klasszikus Mann--Whitney-próba p = 0.06-ot ad, azaz határeset, pedig a vak is látja, hogy lényeges eltérés van a két diákcsoport között.

````javascript
var model = function() {
   // var p = uniform(0,1);          //M_2 modell: bármi lehet a p prior eloszlása
var p = beta(30,90);                 //M_1 modell: az átlagos diákok a prior
  observe(Binomial({p : p, n: 31}), 17);

var poszterior_predikativ = binomial(p,31); // ezzel az új p-vel a szimulált adatok

   //var prior_p = uniform(0,1);
   var prior_p =  beta(30,90); // érintetlen paraméter

var prior_predikativ = binomial(prior_p,31); // érintetlen paraméterből szimulált adatok

return {
       Prior: prior_p, 
       Posterior : p,
       PredictivePrior : prior_predikativ,
       PredictivePosterior : poszterior_predikativ};
};

var output = Infer({model: model, samples: 10000, method: 'MCMC'});

viz.marginals(output);
````

## Bayes-faktor

Modell összehasonlításnál voltaképpen egy hierarchikus modellt építünk: 

i ~ categorical((1,2),(P<sub>1</sub>,P<sub>2</sub>)) (hiperprior)

&theta; ~ p<sub>i</sub>(&theta;) (prior)

x ~ P<sub>i</sub>(x | &theta;) (likelihood)

Ekkor a 

A bayesiánus modell összehasonlítás a következőképpen megy. Kiszámítjuk mindkét esetben a 

<img src="https://render.githubusercontent.com/render/math?math=P(k%3Dk_0%5Cmid%20M_i)">

i=1 és i=2 modellre jellemző **likelihood** értéket. Ezek hányadosa a **Bayes-faktor:**

<img src="https://render.githubusercontent.com/render/math?math=K%3D%5Cdfrac%7BP(k%3Dk_0%5Cmid%20M_1)%7D%7BP(k%3Dk_0%5Cmid%20M_2)%7D">

Az alábbi táblázat (az egyik olyan amiben) a K értékek szerint látható, hogy M<sub>1</sub> mennyire magyarázza a jelenséget a megfigyelt változó ismeretében, mint M<sub>2</sub>.

|K|Az M<sub>1</sub> melletti bizonyíték erőssége|
|---|---|
|... < 10<sup>0</sup>= 1| Negatív: M<sub>1</sub> elvetendő|
|10<sup>0</sup> < ... < 10<sup>1/2</sup>=3.16| Alig érdemes említeni| 
|10<sup>1/2</sup> < ... < 10<sup>3/4</sup>=6| Anekdotikus| 
|10<sup>3/4</sup> < ... < 10<sup>1</sup>=10| Szubsztanciális (létező) |
|10<sup>1</sup> < ... < 10<sup>3/2</sup>=31.62| Erős|
|10<sup>3/2</sup> < ... < 10<sup>2</sup>=100 | Nagyon erős|
|10<sup>2</sup> < ... | Döntő |

## Egy erős döntési helyzet

A konkrét példába, a program lefutását követően, az adatokból: P(17|M<sub>1</sub>), P(17|M<sub>2</sub>) kiszámítása után: K=P(17|M<sub>2</sub>)/P(17|M<sub>1</sub>)=

K = 15,47 > 10

azaz a próba **erősen** M<sub>2</sub>-t részesíti előnyben és az M<sub>1</sub> erősen elvetendő. Tehát az elitgimnáziumi érték nagyon nem vehető olyannak, ami egy véletlenül adódó átlagos gimnáziumi osztály eredménye lehetne.

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/ketevi_1.png" width=600>

## Anekdotikus döntési helyzet

* A fenti kérdés az absztrakt matematikai jelölések megértésére vonatkozott. Ugyanebben a mérésben a 24 átlagos gimnazista közül 11 mondta, hogy a "hagyományos" matekkal nem volt gondja. A 31 elitgimnazista között ez a szám 19. Mennyire tartható a M<sub>1</sub> (mutatis mutandis)?

Ebben az esetben legyen M<sub>1</sub> priorja beta(30,55), ami a 11/24 várható értéknek felel meg. Az adatok alapján:

P(19|M<sub>2</sub>)/P(19|M<sub>1</sub>) = 4.32

azaz az egyenletes eloszlás még mindig jobban magyaráz, de már csak **anekdotikusan**, a 3.16 < K < 6. Különbség tehát kimutatható, de már messze nem olyan hihetően, mint az előbb.

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/homer.png" width=300>


## Modellösszehasonlítás és hiperpriorok

Döntsünk a két város között! Azt tudjuk, hogy derült az ég. Melyik városban vagyunk?

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/muut_1-1.png" width=1000>

````javascript
var HiperModel = Infer({method: 'rejection', samples: 10000 }, 
                       function(){
  
  // a látens változó a Város, amit inferálunk a modellek indexei:
  
  var Város = categorical({ps:[0.5,0.5], vs: ['Winden', 'Hawkins'] }); 
  
  // ez csak azért, hogy az "érintetlen Város" hiperpriort ábrázolni tudjuk
  
  var Város_hiper_prior = categorical({ps:[0.5,0.5], vs: ['Winden', 'Hawkins'] });
  
  // a két város priorja: milyen eloszlást feltételezünk a Felhősség priorjára, 
  // categorical változó konjugált priorja dirichlet
  
  var Winden = dirichlet({alpha: Vector([0.1,0.2,0.7])});
  
    var x1 = (Winden.data)[0];

    var x2 = (Winden.data)[1];

    var x3 = (Winden.data)[2]; 
  
  var Hawkins = dirichlet({alpha: Vector([0.5,0.3,0.2])});
    
    var y1 = (Hawkins.data)[0];

    var y2 = (Hawkins.data)[1];

    var y3 = (Hawkins.data)[2]; 
  
   // ez csak azért, hogy az "érintetlen" priort ábrázolni tudjuk
  
   var Winden_prior = dirichlet({alpha: Vector([0.1,0.2,0.7])});
    var u1 = (Winden.data)[0];
    var u2 = (Winden.data)[1];
    var u3 = (Winden.data)[2]; 
  
  var Hawking_prior = dirichlet({alpha: Vector([0.5,0.3,0.2])});
    var v1 = (Hawkins.data)[0];
    var v2 = (Hawkins.data)[1];
    var v3 = (Hawkins.data)[2]; 
  
  // ez lesz a mért változó:
  
  var Felhősség = (Város === 'Winden')
                   ? categorical({ps:[x1,x2,x3],
                                  vs: ['derült', 'enyhén felhős', 'erősen felhős'] })
                   : categorical({ps:[y1,y2,y3],
                                  vs: ['derült', 'enyhén felhős', 'erősen felhős'] })
  condition(Felhősség === 'derült');
  
  var Felhős_Winden_prior = categorical({ps:[u1,u2,u3],
                                  vs: ['derült', 'enyhén felhős', 'erősen felhős'] })
  
  var Felhős_Hawkins_prior = categorical({ps:[v1,v2,v3],
                                  vs: ['derült', 'enyhén felhős', 'erősen  felhős'] })
                     
  var Esik = (Felhősség === 'derült') 
                   ? flip(0.1)  
                   : (Felhősség === 'enyhén felhős')
                        ? flip(0.6) 
                        : flip(0.9);
  
  return {város_hiper_prior: Város_hiper_prior, 
          Winden_prior: Felhős_Winden_prior, 
          Hawkins_prior: Felhős_Hawkins_prior, 
          város_poszterior: Város, 
          felhős_poszterior: Felhősség,
          esik: Esik};
});
  

viz.marginals(HiperModel)
````

A következő két stratégia ajánlkzik a Bayes faktor kiszámítására.

<img src="https://render.githubusercontent.com/render/math?math=%5Cmathrm%7BBF%7D_%7B12%7D%3D%20%5Cdfrac%7BP(D%7CM_1)%7D%7BP(D%7CM_2)%7D%0A%3D%20%5Cdfrac%7BP(M_1%7CD)%7D%7BP(M_2%7CD)%7D%5Ccdot%5Cdfrac%7BP(M_2)%7D%7BP(M_1)%7D">
  
Ha a modellek valószínűsége ugyanaz (nem feltétetezzük, hogy valamelyik kitüntetettebb), akkor P(M2)/P(M1) = 1 és nem kell vele foglalkozni. (Ha nem az, akkor meg átszorzunk vele, oszt jónapot.)

1. A BF annak a két mennyiségnek az aránya, hogy a mért adat mennyire valószínű a két modellben. Ezt 1 adat esetén egyszerűen a prediktív priorból leolvashatjuk. Sok adat esetén nem ilyen egyszerű. Mindenesetre azért használunk prediktív priort, mert a modelleket vetjük össze és az adatokkal frisített poszteriorokat.

2. A fenti számolás miatt [https://en.wikipedia.org/wiki/Bayes_factor] a BF ugyanaz, mint annak a két mennyiségnek a hányadosa, hogy a két modell mennyire valószínű, feltéve, hogy tudjuk az adatott. Azaz bevezetjük a modell i indexét (i=1;2), mint hiperparamétert, frissítjük a P(M2),P(M1) priorokat (P(M2|D),P(M1|D)) és megnézzük mi lett a poszteriorok hányadosa, melyik modell valószínűbb, tudva a D adatot. 

Ez utóbbit alkalmaztuk a fenti példánál D=derűs, i = Város = {Winden; Hawkins}.

