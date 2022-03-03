# Feltételes valószínűség

## Feltétel, inferálás

Egy 52 lapos francia kártyapakliban annak a valószínűsége, hogy egy kártya kőr (♥) p = 13/52 = 0.25. Keressük annak az X valószínűségi változónak az eloszlását, ami azt mondja meg, hogy ha _visszatevéssel_ kiveszünk a pakliból 3 lapot, akkor hány ebből a kőr, tehát

X := ,,kőrök száma 3 visszatevéses húzásból, francia kártyapakliban''

````javascript
var model = function() {
  var H1 = flip(0.25)
  var H2 = flip(0.25)
  var H3 = flip(0.25)
  var X = H1 + H2 + H3
  return {'X': X}
}
var eloszlás = Enumerate(model)

var binom = Binomial({p: 0.25, n: 3})

viz.auto(binom)
viz.auto(eloszlás)
````

Ha tudunk valamit a szituációból, ez az érték változni fog. Pl.: mi akkor X eloszlása, **ha tudjuk,** hogy az első esetben kőrt húzunk. 

````javascript

var model2 = function() {
  var H1 = flip(0.25)
  var H2 = flip(0.25)
  var H3 = flip(0.25)
  condition( H1 == 1 )
  var X = H1 + H2 + H3
  return {'X': X}
}
var eloszlás2 = Enumerate(model2)

viz.auto(eloszlás2)
````

Ennél sokkal izgalmasabb, ha valaki megmondja a kísérlet eredményét és nekünk kéne megmondanunk, hogy a húzások milyenek voltak. Ezt hívjuk **hipotetikus következtetésnek,** vagy ebben a kontextusban egyszerűen **inferálásnak.**

````javascript
var model3 = function() {
  var H1 = flip(0.25)
  var H2 = flip(0.25)
  var H3 = flip(0.25)
  var X = H1 + H2 + H3
  condition( X == 2 )
  return {'H1' : H1}
}
var eloszlás3 = Enumerate(model3)
viz.auto(eloszlás3)
````

## Rejection sampling (és persze a többi...)

Eddig enumeráció volt, de most érdemes a funkcionális programozásban rejlő rekurziót is kiaknázni a **rejection sampling** módszerrel:

````javascript
var mintavétel = function () {
    var H1 = flip(0.25)
    var H2 = flip(0.25)
    var H3 = flip(0.25)
    var X = H1 + H2 + H3
    return  X == 2 ? {'H1' : H1} : mintavétel()
}
viz(repeat(10, mintavétel))
````

Ha minden igaz, akkor ez ugyanaz, mint a webppl 


````javascript
var model4 = function () {
    var H1 = flip(0.25)
    var H2 = flip(0.25)
    var H3 = flip(0.25)
    var X = H1 + H2 + H3
    condition( X == 2)
    return  {'H1' : H1}
}

var H1_inferálása = Infer({method: 'rejection', samples: 100, model: model4})

viz.auto(H1_inferálása)
````

## Pihenésképp egy érdekesség: "mem" függvény

Tegyük fel, hogy hipotetikus emberek szemszínét szeretnénk generálni véletlenszerűen. 

````javascript
var szemszín = function (ember) {
  return categorical({ps: [0.6, 0.2, 0.2], vs: ['barna','kék','szürke']})
}

var vektor = [szemszín('Pisti'), szemszín('Anna'), szemszín('Cili')]

print(vektor)

viz.auto(vektor)
````

A probléma, hogy ilyenkor ugyanannak az embernek egy másik híváskor más lesz a szemszíne. Ezt a mem-mel, mint egyfajta "feltétellel" kerülhetjük ki.


````javascript
var szemszín2 = mem(function (ember) {
  return categorical({ps: [0.6, 0.2, 0.2], vs: ['barna','kék','szürke']})
})

var vektor2 = [szemszín2('Pisti'), szemszín2('Anna'), szemszín2('Pisti')]

print(vektor2)

viz.auto(vektor2)
````

## Felételes valószínűség

Valószínűségi változók függhetnek egymástól.

**Például **

X = pénzérmével fej vagy írás (boole (1/0) értékű változó (0.5, 0.5) kategorikus eloszással)

Y = királyt húzása (1/0) magyar kártyávól, akkor és csak akkor, ha X = 1, és francia kártyából, ha X = 0.

|      |  X=1   | X=0 |  
| ---  | --- | --- | 
|  Y=1 |  1/8 | 1/13 | 
|  Y=0 | 7/8 | 12/13  | 

**Megjegyzés.** Y ilyenkor csak X rögzítésével lesz "igazi" eloszlás. A mögöttes eloszlás K, azaz a "király húzása" P( Y ) = P( K | X ) és a K és X együttes eloszlása. 

