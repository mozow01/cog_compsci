# Frekventizmus és bayesianizmus

## Előzetes összevetés

### Mintapélda (törpehörcsi)

Van egy törpehörcsögünk, amelyikről azt gyanítjuk, hogy rendellenesen fogy. A súlya (tömege :) ) elméletileg egy 22 g közepű 3 g-os szórású normál eloszlás (haranggörbe). El kéne dönteni, hogy orvoshoz kell-e vinni. 

![Csofi](https://github.com/mozow01/cog_compsci/blob/main/2024/horcsi.jpeg)

#### Klasszikus frekventista megközelítés

- **Nullhipotézis (H0):** A töpehörcsög egészséges, vagyis súlya [![\\ \mu = \mu_0](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu%20%3D%20%5Cmu_0)](#_) (ahol [![\\ \mu_0](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu_0)](#_) az elvárt átlagos súly).
  
- **Alternatív hipotézis (H1):** A töpehörcsög súlya rendellenesen alacsony, tehát [![\\ \mu < \mu_0](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu%20%3C%20%5Cmu_0)](#_).

Ez egy egyoldali próba, mivel csak az érdekel bennünket, hogy a súly rendellenesen alacsony-e.

- **Statisztikai teszt típusa:**

t-próbát választunk, mert a populációs szórás ismeretlen, és a minta mérete kisebb mint 30. A tesztstatisztika a következő lenne:

[![\\ t = \frac{\bar{x} - \mu_0}{s/\sqrt{n}}](https://latex.codecogs.com/svg.latex?%5C%5C%20t%20%3D%20%5Cfrac%7B%5Cbar%7Bx%7D%20-%20%5Cmu_0%7D%7Bs%2F%5Csqrt%7Bn%7D%7D)](#_)

ahol:
- [![\\  \overline{x}](https://latex.codecogs.com/svg.latex?%5C%5C%20%20%5Coverline%7Bx%7D)](#_) a mintaátlag,
- [![\\ \mu_0](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu_0)](#_) az elvárt populációs átlag,
- _s_ a mintaszórás,
- _n_ a minta mérete (javasolt mérési szám: 10 mérés).

Ezt a tesztstatisztikát hasonlítjuk össze a t-eloszlás kritikus értékével az alfa szignifikanciaszinten (0.05). Ha a t-eloszlás kritikus értéke alacsonyabb a számított t-értéknél (p < alfa), akkor elutasítjuk a nullhipotézist, tehát megállapítjuk, hogy a töpehörcsög súlya kívól esik az elfogadható határon (rendellenesen alacsony). Ellenkező esetben nem áll fenn rendellenesség a súlyával kapcsolatban.

#### Naiv, álnaiv kérdések

1. Értem, hogy a súly haranggörbe, mert nem vagyok hülye, de akkor mi az a t statisztika? (Hogy kerül a csizma az asztalra?) 
2. Mennyiben "betegesen" rendellenes egy [![\\ \mu < \mu_0](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu%20%3C%20%5Cmu_0)](#_) érték? Miért ez az alternatív hipotézis? Miért nem mondjuk az, hogy 17 g +- 3 g ? 
3. A próba alapján két választ kaphatok: A) nem vethető el a nullhipotézi B) elvethető a nullhipotézis. Egyik esetben sem a minket érdeklő "kérdésre" kapunk választ.
4. Mi az a képlet? Jelent valamit vagy nem jelent semmit? (Miért kell statisztikusnak lennem?)
5. Tudom, hogy a próba annál jobb, minél többször mérem a hörcsit. Miért kéne nyaggatni szegényt mondjuk 100 méréssel egy éretelmezhetetlen eredmény miatt?

### Összevetés 1.

|                   | Frekentista statisztika                             | Bayesiánus statisztika                                 |
|-----------------------------|------------------------------------------------------|------------------------------------------------------|
| Alapelvek                   | Egyetlen matematikailag kifundált mintatérből vesz mintákat és ezek alapján következtet. | Előzetes tudással (prior), adattal (megfigyelt változó) és adatfelvétel után levont (pszterior)  következtetésekkel dolgozik. |
| Előzetes elvárások          | Nem utal előzetes tudásra, csak a mintavételezéskor keletkező mintára összpontosít. Az előzetes tudás tacit. | Bevezeti a prior elvárásokat, amelyek a kezdeti ismereteket és hozzáértést tükrözik. Explicit elpzetes tudással dolgozik.                |
| Paraméterek értelmezése     | A paraméterek fix értékek, amelyek ismeretlenek, de konstansok.                                | A paraméterek valószínűségi eloszlások formájában jelennek meg.  |
| Bizonytalanság kezelése      | A bizonytalanságot a konfidedncia intervallumokkal fejezi ki, az intervallum végpontjai egyfajta kétpontú pontbecslés. 95%-os konfidenciaintervallum azt jelenti, hogy az ismeretlen paraméter 95%-os valószínűséggel található meg a kiszámított tartományban.                                             | A paraméternek inherens bizonytalansága van, amit valószínűségi eloszlásos formájában feltételez. Így egy ilyen következtetés nem pontszerű, hanem eloszlást ad vissza. Ennek legsűrűbb intervaluma a 95%-os HDI.   |
| Adatkövetelmény              | Gyakran nagy mintaméretet igényel, hogy az eredmények stabilak legyenek.                                  | Használható kis minta esetén is, mivel a prior tudás rásegít az adatokra. |       
| Adatfeldolgozás              | Kész analitikusan levezettett képletek a számítógép előtti korból, normalitási, függetlenségei feltételekkel. Táblázatokra, statisztikusi tapasztalatra hivatkozik.                            | Egységes elméleti keretrendszert ajánl fel: előzetes tudás + aktuális adat -> aktuális tudás, számítógéppel számolja a következtetéseket. |                        |
|  Jelenségek modellezése              | Mintavételzési eljárásokkal dolgozik.  | Igazodik a természettudományos, valódi kísérletekhez, ezeket formalizálja, explicitté teszi.    |               
| Interpretáció               | Gyakran konfidencia intervallumokkal és hipotézisvizsgálatal foglalkozik, amelyek értelmezése nehézkes, körmönfont.  | A valószínűségi értelmezés miatt könnyebb értelmezni a statisztikai eredményeket.                   |



