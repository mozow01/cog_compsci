# Gráfmodellek (folytatás)

## Ismétlés 

A gráfmodell alapjául szolgáló _G_ irányított körmentes gráf (DAG) egy spéci címkézett osztályba tarozik. A **bekarikázott** csúcsok valószínűségi változókat reprezentálnak (és elég ezeket feltüntetni, de nem kell csak ezeket), a **bekarikázatlanok** determinisztikus változókat. A **nyilak** a p(x<sub>0</sub>,...,x<sub>K</sub>) joint valószínűség alábbi alakú szorzattá bonthatóságát fejezi ki valamilyen értelemben:

<img src="https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle%20%5Cmathrm%7Bp%7D(%5Cmathbf%7Bx%7D)%3D%5Cprod_%7Bk%3D1%7D%5E%7BK%7D%5Cmathrm%7Bp%7D(x_k%5Cmid%5Cmathrm%7Bpare%7D_k)">

ahol pare<sub>k</sub> az x<sub>k</sub> csúcs (közvetlen) szülei. A szorzatban pontosan akkor szerepel a p(x<sub>k</sub> | x<sub>i</sub>,...,x<sub>j</sub>) tényező, ha a G-ben van x<sub>i</sub>,...,x<sub>j</sub> pontjaiból x<sub>k</sub>-ba mutató nyíl. A **besatírozott** csúcsok olyan valószínűségi változók, amelyek a megfigyelt változókat reprezentálják. Tehát G gráfmondellje a p(x<sub>0</sub>,...x<sub>K</sub>) joint valószínűségnek, ha a fenti faktorizációs tulajdonság teljesül.

A faktoritációs tulajdoságot ténylegesen felezni tudjuk **faktorpontokkal.** Ekkor pontosan akkor köti össze egy faktor pont egy gyerekeket és

## Bayes-tétel (évszak és vizes fű)

A generatív modell (azaz egy "randomoutput(randominput)" algoritmikus függvény) négy valószínűségi változóból áll elő, "évszak" (h) (tavasz/nyár/ősz/tél) , "felhős" (f) (derűs/enyhén felhős/erősen felhős), "locsolórendszer" (l) (megy/nem megy), "eső" (e) (esik/nem esik), "vizes a fű" (v) (vizes/nem vizes). Ezek az alábbi gráf alapján függnek egymástól (a faktorok valóban azt jelzik, hogy a joint valószínűség hogyan bomlik szorzatá).

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/locsolo_1.png" width=500>

És a webppl kód:

````javascript
var vizesModel = Infer({ method: 'enumerate' }, function(){
  var évszak = categorical({ps:[0.25,0.25,0.25,0.25], 
                            vs: ['tavasz', 'nyár', 'ősz', 'tél'] });
  
  var felhős = ((évszak === 'tavasz') || (évszak === 'ősz' )) ? flip(0.9) : flip(0.5);
  
  var esik = felhős ? flip(0.8) : flip(0.0);
 
  var locsoló = felhős ? flip(0.1) : flip(0.9);
  
  var vizes = esik && locsoló 
                   ? flip(.99)  
                   : (esik && !locsoló ) || (esik && !locsoló ) 
                        ? flip(0.9) 
                        : flip(0.1);
       condition(vizes === true);
  
  var évszakPrior = categorical({ps:[0.25,0.25,0.25,0.25], 
                            vs: ['tavasz', 'nyár', 'ősz', 'tél'] });
  
  var felhősPrior = ((évszakPrior === 'tavasz') || (évszakPrior === 'ősz' )) ? 
      flip(0.9) : flip(0.5);
  
       return {évszakPrior: évszakPrior, felhősPrior: felhősPrior,
               évszakPost: évszak, felhősPost: felhős  };
});

viz.marginals(vizesModel)
````

Itt "évszak" előtt van egy konstans csúcs, ami a kategorikus változó eloszlásának adatait (hogy egyenletes) tartalmazza. Ezek a paraméterek azonban rögzítettek.

A **Bayes-tétel** szerint

<img src="https://render.githubusercontent.com/render/math?math=P(latens%5Cmid%20megfigyelt)%20%3D%20%5Cdfrac%7BP(megfigyelt%20%5Cmid%20latens%20)%5Ccdot%20P(latens)%7D%7BP(megfigyelt)%7D">

Ill. itt: 

<img src="https://render.githubusercontent.com/render/math?math=P(honap%5Cmid%20vizes)%20%3D%20%5Cdfrac%7BP(vizes%20%5Cmid%20honap%20)%5Ccdot%20P(honap)%7D%7BP(vizes)%7D">

Rögzített inputtal (h) a generatív modell segítségével ki lehet számolni, mi a vizes fű változó feltételes eloszlása: 

<img src="https://render.githubusercontent.com/render/math?math=vizes%5Cquad%20%5Cmapsto%20%5Cquad%20P(vizes%20%5Cmid%20honap%20)">

(ez tehát egy előrejelzés).

A generatív modell programozott verziója a fenti kódban ez:

````javascript
var évszak = categorical({ps:[0.25,0.25,0.25,0.25], vs: ['tavasz', 'nyár', 'ősz', 'tél'] });

var felhős = ((évszak === 'tavasz') || (évszak === 'ősz' )) ? flip(0.9) : flip(0.5);
  
var esik = felhős ? flip(0.8) : flip(0.0);
 
var locsoló = felhős ? flip(0.1) : flip(0.9);
  
var vizes = esik && locsoló 
                   ? flip(.99)  
                   : (esik && !locsoló ) || (esik && !locsoló ) 
                        ? flip(0.9) 
                        : flip(0.1);
