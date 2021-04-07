# cog_compsci

[http://webppl.org/](http://webppl.org/)

## Kockák

1. Három (kettő, négy) kockával dobunk. Mi az elemi események tere? Mi az _eloszlás?_

````javascript
var dobas = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  var kocka3 = randomInteger(6) + 1;
  return [kocka1,kocka2,kocka3];
}

var eloszlas = Enumerate(dobas);
print(eloszlas);
viz.auto(eloszlas);
````

2. Ugyanez, de végezzünk random kísérleteket. Mi az adott kimenetelek **mintabeli gyakorisága?**

````javascript
var dobas = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  var kocka3 = randomInteger(6) + 1;
  return [kocka1,kocka2];
}

var eloszlas = Enumerate(dobas);
//print(eloszlas);viz.auto(eloszlas);

var minta = repeat(10000,dobas);

viz.hist(minta);
````

3. Számítsuk ki, hogy mi egy konkrét feladat valószínűsége azzal, hogy ismerjük az elemi esemény sokaságbeli gyakoriságát és a feltételes elemi események gyakoriságát!

````javascript
var dobas = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  var kocka3 = randomInteger(6) + 1;
  return [kocka1,kocka2,kocka3];
}

var kedvezo_dobas = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  var kocka3 = randomInteger(6) + 1;
  condition(kocka1 == 6 && kocka2 == 6)
  return [kocka1,kocka2,kocka3];
}

var eloszlas = Enumerate(kedvezo_dobas);

//var inf = Infer({method: 'enumerate'}, dobas);
//print(inf);
//viz.hist(inf);

print(eloszlas);

viz.hist(eloszlas);
````
