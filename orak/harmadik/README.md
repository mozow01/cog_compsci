# Bayesiánus adatelemzés (Bayesian Data Analysis)

## Ismétlés, összefoglalás

A klasszikus valószínűségszámítási példákat jellemzően a rendezett n-esek feletti valószínűségi mezőben oldottuk meg. Ezek nem mások, mint a többváltozós eloszlások (joint distributions). Ezeket az eloszlásokat    algorimikusan is generáltuk, kétféleképpen: kimerítésses módszerrel (_enumeration_), amikoer minden esetet végiszámol az algoritmus és mintavétellel, amikor egy elég nagy mintából származtatjuk az adatokat (_sampling_). Két fő példa, a kockák és a kártyák:

**1.** Mi annak a valószínűsége, hogy három kockával dobva, az eredményben van hatos. Mi annak a valószínűsége, hogy az első kocka hatos feltéve, hogy a van benne (egyáltalán) hatos.

````javascript
var kocka_modell = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  var kocka3 = randomInteger(6) + 1;
  condition(kocka1 == 6 || (kocka2 == 6 || kocka3 === 6))
  return [kocka1,kocka2,kocka3];
}

var eloszlas = Enumerate(kocka_modell);

// Ugyenez valószínűségi következtetéssel is kiszámítható
// var következtetés = Infer({method: 'enumerate'}, kocka_modell);
// Ugyanez minételezéssel: 
// var következtetés = Infer({method: 'enumerate'}, kocka_modell);
//print(következtetés);
//viz.hist(következtetés);

print(eloszlas);
viz.hist(eloszlas);
````
Ellenőrzés, komplementer valószínűséggel: P(van hatos) = 1 - (5^3)/(6^3) ~ 0.4213 (= 91/216 ).

Ezzel gyakorlatilag a feltételes valószínűséget is megértettük. Pl.:

Mi annak a valószínűsége, hogy az első kocka hatos feltéve, hogy a van benne (egyáltalán) hatos: 1/91. Azaz leszűkítjük az elemi események terét a feltételre:

<img src="https://render.githubusercontent.com/render/math?math=P(B%7CA)%20%5Coverset%7B%5Cmathrm%7Bdef.%7D%7D%7B%3D%7D%20%5Cdfrac%7BP(B)%7D%7BP(A)%7D%2C%20%5Cquad%5Cquad%20P(A)%5Cneq%200">

(P(B|A) -t úgy mondjuk ki, hogy B valószínűsége feltéve, hogy A (probability of B given A)).






