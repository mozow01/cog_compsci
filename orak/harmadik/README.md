# Bayesiánus adatelemzés (Bayesian Data Analysis)

## Ismétlés, összefoglalás

A klasszikus valószínűségszámítási példákat jellemzően a rendezett n-esek feletti valószínűségi mezőben oldottuk meg. Ezek nem mások, mint a többváltozós eloszlások (joint distributions). Ezeket az eloszlásokat    algoritmikusan is generáltuk, kétféleképpen: kimerítéses módszerrel (_enumeration_), amikor minden esetet végiszámol az algoritmus, és mintavétellel, amikor egy elég nagy mintából származtatjuk az adatokat (_sampling_). Két fő példa, a kockák és a kártyák voltak. Most először a **feltételes valószínűséget** és a **binomiális eloszlást** elevenítjük fel.

### A feltételes valószínűség

Ekkor leszűkítjük az elemi események terét a feltételre, az A eseményt teljesítő elemi részeseményekre, azaz innentől nem omega, hanem A az összes elemi események tere:

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

P(az első hatos|van a dobások köztött hatos) = 1/91 vagy ((1/216) / (1/91)).

A feltételes valószínűség sokszor olyan intuitív, hogy azonnal ennek az értékét tudjuk, sőt, vannak olyan tárgyalások is (Rényi), amelyekben a feltételes valószínűség az alapfogalom. Éppen ezért a definíciót néha így írják (**szorzattörvénynek** nevezik) (a "törvény" szón persze a matematikusok nevetnek... :)

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
## Bayes-tétel jointtal

A Bayes-tétel egy matematikai trivialitás. Ereje abban áll, hogy úgy viselkedik, mint a szélsőértékszámítástban a maximum meghatározása a deriváltra felírt egyenletből. Csak mindezt valószínűségi eszközökkel.

Legyen P(X,Y) joint eloszlás. Ekkor P(x<sub>i</sub>,y<sub>j</sub>)-t kétféleképpen is fel lehet írni (a második bayesiánus világtörvény alapján):

<img src="https://render.githubusercontent.com/render/math?math=P(X%3Dx_i%5Cwedge%20Y%3Dy_j)%3DP(%20Y%3Dy_j%5C%3B%7C%5C%3BX%3Dx_i)%5Ccdot%20P(X%3Dx_i)">

és 

<img src="https://render.githubusercontent.com/render/math?math=P(X%3Dx_i%5Cwedge%20Y%3Dy_j)%3DP(%20X%3Dx_i%5C%3B%7C%5C%3BY%3Dy_j)%5Ccdot%20P(Y%3Dy_j)">

Innen a két oldal egyenlőségéből:

<img src="https://render.githubusercontent.com/render/math?math=P(%20Y%3Dy_j%5C%3B%7C%5C%3BX%3Dx_i)%5Ccdot%20P(X%3Dx_i)%3DP(%20X%3Dx_i%5C%3B%7C%5C%3BY%3Dy_j)%5Ccdot%20P(Y%3Dy_j)">

Innen pedig a Bayes-tétel:

<img src="https://render.githubusercontent.com/render/math?math=P(%20X%3Dx_i%5C%3B%7C%5C%3BY%3Dy_j)%3D%5Cdfrac%7BP(%20Y%3Dy_j%5C%3B%7C%5C%3BX%3Dx_i)%5Ccdot%20P(X%3Dx_i)%7D%7BP(Y%3Dy_j)%7D">

Ezoterikus jelöléssel pedig:

<img src="https://render.githubusercontent.com/render/math?math=%5Cboxed%7BP(%20X%5C%3B%7C%5C%3BY)%3D%5Cdfrac%7BP(%20Y%5C%3B%7C%5C%3BX)%5Ccdot%20P(X)%7D%7BP(Y)%7D%7D">

Ezt nevezte Pierre-Simon de Laplace "inverz valószínségnek", mert a feltételes és a fordított feltételes valószínűséget kapcsolja össze egy formulával.

