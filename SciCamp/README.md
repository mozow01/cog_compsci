# Valszám problémamegoldás fájdalommentesen

## webppl 

Noah Goodman and Andreas Stuhlmüller (Stanford)

http://webppl.org/ egy valószínűségi programnyelv, ami a JavaScript nyelvbe van beágyazva és pszicholgiai és természetes nyelvi jelenségek modellezésére és fejlesztették. Nagyon sok valószínűségi eloszlást és mintavételezési eljárást tartalmaz és kellően variábilisan és felhasználóbarát módon lehet vele  valószínűségi modelleket írni. 

## Kombinatorikai, valószínűségi kísérletek modellezése

### Kockadobás

<img src="https://github.com/mozow01/cog_compsci/blob/main/SciCamp/1200px-2-Dice-Icon.svg.png" width=100>

````javascript
// Két kocka

var dobás = function () {
  var kocka1 = categorical({ps: [1/6, 1/6, 1/6, 1/6, 1/6, 1/6], 
                            vs: [1, 2, 3, 4, 5, 6]});
  var kocka2 = categorical({ps: [1/6, 1/6, 1/6, 1/6, 1/6, 1/6], 
                            vs: [1, 2, 3, 4, 5, 6]});
  return  { kockapár: [kocka1,kocka2] };
}

// Egy dobás, a dobás() fv. egy futtatása.
print(dobás());

// Sok dobás és gyakorisági táblázata

var sokdobás = repeat(10, dobás); 

viz.auto(sokdobás);

// A [kocka1,kocka2] változó "elméleti" (egzakt) eloszlása az esetek felsorolásával.

var output = Enumerate(dobás);

// A kimenet eloszlásának generálása felsorolással
// var output = Infer({method: 'enumerate', model: dobás});

// Közelítő eloszlás samples db-szori lefuttatással és abból histogram építésével 
// var output = Infer({method: 'forward', samples: 10000, model: dobás});

print("1/36 = "+1/36);
print("p( kocka1=6 és kocka2=6) = "+Math.exp((output.score)([6,6])));

viz.auto(output);
// viz.hist(output);
// viz.table(output);
````

### Kockadobás kedvező esetekkel

<img src="https://github.com/mozow01/cog_compsci/blob/main/SciCamp/2381778-200.png" width=100>

Mi annak a valószínűsége, hogy két kockával dobva legalább az egyik hatos?

````javascript
var dobás = function () {
  var kocka1 = categorical({ps: [1/6, 1/6, 1/6, 1/6, 1/6, 1/6], 
                            vs: [1, 2, 3, 4, 5, 6]});
  var kocka2 = categorical({ps: [1/6, 1/6, 1/6, 1/6, 1/6, 1/6], 
                            vs: [1, 2, 3, 4, 5, 6]});
  return [kocka1,kocka2];
}

var kedvező_dobás = function () {
  var kocka1 = categorical({ps: [1/6, 1/6, 1/6, 1/6, 1/6, 1/6], 
                            vs: [1, 2, 3, 4, 5, 6]});
  var kocka2 = categorical({ps: [1/6, 1/6, 1/6, 1/6, 1/6, 1/6], 
                            vs: [1, 2, 3, 4, 5, 6]});
  condition((kocka1 == 6 || kocka2 == 6));
  
  return  [kocka1,kocka2];
}

var összes = Infer({method: 'enumerate'}, dobás);

var kedvező = Infer({method: 'enumerate'}, kedvező_dobás);

viz.auto(összes);

viz.auto(kedvező);

print("11/36 = " 
      + 11/36);

print("p = kedvező/összes = " 
      + Math.exp((összes.score)([6,6]))/Math.exp((kedvező.score)([6,6])));
````
### Kártyahúzás visszatevéssel (binomiális eloszlás)

<img src="https://github.com/mozow01/cog_compsci/blob/main/SciCamp/png-transparent-blackjack-texas-hold-em-three-card-poker-playing-card-card-miscellaneous-game-angle-thumbnail.png" width=100>

Egy 52 lapos francia kártyapakliban annak a valószínűsége, hogy egy kártya kőr (♥): p = 13/52 = 0.25. Keressük annak az X valószínűségi változónak az eloszlását, ami azt mondja meg, hogy ha _visszatevéssel_ kiveszünk a pakliból 3 lapot, akkor hány ebből a kőr, tehát

X := ,,kőrök száma 3 visszatevéses húzásból, francia kártyapakliban''

````javascript
var model = function() {
  var H1 = flip(0.25)
  var H2 = flip(0.25)
  var H3 = flip(0.25)
  var X = H1 + H2 + H3
  return {'X': X}
}
var eloszlás = Enumerate(model)

var binom = Binomial({p: 0.25, n: 3})

viz.auto(binom)
viz.auto(eloszlás)
````

Ha tudunk valamit a szituációból, ez az érték változni fog. Pl.: mi akkor X eloszlása, **ha tudjuk,** hogy az első esetben kőrt húzunk. 

````javascript

var model2 = function() {
  var H1 = flip(0.25)
  var H2 = flip(0.25)
  var H3 = flip(0.25)
  condition( H1 == 1 )
  var X = H1 + H2 + H3
  return {'X': X}
}
var eloszlás2 = Enumerate(model2)

viz.auto(eloszlás2)
````

## Valószínűségi (induktív) következtetés, Bayes-inferencia

<img src="https://github.com/mozow01/cog_compsci/blob/main/SciCamp/Screenshot from 2022-07-06 23-01-09.png" width=100>

Ennél sokkal izgalmasabb, ha valaki megmondja a kísérlet eredményét (a feltétel lényeges) és nekünk kéne megmondanunk, hogy a húzások milyenek voltak. Ezt hívjuk **induktív következtetésnek,** **Bayes-inferenciának,** vagy ebben a kontextusban egyszerűen **inferálásnak.**

````javascript
var model3 = function() {
  var H1 = flip(0.25)
  var H2 = flip(0.25)
  var H3 = flip(0.25)
  var X = H1 + H2 + H3
  condition( X == 2 )
  return {'H1' : H1}
}
var eloszlás3 = Enumerate(model3)
viz.auto(eloszlás3)
````
````javascript
var model = function () {
    var eső = flip(1/5)
    var dugó = (eső == true) ? flip(1/2) : flip(1/4)
    var késés = (dugó == true) ?  flip(0.9) : flip(.05)
    condition(késés == 1)
    return  {eső: eső, dugó: dugó, késés: késés}
}

var X = Infer({method: 'SMC', model: model})

viz.marginals(X)
````

