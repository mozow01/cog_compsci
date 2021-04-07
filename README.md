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

2. Ugyanez, de végezzünk random kísérleteket. Mi az adott kimenetelek mintabeli gyakorisága?

````javascript
var dobas = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  var kocka3 = randomInteger(6) + 1;
  return [kocka1,kocka2];
}

var eloszlas = Enumerate(dobas);
//print(eloszlas);viz.auto(eloszlas);

viz.hist(repeat(1000,dobas));
````
