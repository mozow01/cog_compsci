# Gráfmodellek

A gráfmodell alapjául szolgáló _G_ irányított körmentes gráf (DAG) egy spéci címkézett osztályba tarozik. A **bekarikázott** csúcsok valószínűségi változókat reprezentálnak (és elég ezeket feltüntetni, de nem kell csak ezeket), a **bekarikázatlanok** determinisztikus változókat. A **nyilak** a p(x<sub>0</sub>,...,x<sub>K</sub>) joint valószínűség alábbi alakú szorzattá bonthatóságát fejezi ki valamilyen értelemben:

<img src="https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle%20%5Cmathrm%7Bp%7D(%5Cmathbf%7Bx%7D)%3D%5Cprod_%7Bk%3D1%7D%5E%7BK%7D%5Cmathrm%7Bp%7D(x_k%5Cmid%5Cmathrm%7Bpare%7D_k)">

ahol pare<sub>k</sub> az x<sub>k</sub> csúcs (közvetlen) szülei. A szorzatban pontosan akkor szerepel a p(x<sub>k</sub> | x<sub>i</sub>,...,x<sub>j</sub>) tényező, ha a G-ben van x<sub>i</sub>,...,x<sub>j</sub> pontjaiból x<sub>k</sub>-ba mutató nyíl. A **besatírozott** csúcsok olyan valószínűségi változók, amelyek a megfigyelt változókat reprezentálják. Tehát G gráfmondellje a p(x<sub>0</sub>,...x<sub>K</sub>) joint valószínűségnek, ha a fenti faktorizációs tulajdonság teljesül.

A faktoritációs tulajdoságot ténylegesen felezni tudjuk **faktorpontokkal.** Ekkor pontosan akkor köti össze egy faktor pont egy gyerekeket és

## Diszkrét hierarchikus modell (évszak és vizes fű)

A generatív modell (azaz egy "randomoutput(randominput)" algoritmikus függvény) négy valószínűségi változóból áll elő, "évszak" (h) (tavasz/nyár/ősz/tél) , "felhős" (f) (derűs/enyhén felhős/erősen felhős), "locsolórendszer" (l) (megy/nem megy), "eső" (e) (esik/nem esik), "vizes a fű" (v) (vizes/nem vizes). Ezek az alábbi gráf alapján függnek egymástól (a faktorok valóban azt jelzik, hogy a joint valószínűség hogyan bomlik szorzatá).

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/locsolo_1.png" width=600>

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

Nyilvánvaló, hogy a **jelenséget** a generatív modell tartalmazza, ami pedig a likelihood függvényt számolja ki valamilyen módon. Ez most az egyszerűségében is elég bonyi, de maradjunk annyiban, hogy valami kategorikus változó a bemenet és Boole-értékű a kimenet. Az elemzést tovább finomíthatjuk úgy, hogy a prior paramétereit variáljuk. Most a prior egy egyszerű **kategorikus** változó: egy zsákban színes golyók vannak rögzített arányban és egy golyót húzunk belőle. A prior paramétereire tett elméleti feltételezést hiperpriornak nevezzük. 

Hogyan választunk hiperpriort (vagy általában priort)?  

Bárhogy. De ha szép eredményt akarunk, akkor a likelihoodhoz (vagyis az alapjelenséghez) olyan hiperprior eloszlást (másik jelenséget) kell választanunk, amivel ha a likelihood-ot megszorozzuk ugyanolyan jelenséget kapunk, mint a hiperprior. Ez azért van, mert azt gondoljuk, hogy a Bayes-i update-elés valóban élesítés: egy y |----> f(p,y) (prior) függvénycsaládból választja ki azt a p paramétert, amit az adatok mellett a legvalószínűbb. Nem kell feltétlenül így tennünk, mert úgy is numerikus a számítás és a gép kidobja az eloszlást mindenképpen. De ez az ajánlás, nem teljesen hülyeség. 

