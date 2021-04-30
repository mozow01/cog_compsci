# Bayesiánus adatelemzés (Bayesian Data Analysis)

## Ismétlés, összefoglalás

A klasszikus valószínűségszámítási példákat jellemzően a rendezett n-esek feletti valószínűségi mezőben oldottuk meg. Ezek nem mások, mint a többváltozós eloszlások (joint distributions). Ezeket az eloszlásokat    algoritmikusan is generáltuk, kétféleképpen: kimerítéses módszerrel (_enumeration_), amikor minden esetet végiszámol az algoritmus, és mintavétellel, amikor egy elég nagy mintából származtatjuk az adatokat (_sampling_). Két fő példa, a kockák és a kártyák voltak. Most először a **feltételes valószínűséget** és a **binomiális eloszlást** elevenítjük fel.

### A feltételes valószínűség

Ekkor leszűkítjük az elemi események terét a feltételre, az A eseményt teljesítő elemi részeseményekre, azaz innentől nem Ω, hanem A az összes elemi események tere:

<img src="https://render.githubusercontent.com/render/math?math=P(B%7CA)%5Coverset%7B%5Cmathrm%7Bdef.%7D%7D%7B%3D%7D%5Cdfrac%7BP(A%5Ccap%20B)%7D%7BP(A)%7D%5Cquad%20%5Cquad%20P(A)%5Cneq%200">

(P(B|A) -t úgy mondjuk ki, hogy B valószínűsége feltéve, hogy A (probability of B given A)).

**1.** 

**a)** Mi annak a valószínűsége, hogy két kockával dobva, az eredményben van hatos. 

**b)** Mi annak a valószínűsége, hogy az első kocka hatos feltéve, hogy van a dobottak között (egyáltalán) hatos.

````javascript
var kocka_modell = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  condition(kocka1 == 6 || kocka2 == 6)
  return [kocka1,kocka2];
}

var eloszlas = Enumerate(kocka_modell);

// Ugyenez valószínűségi következtetéssel is kiszámítható
// var következtetés = Infer({method: 'enumerate'}, kocka_modell);
// Ugyanez minételezéssel: 

var következtetés = Infer({method: 'rejection'}, kocka_modell);
print(következtetés);
viz.auto(következtetés);

print(eloszlas);
viz.auto(eloszlas);
````
Ellenőrzés, komplementer valószínűséggel: P(van hatos) = 1 - (5^2)/(6^2) ~ 0.3056 (= 11/36 ).

Ezzel gyakorlatilag a **feltételes valószínűséget** is megértettük, így:

* Mi annak a valószínűsége, hogy az első kocka hatos feltéve, hogy van benne (egyáltalán) hatos? 

P(az első hatos | van a dobások között hatos) = 6/11 vagy ((6/36) / (11/36)).

**Megjegyzés, szorzatszabály.** A feltételes valószínűség sokszor olyan intuitív, hogy azonnal ennek az értékét tudjuk, sőt, vannak olyan tárgyalások is (Rényi), amelyekben a feltételes valószínűség az alapfogalom. Éppen ezért a definíciót néha így írják:

<img src="https://render.githubusercontent.com/render/math?math=P(A%5Ccap%20B)%3DP(B%7CA)%5Ccdot%20P(A)%5Cquad%20%5Cquad%20%5Cmathrm%7Bill.%7D%20%5Cquad%20%5Cquad%20P(B%5Ccap%20A)%3DP(A%7CB)%5Ccdot%20P(B)">

### Feltételes eloszlás joint eloszlás esetén

A 

<img src="https://render.githubusercontent.com/render/math?math=P(X%2CY)%3A%3DP(X%3Dx_i%2CY%3Dy_j)%3A%3DP(X%3Dx_i%5Cwedge%20Y%3Dy_j)">

**joint eloszlás** esetén, a **feltételes valószínűség** gyakran a fenti egy speciális esete és így definiálják:

<img src="https://render.githubusercontent.com/render/math?math=P(X%7CY)%3A%3D%20P(X%3Dx_i%5Cmid%20Y%3Dy_i)">

Persze ezt is lehet szorzat formában írni:

<img src="https://render.githubusercontent.com/render/math?math=P(X%2CY)%3DP(X%3Dx_i%5Cwedge%20Y%3Dy_i)%3DP(X%3Dx_i%5Cmid%20Y%3Dy_i)%5Ccdot%20P(Y%3Dy_j)">

ahol P(Y) = P( Y = y<sub>j</sub> ) spediálisan egyban az egyik marginális eloszlás is:

<img src="https://render.githubusercontent.com/render/math?math=P(Y%3Dy_j)%3D%5Csum_i%20P(X%3Dx_i%2CY%3Dy_j)">

