# Bayesiánus adatelemzés (Bayesian Data Analysis)

## Ismétlés, összefoglalás

A klasszikus valószínűségszámítási példákat jellemzően a rendezett n-esek feletti valószínűségi mezőben oldottuk meg. Ezek nem mások, mint a többváltozós eloszlások (joint distributions). Ezeket az eloszlásokat    algorimikusan is generáltuk, kétféleképpen: kimerítésses módszerrel (_enumeration_), amikoer minden esetet végiszámol az algoritmus és mintavétellel, amikor egy elég nagy mintából származtatjuk az adatokat (_sampling_). Két fő példa, a kockák és a kártyák voltak. Most először a **feltételes valószínűséget** és a **binomiális eloszlást** elevenítjük fel.

### A feltételes valószínűség

Ekkor leszűkítjük az elemi események terét a feltételre az A eseményt teljesítő elemi részeseményekre, azaz innentől nem omega, hanem A az összes elemi események tere:

<img src="https://render.githubusercontent.com/render/math?math=P(B%7CA)%5Coverset%7B%5Cmathrm%7Bdef.%7D%7D%7B%3D%7D%5Cdfrac%7BP(A%5Ccap%20B)%7D%7BP(A)%7D%5Cquad%20%5Cquad%20P(A)%5Cneq%200">

(P(B|A) -t úgy mondjuk ki, hogy B valószínűsége feltéve, hogy A (probability of B given A)).

**1.** Mi annak a valószínűsége, hogy három kockával dobva, az eredményben van hatos. Mi annak a valószínűsége, hogy az első kocka hatos feltéve, hogy van benne (egyáltalán) hatos.

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

Mi annak a valószínűsége, hogy az első kocka hatos feltéve, hogy van benne (egyáltalán) hatos? 

P(az első hatos|van adobások köztött hatos) = 1/91 vagy ((1/216) / (1/91)).

A feltételes valószínűség sokszor olyan intuitív, hogy azonnal ennek az értékét tudjuk, sőt, vannak olyan tárgyalások is (Rényi), amelyekben nem a feltételes valószínűség az alapfogalom. Éppen ezért a definíciót néha így írják és **szorzattörvénynek** nevezik (a "törvény" szón persze a matematikusok nevetnek... :)

<img src="https://render.githubusercontent.com/render/math?math=P(A%5Ccap%20B)%3DP(B%7CA)%5Ccdot%20P(A)%5Cquad%20%5Cquad%20%5Cmathrm%7Bill.%7D%20%5Cquad%20%5Cquad%20P(B%5Ccap%20A)%3DP(A%7CB)%5Ccdot%20P(B)">

### Feltételes eloszlás joint eloszlás esetén

Joint esetén, szokásos módon inkább egy új P( Y | X ) joint eloszlást definiálnak és csak ezt hívják feltételes valószínűségnek. A kiszámítási formulája:

<img src="https://render.githubusercontent.com/render/math?math=P(Y%3Dy_j%7CX%3Dx_i)%20%3D%20%5Cdfrac%7BP(Y%3Dy_j%5Cwedge%20X%3Dx_i)%7D%7BP(X%3Dx_i)%7D">

Ami a fenti egy speciális esete akkor, amikor a P(X,Y) joint eloszlásban olyan feltételt fogalmazunk meg, ami azt mondja, hogy az egyik változó rögzített értékű. A joint előző órai jelölésével, mivel <img src="https://render.githubusercontent.com/render/math?math=P(x_i%2Cy_j%5C%3B)%3DP(X%3Dx_i%5Cwedge%20Y%3Dy_j)">, ezért ezt még így is írhatjuk:

<img src="https://render.githubusercontent.com/render/math?math=P(Y%3Dy_j%5C%3B%7C%5C%3BX%3Dx_i)%20%3D%20%5Cdfrac%7BP(x_i%2Cy_j%5C%3B)%7D%7BP(X%3Dx_i)%7D">

ill. a menőző (borzasztó, túl kompakt) jelöléssel:

<img src="https://render.githubusercontent.com/render/math?math=P(Y%7CX)%20%3D%20%5Cdfrac%7BP(X%2CY%5C%3B)%7D%7BP(X)%7D">

hiszen <img src="https://render.githubusercontent.com/render/math?math=P(X)%3DP(X%3Dx_i)%20%3D%5Csum_j%20P(x_i%2Cy_j)"> a **marginális eloszlás.**

**2.** Ellenőrizzük az előző feladatban, hogy a marginális eloszlások tényleg azok, amiket a ````viz.marginals(eloszlas)```` parancs ad ki. Világos, hogy a "gép" szerint az (X,Y) joint tér az hogy X = "az első kockával x_i-t dobunk", Y ="a második kockával y_j-t dobunk" és a P(X,Y) az az **eloszlás**, ami nulla, ha X és Y nem hatos, és egyenletes, ha van benne hatos. 

### Binomiális eloszlás, selejtformula

Amikor a kártyapakliba nem tettük vissza kihúzott kártyalapokat, akkor visszatevés nélküli mintavételt alkalmaztunk. Lásd: _mi annak a valószínűsége, hogy 5 kártyából legalább az egyik treff!_

Ha a populáció óriási és a minta pici, pl. egy egész ország népessége, kontra 20 fő, akkor a mintavételt tekinthetjük akár visszatevésesnek is, mert a 20 ember kiválasztásának hatása az arányokra nézve olyan, mint halottnak a csók. A visszavetés nélküli mintavételezés selejtformulája a binomiális eloszlás formulája.

Tegyük fel, hogy egy bizonyos A tulajdonság (pl.: a Brexitre szavazott-e/nem-e), az adott populációban p valszínűséggel teljesül egy emberre. Ekkor annak a valószínűsége, hogy n kiválasztott személyből pontosan **k** rendelkezik az A tulajdonsággal, 

<img src="https://render.githubusercontent.com/render/math?math=P(X%3Dk)%3D%7Bn%20%5Cchoose%20k%7D%5Ccdot%20p%5Ek%5Ccdot%20(1-p)%5E%7Bn-k%7D">

**3.** Számítsuk ki, hogy mi annak a valószínűsége, hogy 3 emberből legalább 1 bliccel a BKV-n, ha a magyarok jegyvásárlási hajlandósága 65%.

````javascript
var b = Binomial({p: 0.65, n: 3})
print(b)
viz(b)
````
## Bayes-tétel












