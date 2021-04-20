# Bayesiánus adatelemzés (Bayesian Data Analysis)

## Ismétlés, összefoglalás

A klasszikus valószínűségszámítási példákat jellemzően a rendezett n-esek feletti valószínűségi mezőben oldottuk meg. Ezek nem mások, mint a többváltozós eloszlások (joint distributions). Ezeket az eloszlásokat    algoritmikusan is generáltuk, kétféleképpen: kimerítéses módszerrel (_enumeration_), amikor minden esetet végiszámol az algoritmus, és mintavétellel, amikor egy elég nagy mintából származtatjuk az adatokat (_sampling_). Két fő példa, a kockák és a kártyák voltak. Most először a **feltételes valószínűséget** és a **binomiális eloszlást** elevenítjük fel.

### A feltételes valószínűség

Ekkor leszűkítjük az elemi események terét a feltételre, az A eseményt teljesítő elemi részeseményekre, azaz innentől nem omega, hanem A az összes elemi események tere:

<img src="https://render.githubusercontent.com/render/math?math=P(B%7CA)%5Coverset%7B%5Cmathrm%7Bdef.%7D%7D%7B%3D%7D%5Cdfrac%7BP(A%5Ccap%20B)%7D%7BP(A)%7D%5Cquad%20%5Cquad%20P(A)%5Cneq%200">

(P(B|A) -t úgy mondjuk ki, hogy B valószínűsége feltéve, hogy A (probability of B given A)).

**1.** 

**a)** Mi annak a valószínűsége, hogy három kockával dobva, az eredményben van hatos. 

**b)** Mi annak a valószínűsége, hogy az első kocka hatos feltéve, hogy van a dobottak között (egyáltalán) hatos.

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

Ezzel gyakorlatilag a feltételes valószínűséget is megértettük, így:

* Mi annak a valószínűsége, hogy az első kocka hatos feltéve, hogy van benne (egyáltalán) hatos? 

P(az első hatos | van a dobások köztött hatos) = 1/91 vagy ((1/216) / (1/91)).

A feltételes valószínűség sokszor olyan intuitív, hogy azonnal ennek az értékét tudjuk, sőt, vannak olyan tárgyalások is (Rényi), amelyekben a feltételes valószínűség az alapfogalom. Éppen ezért a definíciót néha így írják (**szorzattörvénynek** nevezik) (a "törvény" szón persze a matematikusok nevetnek... :)

<img src="https://render.githubusercontent.com/render/math?math=P(A%5Ccap%20B)%3DP(B%7CA)%5Ccdot%20P(A)%5Cquad%20%5Cquad%20%5Cmathrm%7Bill.%7D%20%5Cquad%20%5Cquad%20P(B%5Ccap%20A)%3DP(A%7CB)%5Ccdot%20P(B)">

### Feltételes eloszlás joint eloszlás esetén

Joint esetén, szokásos módon inkább egy új P( Y | X ) eloszlást definiálnak (amely szintén egy joint eloszlás) és ezt hívják feltételes valószínűségnek. A kiszámítási formulája:

<img src="https://render.githubusercontent.com/render/math?math=P(Y%3Dy_j%7CX%3Dx_i)%20%3D%20%5Cdfrac%7BP(Y%3Dy_j%5Cwedge%20X%3Dx_i)%7D%7BP(X%3Dx_i)%7D">

Ami a fenti egy speciális esete akkor, amikor a P(X,Y) joint eloszlásban olyan feltételt fogalmazunk meg, ami azt mondja, hogy az egyik változó rögzített értékű. A joint előző órai jelölésével, mivel <img src="https://render.githubusercontent.com/render/math?math=P(x_i%2Cy_j%5C%3B)%3DP(X%3Dx_i%5Cwedge%20Y%3Dy_j)">, ezért ezt még így is írhatjuk:

<img src="https://render.githubusercontent.com/render/math?math=P(Y%3Dy_j%5C%3B%7C%5C%3BX%3Dx_i)%20%3D%20%5Cdfrac%7BP(x_i%2Cy_j%5C%3B)%7D%7BP(X%3Dx_i)%7D">

ill. a menőző (borzasztó, túl kompakt) jelöléssel:

<img src="https://render.githubusercontent.com/render/math?math=P(Y%7CX)%20%3D%20%5Cdfrac%7BP(X%2CY%5C%3B)%7D%7BP(X)%7D">

hiszen <img src="https://render.githubusercontent.com/render/math?math=P(X)%3DP(X%3Dx_i)%20%3D%5Csum_j%20P(x_i%2Cy_j)"> a **marginális eloszlás.**

