# Ez itt a második zh

**1.** Az alábbi program azt a szituációt szimulálja, hogy 5 helyre 2 golyót rakunk le. Módosítsd úgy a programot, hogy 6 helyre 3 golyót rakjon le minden lehetséges módon!

a) Ennek segítségével számítsd ki, hogy mi annak a valószínűsége, hogy ha 6 helyre 3 golyót rakunk le, akkor ebből pontosan kettő az első 4 helyen lesz! (A condition-t kell módosítani a "és" &&, "vagy" ||, illetve összegek segítségével, majd arányt számítani.)

b) Számítsd ki mi annak a valószínűsége, hogy ha 6 helyre 3 golyót lerakunk, akkor legalább kettő az első 4 helyen lesz!

````javascript
var model = function () {
  var hely1 = randomInteger(2);
  var hely2 = randomInteger(2);
  var hely3 = randomInteger(2);
  var hely4 = randomInteger(2);
  var hely5 = randomInteger(2);
  condition(hely1+hely2+hely3+hely4+hely5 == 2);
  return [hely1,hely2,hely3,hely4,hely5];
}

var eloszlas = Enumerate(model);

print(eloszlas);

viz.hist(eloszlas);
````

**2.** Francia kárlyapakliból kiválogatjuk a figurásokat (bubi, dáma, király, ász). Ez 16 lap. Kihúzunk visszatevés nélkül belőlük két lapot. Az alábbi program azokat az eseteket sorolja fel, amikor teljesül az A = "az egyik lap nem kőr vagy a másik lap nem király" esemény. Ezt tekintsük úgy, mint egy olyan P(X,Y) joint eloszlást, ahol a lyukas helyekhez tartozó valószínűség nulla, a többihez egyenletes.

````javascript
var kartya = function () {
  var szin1 = randomInteger(4) + 1;
  var figura1 = randomInteger(4) + 1;
  var szin2 = randomInteger(4) + 1;
  var figura2 = randomInteger(4) + 1;
  var huzas1 = [szin1,figura1];
  var huzas2 = [szin2,figura2];
  condition(!(szin1 == szin2 && figura1 == figura2) && 
            (!(szin1 == 1) || !(figura2 == 1)) &&
            (!(szin2 == 1) || !(figura1 == 1))
             );
           
  return [huzas1,huzas2];
  };

var eloszlas = Enumerate(kartya);

print(eloszlas);

viz.auto(eloszlas);
````

a) Rajzoljuk fel a P(X) = P( X = x<sub>i</sub> ) = ∑<sub>j</sub>P( X = x<sub>i</sub> , Y = y<sub>j</sub> ) marginális eloszlást (vigyázat! ez nem lesz ugyanaz, mint a "marginals" paranccsal ad a webppl!).

b) Számoljuk ki, mint a P( X = 1 vagy X = 2 | Y = 1  ) feltételes valószínűséget!

**3.** Egymás utáni három héten kérdeztük meg, hogy a "Nagyra nőjetek" ételszolgáltató kft. rántotthús fogása mennyira ízlett a 30 fős (mikor hiányzott valaki, akkor az óvónénik szavaztak helyettük). A három mérési adat: 25, 26, 28 ovisnak ízlett. 

a) Tervezzünk generatív modellt az óvodások válaszainak modellezésére. Dogmatikus beta priort feltételezzünk, amely kifejezi, hogy igen ínyükre van ez az étel! 

A pillangós példa ez volt:

````javascript
var model = function() {  
  var p = beta(30,90);
  
  observe(Binomial({p : p, n: 20}), 4);

  var poszterior_predikativ = binomial(p,30);

  var prior_p = beta(30,90);

  var prior_predikativ = binomial(prior_p,20);

return {
       Prior: prior_p, 
       Posterior : p,
       PredictivePrior : prior_predikativ,
       PredictivePosterior : poszterior_predikativ};
};

var output = Infer({model: model, samples: 10000, method: 'MCMC'});

viz.marginals(output);
````

b) Határozzuk meg próbálgatással a posterior eloszlás várható értékét és a kredibilitási intervallumot 95%-ra!

Ez a pillangós példánál ez volt:

````javascript
var model = function() {  
  var p = beta(30,90);
  observe(Binomial({p : p, n: 20}), 4);

  var poszterior_predikativ = binomial(p,20);

  var prior_p = beta(30,90);

  var prior_predikativ = binomial(prior_p,20);

return p;
};

var output = Infer({model: model, samples: 10000, method: 'MCMC'});
viz.auto(output);
print(expectation(output));
expectation(output,function(p){0.1<p && p<0.31})
````
**4.** A szomszédos óvodába minden második nap a krisnások hordják az ételt, ezért az ottani gyerekek ismerik a tradicionális magyar konyhától eltérő ízvilágot is. A mérés szerint ott 30 óvodásból 8 szerette csak a "Nagyra nőjetek" rántotthúsát. Az M modell legyen az előző feladatbeli, az N modell pedig az, hogy akármilyen uniform p értékkel döntenek a szomszédos óvoda óvodásai. Mennyire (milyen szinten) magyarázza a megfigyelt értéket az N az M-hez képest (vagy fordítva)? Végezzünk Bayes-faktoros elemzést! (Erről lásd az negyedik valszám órát.)
