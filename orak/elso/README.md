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
## Szigma algebra

Ha X halmaz és _P_(X) az összes részhalmazainak halmaza, akkor a Σ ⊆ _P_(X) halmazcsalád σ-algebra, ha

1. X ∈ Σ (biztos esemény)
2. Σ zárt a komplementerre: ha A ∈ Σ, akkor X \ A ∈ Σ is
3. Σ zárt a megszámlálható unióra: ha  A1, A2, A3, ... ∈ Σ, akkor A1 ∪ A2 ∪ A3 ∪ … ∈ Σ.

Ezekből következik, hogy ∅ is a Σ eleme és a (megszámlálható) metszet is a Σ eleme. (De Morgan azonosságok.) Tehát Σ egyben Boole-halmazalgebra is.

## Házi feladatok

1. Írjuk át a SimpleModel-t úgy, hogy az konklúzióban a tanár/könyvtáros kategóriák is megjelennek és egy determiniszikus, matekos logikával gondolkodó ágenst modellezzen.
2. Írjuk át a SimpleModel-t úgy, hogy hihető valószínűségekkel ( flip(0...) ) egy olyan természetes ágens probalilisztikus videlkedését modellezze, aki a) néha összetéveszti a cserfest a csendessel, b) a tanárt valamilyen nagy valószínűséggel cserfesenk tételezi fel.
3. Írjuk olyan programot, ami kiszámítja, hogy ha két kockával dobunk, akkor mi annak a valószínűsége, hogy a) a dobott számok között van prímszám (vili, hogy itt csak a 2, 3, 5) ill. b) a dobott számok összege kisebb, mint 6.
4. * Írjunk programot, ami  kiszámítja, hogy ha 3 kockával dobunk, akkor mi annak a valószínűsége, hogy pontosan 2 kocka hatos.
5. * Igazoljuk, hogy  B ∩ (A1 ∪ A2 ∪ A3 ∪ … ) = (B ∩ A1) ∪ (B ∩ A2) ∪ (B ∩ A3) ∪ …
