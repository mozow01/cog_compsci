# cog_compsci

[http://webppl.org/](http://webppl.org/)

[https://probmods.org/](https://probmods.org/) (MIT)

[https://www.cs.cornell.edu/courses/cs4110/2016fa/lectures/lecture33.html](https://www.cs.cornell.edu/courses/cs4110/2016fa/lectures/lecture33.html) (Cornell)

[https://github.com/tobiasgerstenberg/webppl_tutorial](https://github.com/tobiasgerstenberg/webppl_tutorial) (Stanford)

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
## Golyók

4. Lerakunk 5 helyre 2 golyót, úgy, hogy egy helyen, csak 1 golyó lehet egyszerre. Adjuk meg az elemi eseménytér két modelljét és mondjuk meg, hogy mennyi annak az eseménynek a valószínűsége, hogy a) mindkét golyó az első három hely valamelyikén van, b) valamelyik golyó az utolsó 2 hely valamelyikén van.

````javascript
var lerakas = function () {
  var golyo1 = randomInteger(5) + 1;
  var golyo2 = randomInteger(5) + 1;
  condition(golyo1 !== golyo2);
  return [golyo1,golyo2];
}

var lerakas2 = function () {
  var hely1 = randomInteger(2);
  var hely2 = randomInteger(2);
  var hely3 = randomInteger(2);
  var hely4 = randomInteger(2);
  var hely5 = randomInteger(2);
  condition(hely1+hely2+hely3+hely4+hely5 == 2);
  return [hely1,hely2,hely3,hely4,hely5];
}

var eloszlas = Enumerate(lerakas2);

//var inf = Infer({method: 'enumerate'}, dobas);
//print(inf);
//viz.hist(inf);

print(eloszlas);

viz.hist(eloszlas);
````

## Kártyák

5. Egy 52 lapos francia kártyapakliból kihúzunk 2 lapot. Mi annak a valószínűsége, hogy a kőr király van a kihúzott lapok között van?

Rendezett modellt nézzünk, határozzuk meg az elemi események terét és a kedvezőt, de utána vizsgáljuk meg a rendezetlen esetben is a számítást.

````javascript
var kartya = function () {
  var szin1 = randomInteger(4) + 1;
  var figura1 = randomInteger(13) + 1;
  var szin2 = randomInteger(4) + 1;
  var figura2 = randomInteger(13) + 1;
  var huzas1 = [szin1,figura1];
  var huzas2 = [szin2,figura2];
  condition(szin1 !== szin2 || figura1 !== figura2);
  return [huzas1,huzas2];
  }

var kedvezo_kartya = function () {
  var szin1 = randomInteger(4) + 1;
  var figura1 = randomInteger(13) + 1;
  var szin2 = randomInteger(4) + 1;
  var figura2 = randomInteger(13) + 1;
  var huzas1 = [szin1,figura1];
  var huzas2 = [szin2,figura2];
  condition((szin1 !== szin2 || figura1 !== figura2) &&
            ((figura1 == 13 && szin1 == 1 ) || (figura2 == 13 && szin2 == 1 )
              ) 
            );
  return [huzas1,huzas2];
}
  
var eloszlas = Enumerate(kedvezo_kartya);

print(eloszlas);

viz.hist(eloszlas);
````