**Megjegyzés.** Vegyük észre, hogy a P (X | Y = y<sub>j</sub> ) = P( X = x<sub>i</sub> | Y = y<sub>j</sub> ) rögzített y<sub>j</sub>-re szintén az **X változó** egy valószínűségi eloszlása, pl. egyre összegződik: 

<img src="https://render.githubusercontent.com/render/math?math=%5Csum_%7Bi%7D%20P(X%3Dx_i%5Cmid%20Y%3Dy_i)%3D%5Csum_%7Bi%7D%5Cdfrac%7BP(X%3Dx_i%2CY%3Dy_j)%7D%7BP(Y%3Dy_j)%7D%3D%5Cdfrac%7B%5Csum_%7Bi%7DP(X%3Dx_i%2CY%3Dy_j)%7D%7BP(Y%3Dy_j)%7D%3D%5Cdfrac%7BP(Y%3Dy_j)%7D%7BP(Y%3Dy_j)%7D%3D1">

Mindez Y-ra nem igaz, az Y értékben változó P(X = x<sub>i</sub> | Y ) csak egy egyszerű függvény, nem feltétlenül összegződik 1-re.
 
**2.** Ellenőrizzük az előző feladatban, hogy a marginális eloszlások tényleg azok, amiket a ````viz.marginals(eloszlas)```` parancs ad vissza! Világos, hogy a program szerint az (X,Y) joint tér az, hogy X = "az első kockával x_i-t dobunk", Y ="a második kockával y_j-t dobunk", és a P(X,Y) az az **eloszlás**, ami nulla, ha X és Y nem hatos, és egyenletes, ha van benne hatos. 

### Binomiális eloszlás, selejtformula

Amikor a kártyapakliba nem tettük vissza a kihúzott kártyalapokat, akkor visszatevés nélküli mintavételt alkalmaztunk. Lásd: _mi annak a valószínűsége, hogy 5 kártyából legalább az egyik treff!_

Ha a populáció óriási a mintához képest, pl. egy egész ország népessége, kontra 20 fő, akkor a mintavételt tekinthetjük akár visszatevésesnek is, mert a 20 ember kiválasztásának hatása az arányokra nézve olyan, mint halottnak a csók. A visszavetés nélküli mintavételezés selejtformulája a binomiális eloszlás formulája.

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

Legyen P(X,Y) joint eloszlás. Ekkor P(x<sub>i</sub>,y<sub>j</sub>)-t kétféleképpen is fel lehet írni (a második bayesiánus világtörvény, a szorzatszabály alapján):

<img src="https://render.githubusercontent.com/render/math?math=P(X%3Dx_i%5Cwedge%20Y%3Dy_j)%3DP(%20Y%3Dy_j%5C%3B%7C%5C%3BX%3Dx_i)%5Ccdot%20P(X%3Dx_i)">

és 

<img src="https://render.githubusercontent.com/render/math?math=P(X%3Dx_i%5Cwedge%20Y%3Dy_j)%3DP(%20X%3Dx_i%5C%3B%7C%5C%3BY%3Dy_j)%5Ccdot%20P(Y%3Dy_j)">

Innen a két oldal egyenlőségéből:

<img src="https://render.githubusercontent.com/render/math?math=P(%20Y%3Dy_j%5C%3B%7C%5C%3BX%3Dx_i)%5Ccdot%20P(X%3Dx_i)%3DP(%20X%3Dx_i%5C%3B%7C%5C%3BY%3Dy_j)%5Ccdot%20P(Y%3Dy_j)">

Így a **Bayes-tétel:**

<img src="https://render.githubusercontent.com/render/math?math=P(%20X%3Dx_i%5C%3B%7C%5C%3BY%3Dy_j)%3D%5Cdfrac%7BP(%20Y%3Dy_j%5C%3B%7C%5C%3BX%3Dx_i)%5Ccdot%20P(X%3Dx_i)%7D%7BP(Y%3Dy_j)%7D">

Ezoterikus jelöléssel pedig:

<img src="https://render.githubusercontent.com/render/math?math=%5Cboxed%7BP(%20X%5C%3B%7C%5C%3BY)%3D%5Cdfrac%7BP(%20Y%5C%3B%7C%5C%3BX)%5Ccdot%20P(X)%7D%7BP(Y)%7D%7D">

Ezt nevezte Pierre-Simon de Laplace "inverz valószínségnek", mert a P(X|Y) feltételes és a fordított feltételes P(Y|X) valószínűséget kapcsolja össze egyetlen formulával.

**Megjegyzés.** Könnyen el lehet bánni a P(Y) marginálissal, hiszen ez direkt dolgoktól függ:

