## Ízelítő

````javascript
var feature1 = ['könyvtáros','tanár'];
var feature2 = ['csendes','cserfes'];
var operator = [' és ',' vagy '];

var SimpleModel = function() {
    var word1 = uniformDraw(feature1)
    var op = uniformDraw(operator)
  
    var word2  = uniformDraw(feature2)
    print('Premissza: Anna ' + word1 + op + word2 + '.'); 
    var word3 = uniformDraw(feature2)
    print('Konklúzió: Anna ' + word3 + '.'); 
    var ervenyes = (op == ' és ')
                   ? ((word2 == word3) ? 'érvényes' : 'nem érvényes') 
                   : 'nem érvényes'
    print(ervenyes); 
    return ervenyes
}
 
var output = 
  Infer({model: SimpleModel, method:'rejection', samples: 1})
````

## Kockák

1. Három (kettő, négy) kockával dobunk. Mi az elemi események tere? Mi az _elméleti eloszlás?_ Ugyanez, de végezzünk random kísérleteket. Mi az adott kimenetelek **mintabeli gyakorisága?**


````javascript
var dobas = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  var kocka3 = randomInteger(6) + 1;
  return [kocka1,kocka2,kocka3];
}

var elméleti = Enumerate(dobas);
//print(eloszlas);viz.auto(eloszlas);

var mintavétel = repeat(100000,dobas);

viz.hist(elméleti);

viz.hist(mintavétel);
````

2. Számítsuk ki, hogy mi egy konkrét feladat valószínűsége azzal, hogy ismerjük az elemi esemény sokaságbeli gyakoriságát és a feltételes elemi események gyakoriságát! a)

````javascript
var dobas = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  return [kocka1,kocka2];
}

var kedvezo_dobas = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  condition((kocka1 == 6 || kocka2 == 6))
  return [kocka1,kocka2];
}

var osszes = Infer({method: 'enumerate'}, dobas);

var kedvezo = Infer({method: 'enumerate'}, kedvezo_dobas);

print(osszes);
viz.hist(osszes);

print(kedvezo);
viz.hist(kedvezo);

print("p = kedvező/összes = " 
      + Math.exp((osszes.score)([6,6]))/Math.exp((kedvezo.score)([6,6])));

````

2. b)

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

3. Lerakunk 5 helyre 2 golyót, úgy, hogy egy helyen, csak 1 golyó lehet egyszerre. Adjuk meg az elemi eseménytér két modelljét és mondjuk meg, hogy mennyi annak az eseménynek a valószínűsége, hogy a) mindkét golyó az első három hely valamelyikén van, b) valamelyik golyó az utolsó 2 hely valamelyikén van.

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
