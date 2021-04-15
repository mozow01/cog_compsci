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

## Binomiális

````javascript
var dobas = function () {
  var erme1 = flip(0.5);
  var erme2 = flip(0.5);
  var erme3 = flip(0.5);
  var erme4 = flip(0.5);
  var erme5 = flip(0.5);
  return erme1+erme2+erme3+erme4+erme5;
}

var dist = Enumerate(dobas);
var kiserlet = repeat(100, dobas);

//print(dist);
//viz.auto(dist);

print(kiserlet);
viz.hist(kiserlet);
````

````javascript
var b = Binomial({p: 0.4, n: 5})

viz(b)
````