<img src="https://render.githubusercontent.com/render/math?math=P(Y)%3DP(Y%3Dy_j)%3D%5Csum_i%20P(x_i%2Cy_j)%3D%5Csum_i%20P(Y%3Dy_j%20%5C%3B%7C%5C%3BX%3Dx_i)%5Ccdot%20P(X%3Dx_i)%3D%5Csum_X%20P(Y%5C%3B%7C%5C%3BX)%5Ccdot%20P(X)">

### Bayesiánus bestiárium

**Generatív modell:**

Egy generatív modell olyan GM algoritmus, ami nagy adatmennyiséget képes algoritmikusan generálni. Az algoritmus bemenete a **paraméterek,** kimenete a **szimulált adat.** Jellemzően pszeudo-random generátor is szerepel benne, amely úgy produkálja az adatokat, hogy azok nagy átlagban egy adott valószínűségi eloszlásnak megfelelőek legyenek. A generatív modell, tehát egy akármilyen bonyolultságú pszeudorandom-generátor program: 

<img src="https://render.githubusercontent.com/render/math?math=%5Cmathrm%7Bparameter%7D%20%5Cto%20%5Cboxed%7B%5Cmathrm%7Bprogram%7D%7D%20%5Cto%20%5Cmathrm%7Bsok%7D%5C%3B%5Cmathrm%7Badatok%7D%20">

Ilyennel már találkoztunk. Nem dobáltunk kockát, nem húztunk kártyát, a gép elvégezte helyettünk. Képesek voltunk kockadobást, laphúzást szimulálni programmal.

**Bayesiánus következtetés:** (lényeg!) Az előbbi program feladatát megfordítjuk: megpróbálunk visszakövetkeztetni arra, hogy egy valóságosan mért (tehát nem szimulált) y **adat** a generatív modell milyen X = x **paraméterértékeire** tud generálódni. 

**Joint eloszlást** kapunk, ha a paratméterek X és a (szimulált vagy prediktált) adatok Y terének szorzatán feltételezünk egy P(X,Y) valószínűségi eloszlást.

Az adat és a generatív modell még nem elég, mert a paraméterteret is be kell népesíteni paraméterértékekkel és ehhez valami előzetes tudással kell rendelkeznünk arról, hogy mit gondolunk ezek eloszlásáról. Ez a joint eloszlás egy marginális eloszlása, a P(X) **prior eloszlás**.

Az általános P(X,Y) eloszlás általában nem ismert minden Y-ra, mert csak néhány Y mért adatot ismerünk (itt most y-t). 

A **likelihood függvény** az 

<img src="https://render.githubusercontent.com/render/math?math=x%5Cmapsto%20P(Y%3Dy%5Cmid%20X%3Dx)">

függvény, rögzített y adatra és arra használhatjuk, hogy a legjobb paraméterértéket meghatározzuk belőle. Világos, hogy ez nem ugyanaz, mint az 

<img src="https://render.githubusercontent.com/render/math?math=y%5Cmapsto%20P(Y%3Dy%5Cmid%20X%3Dx)">

rögzített x-re, ami egy valószínűségi eloszlás és azt mondja meg, hogy milyen predikciót tudunk tenni az adatokra, ha a paraméter értéke a rögzített x. A likelihood maximum feladat az 

<img src="https://render.githubusercontent.com/render/math?math=x_m%3D%5Cmathrm%7Bargmax%7D(x%5Cmapsto%20P(Y%3Dy%5Cmid%20X%3Dx))"> 

érték meghatározása, ami az a paraméterérték, amire a likelihood függvény maximális. Ha megvan ez az x<sub>m</sub>, akkor az y ↦ P( Y = y | X = x<sub>m</sub> ) függvény az adatokat prediktáló eloszlás. 

Vegyük észre, hogy a likelihood függvény kiszámítható az adatokból! Ha sokszor lefuttatjuk a modellt és kidobjuk a GM(x) = y egyenletet nem teljesítő adatokat, akkor megkereshető lesz az argmax.

A likelihood maximum módszer azonban semmit sem kezd a priorral, csak a generatív modellről mond valamit (bár arról elég sokat).

A P( X | Y = y ) **posteriori eloszlás** viszont a P(X) prior élesítése a mért adatok alapján, ami a likelihood függvényből és a priorból a Bayes-tételen keresztül már kiszámítható  

