## Golyók

1. Lerakunk 5 helyre 2 golyót, úgy, hogy egy helyen, csak 1 golyó lehet egyszerre. Adjuk meg az elemi eseménytér két modelljét és mondjuk meg, hogy mennyi annak az eseménynek a valószínűsége, hogy a) mindkét golyó az első három hely valamelyikén van, b) valamelyik golyó az utolsó 2 hely valamelyikén van.

````javascript
var lerakas1= function () {
  var golyo1_helye = randomInteger(5) + 1;
  var golyo2_helye = randomInteger(5) + 1;
  condition(golyo1_helye !== golyo2_helye);
  return [golyo1_helye,golyo2_helye];
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

var eloszlas_1 = Enumerate(lerakas1)

var eloszlas_2 = Enumerate(lerakas2);

//var inf = Infer({method: 'enumerate'}, dobas);
//print(inf);
//viz.hist(inf);

viz.hist(eloszlas_1);
viz.hist(eloszlas_2)
````


## Kolmogorov-axiómák

(Ω, Σ, P) valószínűségi mező, ha Ω nemüres (elemi események tere), Σ egy σ-algebra (eseménytér), és
1. P : Σ → ℝ, P(A) ≧ 0
2. P(Ω)=1, P(∅)=0
3. ha A, B ∊ Σ egymást _kizáró események_ (A ⋂ B = ∅), akkor P(A ⋃ B) = P(A) + P(B).

További fontos szabályok:

4. _Komplementer-szabály_: P(comp(A)) = 1-P(A)

5. Független események: P(A ⋂ B) = P(A) ⋅ P(B)

6. Logikai szita:  P(A+B) = P(A) + P(B) - P(AB). (néha unió: +, metszet: ⋅)

Eloszlásnak azt nevezzük, ahogy megadjuk P-t. Ez sokféle lehet, de pl. véges esetben az elemi események felsorolása, végtelen esetben sűrűség vagy kumulatív eloszlásfüggvénnyel (később).

## Kártyák

**1.** Egy 52 lapos francia kártyapakliból kihúzunk 2 lapot. Mi annak a valószínűsége, hogy a kőr király van a kihúzott lapok között van?

_Rendezetlen modell._ Összes eset: <img src="https://render.githubusercontent.com/render/math?math=52%20%5Cchoose%202">

Kedvező esetek: nincsenek a helyek (húzások) megülönböztetve. De szorzás, mert függetlenül választunk az egyetlen kőr király és a többi közül: 
<img src="https://render.githubusercontent.com/render/math?math=%7B1%20%5Cchoose%201%7D%7B51%20%5Cchoose%201%7D">

Tehát: <img src="https://render.githubusercontent.com/render/math?math=P%3D%5Cdfrac%7B%7B1%20%5Cchoose%201%7D%7B51%20%5Cchoose%201%7D%7D%7B%7B52%20%5Cchoose%202%7D%7D">

_Rendezett modell._ Ebben az esetben párokhoz rendelünk valószínűséget, ezt **joint** vagy **többváltozós eloszlásnak** nevezzük. Z = (X,Y), ahol X, Y a két külön kártya tere, amik feletti eloszlás azonban vészesen _összefügg_, mert nem tesszük vissza a kártyát.

|   P(X,Y)   | Y=1 | 2 | ... | 52 | marginális P(X) |
| --- | --- | --- | --- | --- | --- | 
| X= 1           | 0 | 1/(51⋅52) | ... | 1/(51⋅52) | 1/52 |
| 2           | 1/(51⋅52) | 0 | ... | 1/(51⋅52) | 1/52 |
| 3           | 1/(51⋅52) | 1/(51⋅52) | ... | 1/(51⋅52) | 1/52 |
|    ...      | 1/(51⋅52) | 1/(51⋅52) | ... | 1/(51⋅52) | 1/52 |
| 52         | 1/(51⋅52) | 1/(51⋅52) | ... | 0 | 1/52 |
| marginális P(Y)   |  1/52 |  1/52 | 1/52 | 1/52 |  1  |

Megjegyzés: Itt az eloszlás (joint): 

<img src="https://render.githubusercontent.com/render/math?math=P(X%2CY)%3D%20P(x_i%2Cy_j)">

Egyik marginális eloszlás: 

<img src="https://render.githubusercontent.com/render/math?math=P(X)%3D%5Csum_Y%20P(X%2CY)%3DP(y_j)%3D%5Csum_%7Bj%3D1%7D%5E%7Bm%7D%20P(x_i%2Cy_j)">

Másik marginális eloszlás: 

<img src="https://render.githubusercontent.com/render/math?math=P(Y)%3D%5Csum_X%20P(X%2CY)%3DP(y_j)%3D%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%20P(x_i%2Cy_j)">

És persze a szumma-szummárum = 1:

<img src="https://render.githubusercontent.com/render/math?math=%5Csum_X%20%5Csum_Y%20P(X%2CY)%3D%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%5Csum_%7Bj%3D1%7D%5E%7Bm%7D%20P(x_i%2Cy_j)%3D1">

A kedvező esetek: (X,Y) = (♥K,_ ) vagy (_ ,♥K), így a valószínűség: 1/52 + 1/52 = 1/26. 

Ugyanez webppl-lel:

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
**2.** Hipergeometrikus eloszlás. 52 lapos francia kártyából húzunk 5 lapot. Mi annak a valószínűsége, hogy a) lesz benne pontosan egy treff, b) legalább egy király?