**2.** Ellenőrizzük az előző feladatban, hogy a marginális eloszlások tényleg azok, amiket a ````viz.marginals(eloszlas)```` parancs ad vissza! Világos, hogy a program szerint az (X,Y) joint tér az, hogy X = "az első kockával x_i-t dobunk", Y ="a második kockával y_j-t dobunk", és a P(X,Y) az az **eloszlás**, ami nulla, ha X és Y nem hatos, és egyenletes, ha van benne hatos. 

### Binomiális eloszlás, selejtformula

Amikor a kártyapakliba nem tettük vissza a kihúzott kártyalapokat, akkor visszatevés nélküli mintavételt alkalmaztunk. Lásd: _mi annak a valószínűsége, hogy 5 kártyából legalább az egyik treff!_

Ha a populáció óriási és a minta pici, pl. egy egész ország népessége, kontra 20 fő, akkor a mintavételt tekinthetjük akár visszatevésesnek is, mert a 20 ember kiválasztásának hatása az arányokra nézve olyan, mint halottnak a csók. A visszavetés nélküli mintavételezés selejtformulája a binomiális eloszlás formulája.

* Tegyük fel, hogy egy bizonyos _A_ tulajdonság (pl.: Brexitre szavazott-e/nem-e), az adott populációban p valszínűséggel teljesül egy emberre. Ekkor annak a valószínűsége, hogy n kiválasztott személyből pontosan k  rendelkezik az _A_ tulajdonsággal, 

<img src="https://render.githubusercontent.com/render/math?math=P(X%3Dk)%3D%7Bn%20%5Cchoose%20k%7D%5Ccdot%20p%5Ek%5Ccdot%20(1-p)%5E%7Bn-k%7D">

(Bizonyítás egyszerű, ha valaki szeretné látni, akkor majd kitaláljuk együtt.)

**3.** Számítsuk ki, hogy mi annak a valószínűsége, hogy 3 emberből legalább 1 bliccel a BKV-n, ha a magyarok jegyvásárlási hajlandósága 65%.

````javascript
var b = Binomial({p: 0.65, n: 3})
print(b)
viz(b)
````

## Bayes-tétel jointtal

A Bayes-tétel egy matematikai trivialitás. Ereje abban áll, hogy úgy viselkedik, mint a szélsőértékszámításban a maximum meghatározása a deriváltra felírt egyenletből -- azaz visszafelé. Csak persze mindezt valószínűségi eszközökkel.

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

**Megjegyzés.** Könnyen el lehet bánni a P(Y) marginálissal, hiszen ez direkt dolgoktól függ:

<img src="https://render.githubusercontent.com/render/math?math=P(Y)%3DP(Y%3Dy_j)%3D%5Csum_i%20P(x_i%2Cy_j)%3D%5Csum_i%20P(Y%3Dy_j%20%5C%3B%7C%5C%3BX%3Dx_i)%5Ccdot%20P(X%3Dx_i)%3D%5Csum_X%20P(Y%5C%3B%7C%5C%3BX)%5Ccdot%20P(X)">

### Bayesiánus bestiárium

**generatív modell**

Ez egy GM algoritmus, ami nagy adatmennyiséget képes generálni automatikusan, mintha ő lenne a valóság, ami adatokat teremt:

<img src="https://render.githubusercontent.com/render/math?math=%5Cmathrm%7Bparameter%7D%20%5Cto%20%5Cboxed%7B%5Cmathrm%7Bprogram%7D%7D%20%5Cto%20%5Cmathrm%7Bsok%7D%5C%3B%5Cmathrm%7Badatok%7D%20">

Ilyennel már találkoztunk. Nem dobáltunk kockát, nem húztunk kártyát, a gép elvégezte helyettünk. Képesek voltunk kockadobást, laphúzást szimulálni programmal. Ezek algoritmusok, tehát, most nem pusztán aritmetikai műveleteket hajtanak végre, hanem véges, de sok olyan adatot produkálnak, amik olyanok, mintha egy adott valószínűségi eloszlásból származnának. Pont úgy, ahogy a pottyantó gép teszi, ami a binomiális eloszlást modellezi. A

**bayesiánus következtetés:** (és ez a lényeg!) az, hogy megfordítjuk a következtetési láncot és megpróbálunk visszakövetkeztetni arra, hogy a generatív modell milyen bemenetével (legyen ez X), azaz a paramétertér milyen

**paraméterértékeire** tud generálódni egy valóságosan mért (tehát nem szimulált) 

**adat** (legyen ez mondjuk y).

Az adat és a generatív modell még nem elég, mert a paraméterteret is be kell népesíteni paraméterértékekkel és ehhez valami előzetes tudással kell rendelkeznünk arról, hogy mit gondolunk erről a térről. Ez az adatok nélkül feltételezett P(X) _marginális eloszlás,_ a  

