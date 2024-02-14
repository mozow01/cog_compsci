# Frekventizmus és bayesianizmus

## Előzetes összevetés

### Mintapélda (aranyhörcsi)

Van egy törpehörcsögünk, amelyikről azt gyanítjuk, hogy rendellenesen fogy. A súlya elméletileg egy 22 g közepű 3 g-os szórású normál eloszlás (haranggörbe). El kéne dönteni, hogy orvoshoz kell-e vinni.  

#### Klasszikus frekventista megközelítés

- **Nullhipotézis (H0):** A töpehörcsög súlya normális, vagyis [![\\ \mu = \mu_0](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu%20%3D%20%5Cmu_0)](#_) (ahol [![\\ \mu_0](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu_0)](#_) az elvárt átlagos súly).
  
- **Alternatív hipotézis (H1):** A töpehörcsög súlya rendellenesen alacsony, tehát [![\\ \mu < \mu_0](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu%20%3C%20%5Cmu_0)](#_).

Ez egy egyoldalas teszt, mivel csak az érdekel bennünket, hogy a súly rendellenesen alacsony-e.

#### Statisztikai teszt típusa:

A t-próba lenne megfelelő, mivel a populációs szórás ismeretlen, és a minta mérete kisebb, mint 30. A tesztstatisztika a következő lenne:

[![\\ t = \frac{\bar{x} - \mu_0}{s/\sqrt{n}}](https://latex.codecogs.com/svg.latex?%5C%5C%20t%20%3D%20%5Cfrac%7B%5Cbar%7Bx%7D%20-%20%5Cmu_0%7D%7Bs%2F%5Csqrt%7Bn%7D%7D)](#_)

ahol:
- [![\\  \overline{x}](https://latex.codecogs.com/svg.latex?%5C%5C%20%20%5Coverline%7Bx%7D)](#_) a mintaátlag,
- [![\\ \mu_0](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu_0)](#_) az elvárt populációs átlag,
- _s_ a mintaszórás,
- _n_ a minta mérete (javasolt mérési szám: 10 mérés).

Ezt a tesztstatisztikát összehasonlítanánk a t-eloszlás kritikus értékével az alfa szignifikanciaszinten (0.05). Ha a t-eloszlás kritikus értéke alacsonyabb a számított t-értéknél (p<alfa/2), akkor elutasítjuk a nullhipotézist és elfogadjuk az alternatív hipotézist, tehát megállapítjuk, hogy a töpehörcsög súlya rendellenesen alacsony. Ellenkező esetben nem áll rendellenesség fenn a súlyával kapcsolatban.

### Összevetés 1.

|                   | Frekentista statisztika                             | Bayesiánus statisztika                                 |
|-----------------------------|------------------------------------------------------|------------------------------------------------------|
| Alapelvek                   | Egy matematikailag felépített mintatérből vett minta valószínűségi eloszlásának alapján következtet. Az előzetes tudás és korábbi mérések eredményei rejtettek. | A priori (előzetes) és a posterior (adatfelvétel utáni) valószínűségi eloszlásokat használ. A priori eloszlás valóban explicit a priori tudás (meggyőződés), és a következtetés az adatokkal való ütköztetés utáni az a posteriori tudásból van levonva. |
| Előzetes elvárások (nem lehet eléggé hangsúlyozni :D )           | Nem használ, nem utal az előzetes tudásra, csak a mintavételezéskor keletkező mintára összpontosít. | Bevezeti a prior elvárásokat, amelyek a kezdeti ismereteket és hozzáértést tükrözik.                |
| Paraméterek értelmezése     | A paraméterek fix értékeket képviselnek, melyek ismeretlenek, de konstansok.                                | A paraméterek valószínűségi eloszlások formájában értelmezettek.  |
| Bizonytalanság kezelése      | A bizonytalanságot a konfidencia intervallumokkal fejezi ki, az intervallum végpontjai egyfajta két pontú pontbecslés. 95%-os konfidenciaintervallum azt jelenti, hogy az ismeretlen paraméter 95%-os valószínűséggel található meg a kiszámított tartományban.                                             | A paraméternek inherens bizonytalansága van, ami valószínűségi eloszlásos formájában jelenik meg. Így egy ilyen következtetés nem pontszerű, hanem eloszlást ad vissza. Ennek legsűrűbb intervaluma a HDI.   |
| Adatkövetelmény              | Gyakran nagy mintaméreteket igényel, hogy az eredmények stabilak legyenek.                                  | Használható kis minta esetén is, mivel a prior tudás és adatok segíthetnek a becslésekben. |       
| Adatfeldolgozás              | Kész megololdóképletek a számítógép előtti korból, táblázatok, statisztikusi tapasztalatra hivatkozik.                            | Egységes elméleti keretrendszert ajánl fel: (előzetes tudás + aktuális adat -> aktuális tudás), numerikusa számolja a következtetéseket. |                        |
|  Jelenségek modellezése              | Mintavételzési eljárásokat ajánl fel.  | Igazodik a természettudományos, valódi kísérletekhez, ezeket formalizálja.    |               
| Interpretáció               | Gyakran konfidencia intervallumokkal és hipotézisvizsgálatokkal foglalkozik, amelyek nehezebben értelmezhetők.  | A valószínűségi értelmezés miatt könnyebb értelmezni a statisztikai eredményeket.                   |