**3.** Egy iskolában 1000 gyerek tanul és a kompetenciamérésen mindig ugyanaz a 10 gyerek lesz rosszul. A termekbe sorsolják a diákokat. Mi annak a valószínűsége, hogy a Földszint VI-ban kompetenciázó 20 diákból legfeljebb három rosál be (ezek közül)?

**3.** Sokéves átlagban p = 0.02 valószínűséggel lesz egy érettségiző az írásbelin rosszul. Mi annak a valószínűsége, hogy a Soroksári Wass Albert Két-tannyelvű Gimnázium 30 fős 12. C. osztályában legfeljebb 1 ember lesz rosszul.

(Cinkelt és cinkeletlen érmék.)

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

Adott egy n elemű sokaság és az, hogy ebből egy tetszőleges elem p valószínűséggel teljesít valamilyen _A_ tulajdonságot. Annak a valószínűsége, hogy ebből az n elemből pontosan k db teljesíti az _A_ tulajdonságot, 

<img src="https://render.githubusercontent.com/render/math?math=P(X%3Dk)%3D%7Bn%20%5Cchoose%20k%7D%20p%5Ek(1-p)%5E%7Bn-k%7D%20">

ahol az X valószínűségi változó nem más, mint ,,azon elemek száma, amik teljesítik az _A_ tulajdonságot''.

A 3. példa adataival tehát: _A_ = "rosszul lesz", p=0.02, n = 30. A keresett valószínűségű esemény, hogy 0 vagy 1 érettségiző lesz rosszul a 30 főből, azaz ,,X = 0 vagy X=1'' ezek kizárják egymást, ezért a valószínűség az összeg: 

<img src="https://render.githubusercontent.com/render/math?math=P(%2C%2CX%3D0%5C%3Bor%5C%3BX%3D1'')%3DP(X%3D0)%2BP(X%3D1)%3D%7B30%20%5Cchoose%200%7D%20p%5E0(1-p)%5E%7B30-0%7D%2B%7B30%20%5Cchoose%201%7D%20p%5E1(1-p)%5E%7B30-1%7D%3D%20(1-p)%5E%7B30%7D%2B30%20p(1-p)%5E%7B29%7D">

Ez pedig **0.879454,** tehát elég valószínű, hogy legfeljebb csak 1 gyerek betegszik meg. A komplementer esemény valószínűsége, azaz, hogy legalább 2 gyerek beteg lesz, pici, de nem elhanyagolható: 1 - 0.879454 = 0.120546.

