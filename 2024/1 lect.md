# Frekventizmus és bayesianizmus

## Előzetes összevetés

|                   | Frekentista statisztika                             | Bayesiánus statisztika                                 |
|-----------------------------|------------------------------------------------------|------------------------------------------------------|
| Alapelvek                   | Egy matematikailag felépített mintatérből vett minta valószínűségi eloszlásának alapján következtet. Az előzetes tudás és korábbi mérések eredményei rejtettek. | A priori (előzetes) és a posterior (adatfelvétel utáni) valószínűségi eloszlásokat használ. A priori eloszlás valóban explicit a priori tudás (meggyőződés), és a következtetés az adatokkal való ütköztetés utáni az a posteriori tudásból van levonva. |
| Előzetes elvárások (nem lehet eléggé hangsúlyozni :D )           | Nem használ, nem utal az előzetes tudásra, csak a mintavételezéskor keletkező mintára összpontosít. | Bevezeti a prior elvárásokat, amelyek a kezdeti ismereteket és hozzáértést tükrözik.                |
| Paraméterek értelmezése     | A paraméterek fix értékeket képviselnek, melyek ismeretlenek, de konstansok.                                | A paraméterek valószínűségi eloszlások formájában értelmezettek.  |
| Bizonytalanság kezelése      | A bizonytalanságot a konfidencia intervallumokkal fejezi ki, az intervallum végpontjai egyfajta két pontú pontbecslés. 95%-os konfidenciaintervallum azt jelenti, hogy az ismeretlen paraméter 95%-os valószínűséggel található meg a kiszámított tartományban.                                             | A paraméternek inherens bizonytalansága van, ami valószínűségi eloszlásos formájában jelenik meg. Így egy ilyen következtetés nem pontszerű, hanem eloszlást ad vissza. Ennek legsűrűbb intervaluma a HDI.                                   |
                                             |
| Adatkövetelmény              | Gyakran nagy mintaméreteket igényel, hogy az eredmények stabilak legyenek.                                  | Használható kis minta esetén is, mivel a prior tudás és adatok segíthetnek a becslésekben. |                        |
| Adatfeldolgozás              | Kész megololdóképletek a számítógép előtti korból, táblázatok, statisztikusi tapasztalatra hivatkozik.                            | Egységes elméleti keretrendszert ajánl fel: (előzetes tudás + aktuális adat -> aktuális tudás), numerikusa számolja a következtetéseket. |                        |
|  Jelenségek modellezése              | Mintavételzési eljárásokat ajánl fel.  | Igazodik a természettudományos, valódi kísérletekhez, ezeket formalizálja.    |               
| Interpretáció               | Gyakran konfidencia intervallumokkal és hipotézisvizsgálatokkal foglalkozik, amelyek nehezebben értelmezhetők.  | A valószínűségi értelmezés miatt könnyebb értelmezni a statisztikai eredményeket.                   |