````

A pszeudokódját az ábrán látjuk.

Ha rögzítjük az outputot(v), akkor vagy mintavételezéssel vagy kimerítéssel, de ki lehet számolni, hogy az egyes inputok milyen valószínűséggel adják ezt a v-t:

<img src="https://render.githubusercontent.com/render/math?math=honap%5Cquad%20%5Cmapsto%20%5Cquad%20P(vizes%20%5Cmid%20honap%20)">

ez a **likelihood függvény,** sajnos ez **nem eloszlás**. Ahhoz az évszak változó felett (a feltétel mellett) egy valószínűségi eloszlást kapjunk, még súlyozni kell a "évszak" marginális eloszlásával és 1-re normálni kell ezt a mennyiséget. Akkor a Bayes-tétel szerint ez az új mennyiség már eloszlás, ami a posterior. 

A Bayes-inferencia most "kimerítéssel" (enumerate) és "az adat feltételezésével" (condition) működik: a hónap változó marginális eloszlását számolja ki azzal a feltétellel, hogy adat = vizes, az összes eset végigszámolásával.

## Konjugált prior

Nyilvánvaló, hogy a **jelenséget** a generatív modell tartalmazza, ami pedig a likelihood függvényt számolja ki valamilyen módon. Ez most az egyszerűségében is elég bonyi, de maradjunk annyiban, hogy valami kategorikus változó a bemenet és Boole-értékű a kimenet. Az elemzést tovább finomíthatjuk úgy, hogy a prior paramétereit variáljuk. Most a priot egy egyszerű **kategorikus** változó: egy zsákban színes golyók vannak rögzített arányban és egy golyót húzunk belőle. A prior paramétereire tett elméleti feltételezést hiperpriornak nevezzük. 

Hogyan választunk hiperpriort (vagy általában priort)?  

Bárhogy. De ha szép eredményt akarunk, akkor a likelihoodhoz (vagyis az alapjelenséghez) olyan hiperpriot eloszlást (másik jelenséget) kell választanunk, amivel ha a likelihood-ot megszorozzuk ugyanolyan jelenséget kapunk, mint a hiperprior. Ez azért van, mert azt gondoljuk, hogy a Bayes-i update-elés valóban élesítés: egy y |----> f(p,y) (prior) függvénycsaládból választja ki azt a p paramétert, amit az adatok mellett a legvalószínűbb. Nem kell feltétlenül így tennünk, mert úgy is numerikus a számítás és a gép kidobja az eloszlást mindenképpen. De ez az ajánlás, nem teljesen hülyeség. 

[https://en.wikipedia.org/wiki/Conjugate_prior]

Például a kategorikus változó számára a konjugált prior a Dirichlet-eloszlás. A binomiális ("hányan hibáztak rá a jó válaszra") változóhoz a beta. A beta általánosítása a Dirichlet.

A locsolós példa ezzel módosítva:

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/locsolo_2.png" width=500>

És a kód:

````javascript
var vizesmasikModel = Infer({ method: 'rejection' }, function(){
  
  var x = dirichlet({alpha: Vector([1/10,2/10,7/10])});

    var x1 = (x.data)[0];

    var x2 = (x.data)[1];

    var x3 = (x.data)[2];
  
  var felhős =  categorical({ps:[x1,x2,x3], 
                            vs: ['derült', 'enyhén felhős', 'erősen felhős'] });
  
  var esik = (felhős === 'derült')
                   ? flip(.1)  
                   : (felhős === 'enyhén felhős')
                        ? flip(0.6) 
                        : flip(0.9);
 
  var locsoló = (felhős === 'derült')
                   ? flip(.9)  
                   : (felhős === 'enyhén felhős')
                        ? flip(0.2) 
                        : flip(0);
  
  var vizes = esik && locsoló 
                   ? flip(.99)  
                   : (esik && !locsoló ) || (esik && !locsoló ) 
                        ? flip(0.9) 
                        : flip(0.1);
       condition(vizes === true);
  
  var y = dirichlet({alpha: Vector([1/10,2/10,7/10])});
   
    var y1 = (y.data)[0];

    var y2 = (y.data)[1];

    var y3 = (y.data)[2];
  
  var felhősPrior = categorical({ps:[y1,y2,y3], 
                            vs: ['derült', 'enyhén felhős', 'erősen felhős'] });
  
  
       return {derűsHyperPrior: y1, enyhenHyperPrior2: y2, erösenHyperPrior: y3, 
               felhősPrior: felhősPrior,
               felhősPosterior: felhős  };
});

viz.marginals(vizesmasikModel)
````

## Multinomiális eloszlás

Legyen adva k hely, ahova n = 1 vagy 2 vagy 3 ... k golyót rakunk le. Ezzel megadjuk, hogy egy kategóriális változó mely értékeit jelüljük ki. Például van egy kérdés, amelyekre az a) -- b) -- c) -- d) -- ... válaszokat választhatjuk ki. Ha több válasz is, de mindig ugyanannyi megadható, akkor n > 1. Valahonnan tudjuk, hogy az egyes lehetőségeket az emberek a p<sub>1</sub>,...,p<sub>k</sub> valószínűségekkel választják. (Persze p<sub>1</sub>+...+p<sub>k</sub> = 1.) A kérdés, hogy mi annak a valószínűség hogy valaki az a)-t éppen n<sub>a</sub>, a b)-t n<sub>b</sub>, ... db-szor választotta. 


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

## Adattér kategorikus és multinomiális esetén

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

  