> A **bayesiánus eljárás** tehát 
> 
> 1. a P(X) priornak megfelelő X-eket generálva
> 
> 2. elkészíti azoknak az X-eknek az eloszlását, amire
> > GM(X) = y,
> 
> 3. ebből gyárja le a P( X | Y = y ) _poszteriort_ az 
> > P( X | Y ) = P( Y | X ) P (X) / P(Y) 
> 
> Bayest-tétel felhasználásával. Itt P(Y=y) konstans, ezért érvényes a 
> > P( X | Y=y ) α P( Y=y | X ) P (X) 
> 
> arányosság, ezért csak 
> 
> 4. normálni kell az x ↦ P( Y=y | X=x ) P (X=x)-t és máris megvan a poszterior, ami tehát azt írja le, hogy milyen az azon fizikailag is paraméterértékek _eloszlása,_ amiből az adtat származhatott. 

### Óvodások

Tudjuk, hogy az óvodások még nem feltétlenül tudnak különbséget tenni állat és növény között. Jó példa erre a pillangó. Elég magas kompetenciaszint egy kiscsoportostól, ha meg tudja mondani, hogy a pillangó növény vagy másféle élőlény. 20 óvodást kérdeztünk meg arról, hogy a pillangó állat-e. 5 óvodás szerint virág, a többiek szerint valami bogárkaféle. Ismerve az adatot, mi annak az eloszlásnak a várható értéke és 95%-hoz tartozó hihetőségi intervalluma (credible intervall), amelyből ez az adat származhatott? 

**Megoldás.**

A BDA elkezdéséhez kell: 1. egy generatív modell, 2. egy prior.

* _modellgyártás:_

1. Az adatszimuláló algoritmus _binomiális eloszlással_ kell, hogy legyen: szimulálni akarjuk az óvodások válaszait robotovodásokkal: n=20 elemből kell kiválasztani véges sokat (akik virágnak nézik a pillangót), és ezt p valószínűséggel teszik. (Ha például súly vagy magassági adat lenne, akkor valószínűleg normáleloszlást választanánk.) 

<img src="https://render.githubusercontent.com/render/math?math=%5Cboxed%7B%5Cmathrm%7Bprogram%7D%7D%5C%3B%3D%5C%3B%5Cboxed%7B%5Cmathrm%7Bbinomial(p%2C20)%7D%3A%5Cmathrm%7Brandom%7D%5C%3B%5Cmathrm%7Bmintavetel%7D%5Cto%20%5B%5Cbullet%2C%20%5Ccirc%2C%20%5Ccirc%2C%20%5Cbullet%2C%20...%2C%20%5Ccirc%5D%7D">

2. A prior határozatlanabb, az alapfeltevés, hogy p-t egy egyenletes eloszlásból származtatjuk, azaz véletlenszerűen adunk neki 0 és 1 között értéket. Ez lesz a _prior._

* Hogyan generálunk p-t a priorból?

````javascript
var prior_szerint_generalt_p = function() {
  var p = uniform(0,1);   // p egy ilyen valószínűségi változó
  return p;
};

var p_eloszlása = 
    Infer({model: prior_szerint_generalt_p, samples: 1000, method: 'MCMC'});
viz(p_eloszlása);
````

* Hogyan választjuk ki a GM(p) = 5 -nek megfelelő paramétereket és mi lesz ezek eloszlása, vagyis a poszterior eloszlás? 

````javascript
var posterior_p = function() {
  var p = uniform(0,1);   // p egy ilyen valószínűségi változó
  observe(Binomial({p : p, n: 20}), 5); // itt választódnak ki az adatnak megfelelő p-k
  return p;
};

var poszterior = 
    Infer({model: posterior_p, samples: 1000, method: 'MCMC'});
    
    //Itt végezzük el az eloszlás modellezését mintavételezéssel
    
viz(poszterior);
````

* Készen is volnánk. De vajon ezzel az új paramétereloszlással milyen lehetséges (fizikai) értékek jöhetnek ki (_prediktív poszterior_) és mik voltak a korábbi eloszlás szerinti értékek (_prediktív prior_), azaz milyen volt és milyen lett, az úgy lehetséges adatok eloszlása?

````javascript
var model = function() {
  var p = uniform(0,1);
  observe(Binomial({p : p, n: 20}), 5);
  

var poszterior_predikativ = binomial(p,20); // ezzel az új p-vel a szimulált adatok


var prior_p = uniform(0,1); // érintetlen paraméter

var prior_predikativ = binomial(prior_p,20); // érintetlen paraméterből szimulált adatok

return {prior: prior_p, priorPredictive : prior_predikativ,
       posterior : p, posteriorPredictive : poszterior_predikativ};
};

var output = 
    Infer({model: model, samples: 1000, method: 'rejection'});
viz.marginals(output);
````

Érdemes hangsúlyozni, hogy a modell erősen igazodik az adott jelenséghez. Amikor óvódás valaszokat generálunk, akkor ezt annak a pontos tudatában tesszük, hogy a válaszok egy "sikesességi" (binomiális) mérés eredményei: virág vagy nem virág.  