**Matematikailag** leszűkítjük az elemi események terét a feltételre, az A eseményt teljesítő elemi részeseményekre, azaz innentől nem Ω, hanem A az összes elemi események tere:

<img src="https://render.githubusercontent.com/render/math?math=P(B%7CA)%5Coverset%7B%5Cmathrm%7Bdef.%7D%7D%7B%3D%7D%5Cdfrac%7BP(A%5Ccap%20B)%7D%7BP(A)%7D%5Cquad%20%5Cquad%20P(A)%5Cneq%200">

P(B|A) -t úgy mondjuk ki, hogy B valószínűsége feltéve, hogy A (probability of B given A).

````javascript
var model5 = function () {
    var X = flip()
    var Y = flip( X ? 1/8 : 1/13)
    return  {'X,Y': [X , Y]}
}

var Z = Infer({method: 'enumerate', model: model5})

viz(Z)
````

## Monty Hall- (vos Savant-) paradoxon

Adott 3 csukott ajtó mögött egy-egy nyeremény: 1 autó és 1-1 plüsskecske. Monty, a showman megkér minket arra, hogy tippeljük meg, hol az autó (ha eltaláljuk, a miénk lesz). Amikor ez megtörtént, akkor Monty kinyit egy ajtót, éspedig szigorúan azok közül egyet, amelyek mögött egy kecske van és nem mutattunk rá. Majd felteszi újra a kérdést: hol az autó. Érdemes-e megmásítanunk a döntésünket?

🐑 🐑 🏎

🚪 🚪 🚪

🕺 👋

A feladatot a joint eloszlás feltérképezésével oldjuk meg.
|      |     | Y=1 |     |     | Y=2 |     |     | Y=3 |     | P(X) |
| ---  | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
|      | Z=1 | Z=2 | Z=3 | Z=1 | Z=2 | Z=3 | Z=1 | Z=2 | Z=3 |     | 
| X=1  | 0   | 1/18| 1/18| 0   | 0   | 1/9 | 0   | 1/9 |   0 | 1/3 | 
| X=2  | 0   |  0  |1/9  | 1/18   | 0   |  1/18  | 1/9 | 0 |   0 |  1/3| 
| X=3  | 0   | 1/9 |  0 | 1/9   | 0   | 0 | 1/18 | 0  |   1/18 |  1/3 | 
| P(Y) | 0   | 1/3 | 0   | 0   | 1/3 | 0   | 0   | 1/3 |   0 |   1  | 

Először számoljuk ki egy esetben, mi annak a valószínbűsége, hogy ugyanazon ajtó mögött van a nyeremény, ahol az autó van. Pl.: P(X=1 és Y=1) = 1/9. Persze ezt mindhárom esetben ki tudjuk számolni, és az eredmény:

P(X=Y) = 1/3

Ez annak az esélye, hogy elsőre eltaláljuk a kedvező ajtót (ez világos is). Annak a valószínűsége, hogy nem a választottunk mögött van az autó:

P(X=/=Y) = 1 - 1/3 = 2/3

De mivel Monty kinyitja a megmaradó kettő közül azt az ajtót, ami mögött nincs autó és nem is mutattunk rá, ezért utólag behatárolja azt a _két_ ajtót, ami mögött az autó van. Nyilván eredetileg nem bökhettünk volna rá két ajtóra, amelyek persze kétszer annyi valószínűséggel rejtik az autót. De most, hogy ebből a kettőből mutatott Monty egy rossz ajtót, már érvényesíthetjük a P(X=/=Y) = 2/3 valószínűségű nyerést egyetlen ajtóra való rámutatással. Ami persze nem jelenti, hogy ott is lesz az autó, de kétszer akkora eséllyel lesz ott, mint nem. 

Marilyn vos Savant egy szellemes példán mutatta be, hogy miért igaz az, hogy messze jobb váltani. Az érvelése analógiás és a következő. Lefordítunk 1000 kagylót egy parkolóban és az egyik alá rejt Marilyn egy gyöngyöt. Rámutatunk az egyikre azzal, hogy ott van a gyöngy. Találatot ezzel 1/1000 eséllyel érünk el. 

                    ✨

    📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀

Most Marilyn pontosan kettő kivételével az összes kagylót elveszi, éspedig az igaz erre a fel nem fordított kettőre, hogy közte van az is, amire mutattam, és az is, ahol a gyöngy van. 

                    ✨
  
    📀               📀


Érdemes-e váltani? Természetesen, hiszen így 999/1000 az esélye, hogy azalatt van a gyöngy, amire nem szavaztunk. Gyakorlatilag Marilyn megmutatta, hogy hol a gyöngy és 1000-ből 1-szer lesz csak pechünk, amikor is eredetileg jól választottunk.


