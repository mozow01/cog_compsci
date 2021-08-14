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

i ~ categorical((1,2),(P<sub>1</sub>,P<sub>2</sub>))

$Theta




Legyen adva a két M<sub>i</sub> = (P<sub>i</sub>( @theta; ), L<sub>i</sub>( x | @theta; ) ) generatív modell és az ezekhez tartozó P(M<sub>i</sub>) "priorok", amik azt mondják meg, hogy mennyi a valószínűsége a  

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

## Anekdoikus dönési helyzet

* A fenti kérdés az absztrakt matematikai jelölések megértésére vonatkozott. Ugyanebben a mérésben a 24 átlagos gimnazista közül 11 mondta, hogy a "hagyományos" matekkal nem volt gondja. A 31 elitgimnazista között ez a szám 19. Mennyire tartható a M<sub>1</sub> (mutatis mutandis)?

Ebben az esetben legyen M<sub>1</sub> priorja beta(30,55), ami a 11/24 várható értéknek felel meg. Az adatok alapján:

P(19|M<sub>2</sub>)/P(19|M<sub>1</sub>) = 4.32

azaz az egyenletes eloszlás még mindig jobban magyaráz, de már csak **anekdotikusan**, a 3.16 < K < 6. Különbség tehát kimutatható, de már messze nem olyan hihetően, mint az előbb.

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/homer.png" width=300>

## Érettségi eredmények, döntő eredmény

A középszintű matematika érettségi eredményét, mint viszonylag objektív adatot hasonlíthatjuk össze a párhuzamos osztály egy csoportjával ill. egy elitgimnázium végzős évfolyamának eredményével.

Generatív modell: 


<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/ketevi_3.png" width=500>

````Javascript
var multiModel = function() {
  
   var vector = Vector([4, 6, 4, 2]);
    var x = dirichlet(vector);
  
    var x1 = (x.data)[0];
    var x2 = (x.data)[1];
    var x3 = (x.data)[2];
    var x4 = (x.data)[3];
  
    var s = x1 + x2 + x3 + x4;
  
  observe(Multinomial({ps: [x1/s,x2/s,x3/s,x3/s], n: 1}), [0,0,1] );
   var w = multinomial({ps: [x1/s,x2/s,x3/s,x3/s], n: 1});
   
    return {
      s: w
         // Prior: prior, 
           //PredictivePrior: predictivePrior, 
         // Posterior: , 
         // PredictivePosterior: predictivePosterior
    };
}      

var opts = {method: 'MCMC'}
var output_1 = Infer(opts,multiModel)

viz.marginals(output_1)
````

Eredmények:


<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/ketevi_2.png" width=600>

A vizsgált csoportot, az A jelűt vetettük össze két kontrollcsoporttal. Az egyik az iskolán belüli B, amely egy kisebb, homogénabb félosztály volt. A másik, a C jelű az egyik elitgimnázium teljes végzős évfolyama. A bayesiánus elemzésből az derül ki, hogy a B, mint prior messze jobban magyarázza a mért csoport eredményeit, mint a C. A Mann–Whitney-próba szerint azonban nincs kimutatható különbség a B és az A ordinális mérési szintű adatsorok mediánja között (p=0.87>0.05).