**prior eloszlás**.

Az általános P(X,Y) eloszlás, ami a

**joint eloszlás**, minden Y-ra nem ismert, mert csak néhány Y mért adatot ismerünk (itt most y-t). 

A P( y | X ) feltételes eloszlás, a 

**likelihood** (ez a Bayes-tételben a másik szorzó) viszont éppen amiatt _ismert,_ hogy GM generálja szimulált adatokat.  

Ha ugyanis adott y, és GM mint algoritmus, továbbá egy eljárás, ami X értékeket ad vissza a P(X) prior eloszlásból, akkor egyezerű számítással ellenőrizni tudjuk, hogy fenn GM(X)=y fenn áll-e egy adott X-re. A legnagyobb valószínűségű ilyen X pont az argmax<sub>x</sub>  P( X = x | Y = y ). És ezzel tulajdonképpen már készen is lennén (ez a _maximum likelihood_ eljárás). De, mivel a GM(X)=y egyenlőséget teljesítő X-ek eloszlása megvan, ezért a P( X | Y = y ) adatokon és a Bayes-tételen keresztül már a  

**posteriori eloszlás**, vagyis a P( Y = y | X ) is rendelkezésünkre áll, ennek mind várható értékével és szórásával együtt. Ami sokkal több, mint pusztán egy maximumhely, amit a maximum likelihood ad vissza. 

> A **bayesiánus eljárás** tehát 
> 
> 1. a P(X) priornak megfelelő X-eket generálva
> 
> 2. elkészíti azoknak az X-eknek az eloszlását, amire 
> > GM(X) = y,
> 
> ez a P( X | Y = y ) _likelihood_ és 
> 
> 3. ebből gyárja le a P( Y = y | X ) _poszteriort_ az 
> > P( X | Y ) = P( Y | X ) P (X) / P(Y) 
> 
> Bayest-tétel felhasználásával. Itt P(Y=y) konstans, ezért érvényes a 
> > P( X | Y=y ) α P( Y=y | X ) P (X) 
> 
> arányosság, ezért csak 
> 
> 4. normálni kell P( Y=y | X ) P (X)-t és máris megvan a poszterior, ami tehát azt írja le, hogy milyen az azon fizikailag is paraméterértékek _eloszlása,_ amiből az adtat származhatott. 

### Óvodások

Tudjuk, hogy az óvodások még nem feltétlenül tudnak különbséget tenni állat és növény között. Jó példa erre a pillangó. Elég magas kompetenciaszint egy kiscsoportostól, ha meg tudja mondani, hogy a pillangó növény vagy másféle élőlény. 20 óvodást kérdeztünk meg arról, hogy a pillangó állat-e. 5 óvodás szerint virág, a többiek szerint valami bogárkaféle. Ismerve az adatot, mi annak az eloszlásnak a várható értéke és 5%-hoz tartozó konfidenciaintervalluma, amelyből ez az adat származhatott? 

**Megoldás.**

A BDA elkezdéséhez két adósságunk van. 1. egy (paraméteres) generatív modell, ami olyan adatokat képes szolgáltatni, amiket az óvodások válaszai tudnának lenni, 2. egy prior eloszlássereg, amiben p megjelenik.

* _modellgyártás:_

1. Nem nehéz megállapítani, hogy mi tud lenni az adatszimuláló algoritmus: _binomiális eloszlással_ van dolgunk, n=20 elemből kell kiválasztani véges sokat (akik virágnak nézik a pillangót), és ezt p valószínűséggel teszik. Ha például súly vagy magassági adat lenne, akkor valószínűleg normáleloszlást választanánk. Meg is van a _generatív modell:_

<img src="https://render.githubusercontent.com/render/math?math=%5Cboxed%7B%5Cmathrm%7Bprogram%7D%7D%5C%3B%3D%5C%3B%5Cboxed%7B%5Cmathrm%7Bbinomial(p%2C20)%7D%3A%5Cmathrm%7Brandom%7D%5C%3B%5Cmathrm%7Bmintavetel%7D%5Cto%20%5B%5Cbullet%2C%20%5Ccirc%2C%20%5Ccirc%2C%20%5Cbullet%2C%20...%2C%20%5Ccirc%5D%7D">

2. A prior eloszlás már határozatlanabb ügy, a legkevésbé okoskodó megoldás az, hogy p-t egy egyenletes eloszlásból származtatjuk, azaz véletlenszerűen adunk neki 0 és 1 között értéket. Ez lesz a _prior._

* GM(X) = 5 rekurzívan generálódik

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
