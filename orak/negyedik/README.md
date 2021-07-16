# Bayes-i hipotézisvizsgálat

## Várható érték, kredibilitási intervallum

A maximum likelihood módszer pusztán pontbecslést ad, a legjobb paraméterértéket mondja meg. Most a  teljes posterior eloszlás megvan, ezért ki tudjuk számítani az eloszlás _várható értékét_ és a _kredibilitási (credible) intervallumot_ is, mondjuk 95%-ra.

````javascript
//óvodások folyt.
var model = function() {
  var p = uniform(0,1);
  observe(Binomial({p : p, n: 20}), 5);
  
  var poszterior_predikativ = binomial(p,20); // ezzel az új p-vel a szimulált adatok

return p;
};

var output = Infer({model: model, samples: 10000, method: 'MCMC'});
viz.auto(output);
print(expectation(output));
expectation(output,function(p){0.05<p && p<0.45})
````

Ahonnan a várható érték E(p) = 0.2797 és a 95%-os hihetőségi intervallum: (0.05,0.45), amit próbálgatással érdemes beállítani az E(p) körül. Ez azt mondja meg, hogy **a p paraméter hol helyezkedik el 95%-os valószínűséggel.** A bayesianizmusban tehát nem a p rögzített, hanem a kredibilitási intervallum.

## Beta prior

A binomiális eloszlásnál érdemes priornak beta(a,b) eloszlást választani, mert ez elég variábilis és sok eloszlást képes modellezni (ez részben azért van, mert a beta sok likelihood eloszláshoz konjugált, amit most nem érdemes részletezni).

A beta(50,200) olyan eloszlás, ami jó erősen a bal lábára terhel. Ezzel, priorként, azt feltételezzük, hogy az óvodások nagyrészt jól felismeri, hogy a pillangó virág vagy másféle és ezért a k várható értéke kicsi.

````javascript
var model = function() {
  var p = beta(50,200);
  
    observe(Binomial({p : p, n: 20}), 5);

  var poszterior_predikativ = binomial(p,20); // ezzel az új p-vel a szimulált adatok
  
  var prior_p = beta(50,200); // érintetlen paraméter

  var prior_predikativ = binomial(prior_p,20); // érintetlen paraméterből szimulált adatok

return {prior: prior_p, priorPredictive : prior_predikativ,
       posterior : p, posteriorPredictive : poszterior_predikativ};
};

var output = Infer({model: model, samples: 10000, method: 'MCMC'});

viz.marginals(output);
````

Ilyen erős (dogmatikus) prior esetén a megfigyelt adat már kisebb valószínűségű, de ez inkább csak azt jelenti, hogy nagyon komolyan vesszük a feltételezést és ezért a k=5 érték már kevésbé gyakori.

Amennyiben több adat is van, mondjuk k=5, k=6 és k=19, akkor a nyilvánvalóan hibás 19-es mért értéket ez a prior ügyesen ki fogja szűrni:

````javascript
var model = function() {
  var p = beta(50,200);
  
    observe(Binomial({p : p, n: 20}), 5);
    observe(Binomial({p : p, n: 20}), 6); 
    observe(Binomial({p : p, n: 20}), 19);

  var poszterior_predikativ = binomial(p,20); // ezzel az új p-vel a szimulált adatok
  
  var prior_p = beta(50,200); // érintetlen paraméter

  var prior_predikativ = binomial(prior_p,20); // érintetlen paraméterből szimulált adatok

return {prior: prior_p, priorPredictive : prior_predikativ,
       posterior : p, posteriorPredictive : poszterior_predikativ};
};

var output = Infer({model: model, samples: 10000, method: 'MCMC'});

viz.marginals(output);
````

Szemben az egyenletes priorral, ami komolyan veszi a k=19 értéket is és ennek megfelelően valahova 10-re helyezi a k várható értékét a dogmatikus prior diszkreditálja a k=19-et.

## Hipotézisvizsgálat

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
A bayesiánus modell összehasonlítás a következőképpen megy. Kiszámítjuk mindkét esetben a 

<img src="https://render.githubusercontent.com/render/math?math=P(k%3Dk_0%5Cmid%20M_i)">

i=1 és i=2 modellre jellemző **likelihood** értéket. Ezek hányadosa a **Bayes-faktor:**

<img src="https://render.githubusercontent.com/render/math?math=K%3D%5Cdfrac%7BP(k%3Dk_0%5Cmid%20M_1)%7D%7BP(k%3Dk_0%5Cmid%20M_2)%7D">

Az alábbi táblázat (az egyik olyan amiben) a K értékek szerint látható, hogy M<sub>1</sub> mennyire magyarázza a jelenséget a megfigyelt változó ismeretében, mint M<sub>2</sub>.

|K|Az M<sub>1</sub> melletti bizonyíték erőssége|
|---|---|
|... < 1 | Negatív: M<sub>1</sub> elvetendő|
|1 < ... < 10<sup>1/2</sup>=3.16|Anekdotikus (alig érdemes említeni)| 
|10<sup>1/2</sup> < ... < 10|Gyenge|
|10 < ... < 10<sup>3/2</sup>=31.62|Erős|
|10<sup>3/2</sup> < ... < 100|Nagyon erős|
|100 < ... |Döntő|

A konkrét példába, a program lefutását követően, az adatokból: P(17|M<sub>1</sub>)=0.0036 és P(17|M<sub>2</sub>)=0.1007. Innen P(17|M<sub>2</sub>)/P(17|M<sub>1</sub>)

K = 27 > 10

azaz a próba **erősen** M<sub>2</sub>-t részesíti előnyben és az M<sub>1</sub> erősen elvetendő. Tehát az elitgimnáziumi érték nagyon nem vehető olyannak, ami egy véletlenül adódó átlagos gimnáziumi osztály eredménye lehetne.

* A fenti kérdés az absztrakt matematikai jelölések megértésére vonatkozott. Ugyanebben a mérésben a 24 átlagos gimnazista közül 11 mondta, hogy a "hagyományos" matekkal nem volt gondja. A 31 elitgimnazista között ez a szám 19. Mennyire tartható a M<sub>1</sub> (mutatis mutandis)?

Ebben az esetben legyen M<sub>1</sub> priorja beta(30,55), ami a 11/24 várható értéknek felel meg. Az adatok alapján:

P(19|M<sub>2</sub>)/P(19|M<sub>1</sub>) = 4.32

azaz az egyenletes eloszlás még mindig jobban magyaráz, de már csak elég gyengén, a 3.16 < K < 10. Különbség tehát kimutatható, de már messze nem olyan erősen, mint az előbb.
