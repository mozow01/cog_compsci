## Kolmogorov-axiómák

(Ω, Σ, P) valószínűségi mező, ha Ω nemüres (elemi események tere), Σ egy σ-algebra (eseménytér), és
1. P : Σ → ℝ, P(A) ≧ 0
2. P(Ω)=1, P(∅)=0
3. ha A, B ∊ Σ egymást _kizáró események_ (A ⋂ B = ∅), akkor P(A ⋃ B) = P(A) + P(B).

További fontos szabályok:

4. _Komplementer-szabály_: P(comp(A)) = 1-P(A)

5. Független események: P(A ⋂ B) = P(A) ⋅ P(B)

6. Logikai szita:  P(A+B) = P(A) + P(B) - P(AB). (néha unió: +, metszet: ⋅)

Eloszlásnak azt nevezzük, ahogy megadjuk P-t. Ez sokféle lehet, de pl. véges esetben az elemi események felsorolása, végtelen esetben sűrűsűg vagy kumulatív eloszlásfüggvénnyel (később).

## Kártyák

**1.** Egy 52 lapos francia kártyapakliból kihúzunk 2 lapot. Mi annak a valószínűsége, hogy a kőr király van a kihúzott lapok között van?

_Rendezetlen modell._ Összes eset: <img src="https://render.githubusercontent.com/render/math?math=52%20%5Cchoose%202">

Kedvező esetek: nincsenek a helyek (húzások) megülönböztetve. De szorzás, mert függetlenül választunk az egyetlen kőr király és a többi közül: 
<img src="https://render.githubusercontent.com/render/math?math=%7B1%20%5Cchoose%201%7D%7B51%20%5Cchoose%201%7D">

Tehát: <img src="https://render.githubusercontent.com/render/math?math=P%3D%5Cdfrac%7B%7B1%20%5Cchoose%201%7D%7B51%20%5Cchoose%201%7D%7D%7B%7B52%20%5Cchoose%202%7D%7D">

_Rendezett modell._ Ebben az esetben párokhoz rendelünk valószínűséget, ezt **joint** vagy **többváltozós eloszlásnak** nevezzük. Z = (X,Y), ahol X, Y a két külön kártya eloszlása, amik azonban vészesen _összefüggenek_, mert nem tesszük vissza a kártyát.

|   P(X,Y)   | Y=1 | 2 | ... | 52 | marginális P(X) |
| --- | --- | --- | --- | --- | --- | 
| X= 1           | 0 | 1/(51⋅52) | ... | 1/(51⋅52) | 1/52 |
| 2           | 1/(51⋅52) | 0 | ... | 1/(51⋅52) | 1/52 |
| 3           | 1/(51⋅52) | 1/(51⋅52) | ... | 1/(51⋅52) | 1/52 |
|             | 1/(51⋅52) | 1/(51⋅52) | ... | 1/(51⋅52) | 1/52 |
| 52         | 1/(51⋅52) | 1/(51⋅52) | ... | 1/(51⋅52) | 1/52 |
| marginális P(Y)   |  1/52 |  1/52 | 1/52 | 1/52 |  1  |

Innen a kedvező eset: (X,Y) = (♥K,_ ) vagy (_ ,♥K), így a valószínűség: 1/52 + 1/52 = 1/26. 

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