[https://en.wikipedia.org/wiki/Conjugate_prior]

Például a kategorikus változó számára a konjugált prior a _Dirichlet-eloszlás._ A binomiális ("hányan hibáztak rá a jó válaszra") változóhoz a beta. A beta általánosítása a Dirichlet. A gauss-nak a gauss.

A locsolós példa ezzel módosítva:

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/locsolo_2.png" width=600>

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

# Példák

## Binomiális kísérlet különböző méretű csoportokkal

Három különböző csoportban kérdezték meg, hogy a pillangó virág-e, a mért változó értékei a data változó alatt találhatók. A két modellben a priorok a non-informative és a dogmatikus volt. 

### Egyenletes prior

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/binom_1.png" width=500>

````javascript
var data = [{name: 'napocskas', n:20, k:5},
            {name: 'holdacskas', n:23, k:8},
            {name: 'napraforgo', n:19, k:17},
           ]

var simpleModel = function() {
  
  var p = uniform(0,1);
  
  map(function(d){observe(Binomial({p: p, n: d.n}), d.k)}, data);
  
  var prior = uniform(0,1);
  
  // 20 fős csoportokra normálva
  
  var predictivePosterior = binomial({p: p, n: 20});
  
  var predictivePrior = binomial({p: prior, n: 20});
  
  return {Prior: prior, 
          PredictivePrior: predictivePrior, 
          Posterior: p, 
          PredictivePosterior: predictivePosterior};
}

var opts = {method: 'MCMC', samples: 20000}

var output_1 = Infer(opts, simpleModel)

viz.marginals(output_1)
````

Tehát az adatot a **map** környezetbe ágyazva illesztjük be a feltételbe.

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/1559f8.svg" height=200><img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/30810f.svg" height=200><img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/bab66f.svg" height=200><img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/d0c66b.svg" height=200>

### Beta prior


<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/binom_2.png" width=500>

````javascript
var data = [{name: 'napocskas', n:20, k:5},
            {name: 'holdacskas', n:23, k:8},
            {name: 'napraforgo', n:19, k:17},
           ]

var complexModel = function() {
  
  var p = beta(30,90);
  
  map(function(d){observe(Binomial({p: p, n: d.n}), d.k)}, data);
  
  var prior = beta(30,90);
  
    // 20 fős csoportokra normálva
  
  var predictivePosterior = binomial({p: p, n: 20});
  
  var predictivePrior = binomial({p: prior, n: 20});
  
  return {Prior: prior, 
          PredictivePrior: predictivePrior, 
          Posterior: p, 
          PredictivePosterior: predictivePosterior};
}

var opts = {method: 'MCMC', samples: 20000}

var output_2 = Infer(opts, complexModel)

viz.marginals(output_2)
````

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/641286.svg" height=200><img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/6af94a.svg" height=200><img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/28e8ec.svg" height=200><img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/af2eb4.svg" height=200>


## Aranyhal

Egy aranyhal súlyadatai: 5, 16 és 17 g, három mérés után. Úgy döntünk, hogy ha az átlag a 15-17 sávba esik, akkor átlagos mennységet adunk neki, ha kevesebb, akkor többet, ha több, akkor kevesebbet. A kérdés, hogy számtani közepet számoljunk-e (12.7 g) vagy (informatív) priorból dolgozzunk-e. A komplexebb megközelítéshez tudjuk, hogy ezen halfajta súlya (x) normál eloszlást mutat, az átlaga 16 g, ennek az adatnak a szórása 0.2 g, továbbá az x normál elszolásának szórása 1 g. 

### Megoldás. 

#### Ha a prior uniform: 

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/gaussunif_1.png" width=500>

````javascript
var data = [{k: 5},
            {k: 16},
            {k: 17},
           ]

var simpleModel = function() {
  
  var m = uniform(4,18);
   
  map(function(d){observe(Gaussian({mu: m, sigma: 1}),d.k)},data);
  
  var Prior = uniform(4,18);
  
  var PredictivePosterior = gaussian(m,1);
  
  return {
          Prior: Prior, 
          Posterior: m,
          PosteriorPredictive: PredictivePosterior
         };
}

var opts = {method: 'SMC', particles: 2000, rejuvSteps: 5}

var output_1 = Infer(opts, simpleModel)

viz.marginals(output_1)
````
<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/260332.svg" height=200><img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/3777e1.svg" height=200><img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/7e1f19.svg" height=200>

#### Komplexebb modell:

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/gauss_dogm.png" width=500>


````javascript
var data = [{k: 5},
            {k: 16},
            {k: 17},
           ]

var complexModel = function() {
  
  var m = gaussian(16,0.2);
  
  map(function(d){observe(Gaussian({mu: m, sigma: 1}),d.k)},data);
  
  var Prior = gaussian(16,0.2);
  
  var PredictivePosterior = gaussian(m,1);
  
  return {
           Prior: Prior, 
           Posterior: m,
           PosteriorPredictive: PredictivePosterior};
}

var opts = {method: 'SMC', particles: 2000, rejuvSteps: 5}

var output_2 = Infer(opts, complexModel)

viz.marginals(output_2)

````

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/8628b1.jpg" height=200><img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/28c592.jpg" height=200><img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/1f1af8.jpg" height=200>