### Bayesiánus bestiárium

Akármennyire is furcsa, a legfontosabb fogalom a bayes-i analízisben a 

**generatív modell**

Ez lényegében egy algoritmus, ami nagy adatmennyiséget generál automatikusan, mégpedig azzal a céllal, hogy úgy viselkednej, mintha ők lennénak a valóságos világból nyert adatok:

<img src="https://render.githubusercontent.com/render/math?math=%5Cmathrm%7Bparameter%7D%20%5Cto%20%5Cboxed%7B%5Cmathrm%7Bprogram%7D%7D%20%5Cto%20%5Cmathrm%7Bsok%7D%5C%3B%5Cmathrm%7Badatok%7D%20">

Nem akármilyen céllal: pontosan egy ilyen generatív modell írná le, hogy a világban hogyan bukkannak fel mérhető mennyiségek. Gondoljunk bele: nem dobáltunk kockát, mégis képesek voltunk kockadobást szimulálni. Ezek a programok tehát most nem aritmetikai műveleteket hajtanak végre, hanem véges, de sok olyan adatot produkálnak, amik olyanok, mintha egy adott valószínűségi eloszlásból származnának.

Most tehát megfordítjuk a problémát és megpróbálunk visszakövetkeztetni arra, hogy milyen paraméterértékekkel generálódhatott egy adott 

**adatsokaság**

A generatív modell még nem elég, mert kell egy ideális világleírás (eloszlássereg, mondjuk beta- vagy binomiális-eloszlás ), amiben gondolkodunk. Ennek a leírásnak a szóbajövő paraméreteit kell meghatároznunk, azaz most végül is konkrét elméletet választunk ki egy elméletsokaságból. Az előzetes, adatok néküli tudást hívjuk úgy, hogy 

**prior**

Szedjük össze ezt most formálisan! 

1. Legyenek az X-ek az _elméleti paraméterek_.
2. Ezek valószínűségi eloszlása, ahogy mi elképzeljük, a P(X) _marginális_: ez a prior.  
4. A P( X | Y ) feltételes eloszlás azt írja le, hogyan szóródnak az X paraméterek, amennyiben tudjuk mi az Y adat. Ezt hívják _poszteriori_ eloszlásnak és erre hajtunk: azt szeretnénk tudni, hogy ha kimértük az Y adatokat, akkor milyen paramétereloszlás generálhatta ezt az Y adatot. 

Gondoljunk bele! Ez nem semmi! 

Arról van szó, hogy ha adott a generatív modell nevű algoritmus, az elméleti paramétereloszlás és az adatok, akkor mi a sok elméleti paraméter közül a modell fizikailag lehetséges paramétereloszlása.

Lássuk hogy megy!

### Óvodások

Tudjuk, hogy az óvodások még nem feltétlenül tudnak különbséget tenni állat és növény között. Jó példa erre a pillangó. Elég magas kompetenciaszint egy kiscsoportostól, ha meg tudja mondani, hogy a pillangó növény vagy másféle élőlény. 20 óvodást kérdeztünk meg arról, hogy a pillangó állat-e. 5 óvodás szerint virág, a többiek szerint valami bogárkaféle...

````javascript
// observed data
var k = 5 // number of people who support növény
var n = 20  // number of people asked

var model = function() {

   // true population proportion who support növény
   var p = uniform(0, 1);

   // Observed k people support növény
   // Assuming each person's response is independent of each other
   observe(Binomial({p : p, n: n}), k);

   // predict what the next n will say
   var posteriorPredictive = binomial(p, n);

   // recreate model structure, without observe
   var prior_p = uniform(0, 1);
   var priorPredictive = binomial(prior_p, n);

   return {
       prior: prior_p, priorPredictive : priorPredictive,
       posterior : p, posteriorPredictive : posteriorPredictive
    };
}

var posterior = Infer(model);

viz.marginals(posterior)
````















