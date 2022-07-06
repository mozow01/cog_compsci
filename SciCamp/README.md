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
