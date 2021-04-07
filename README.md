# cog_compsci
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
