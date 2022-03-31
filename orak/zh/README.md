# ZH 2022

## 1. alkalom

**1.1** Írj következtetésre olyan ````ComplexModel1````-et, ami ilyen premisszákat generál véletlenszerűen: Panni "könyvtáros/tanár" "és/vagy" "csendes/cserfes" és ebből logikailag helyesen következtetne olyan konklúzióra, hogy "Panni csendes/cserfes", DE! sajnos diszlexiás az ágens és a "csendes/cserfes"-t valamilyen valószínűséggel felcseréli.

**1.2** Írj következtetésre olyan ````ComplexModel2````-t, ami ilyen premisszákat tartalmaz: Panni "könyvtáros/tanár" "és/vagy" "csendes/cserfes" és ebből logikailag helyesen következtetne olyan konklúzióra, hogy "Panni csendes/cserfes" ill. "Panni könyvtáros/tanár", DE! az "és"-t 95%-ban a klasszikus logikának megfelelően használja, de elég gyakran, 80%-os valószínűséggel néha a vagy-ot és-nek olvassa. Vö.: "Jaj! valami ördög... vagy ha nem, hát... kis nyúl!".

**1.3** Programozz be egy olyan modellt, ami kiszámolja, hogy mi annak a valószínűsége, hogy ha két kockával dobunk, akkor az összeg legalább 4 lesz!

**1.4** (King-Ace Paradox, bemelegítő) Tudjuk, hogy a klasszikus logikában a "Ha ász van a kezemben, akkor király van, **vagy** ha nincs ász a kezemben, akkor király van a kezemben" mondatból nem következik feltétlenül, hogy király van a kezemben (miért?), sőt, ha a mondatban szereplő "vagy"-ot kizáró vagy értelemében használjuk, akkor kifejezetten az következik belőle, hogy nincs király a kezemben. Írj programot, ami a helyzetet modellezi úgy, hogy a mondatbei "vagy" **jelentése** néha "és" néha "vagy" néha "kizáró vagy". Vö.: The Cambridge Handbook of Computational Psychology, ed.: Ron Sun, 2008, Cambrigde, p. 137.

## 2. alkalom

**2.1** Írj programot, amelyik generálja az összes olyan lehetséges esetet, amikor 6 helyre 2 golyót teszünk le ismétlés nélkül! 

**2.2** Írj programot, amelyik modellezi, hogy 6 helyre 3 golyót letéve összesen milyen lehetőségek vannak, feltéve, hogy a 2. helyen nem lehet golyó!

**2.3** A sokéves átlag szerint 0.001 annak a valószínűsége, hogy valaki elbotlik a szavazókör küszöbében és eltöri valamilyét. Írjunk egy rövidke programot, aminek az adataiból ki tudjuk nyerni, hogy mi annak a valószínűsége, hogy a falu 137 szavazásra jogosult választójából pontosan kettővet történik meg ez a sajnálatos eset!

**2.4** Írj programot, amelyik generálja az összes olyan lehetséges esetet, amikor 6 helyre 2 golyót teszünk le és ismétlődés is lehet! 

## 3. alkalom

**3.1** Írj programot, amelyik kiszámolja, hogy mi annak a valószínűsége, hogy 52 lapos francia kártyából 2 kártyát választva az egyik király, a másik nem király! 

**3.2** Legyen az X Y és Z valószínűség változó olyan, ami az {0, 1, 2, 3} számok közöl választ egyenletes valószínűséggel. Legyen W = X + Y + Z, mi az X változó eloszlása, ha tudjuk, hogy W = 7? (Írj programot!)

**3.3** Értsd meg a Monty Hall/vos Savant paradoxon programját!

**3.4** Van egy beépített emberünk a Monty Hall/vos Savant szituációban, akinek a válaszában 50% százalékban helyesen mutogatja el nekünk a színfalak mögül, hogy hol melyik ajtó mögött van az autó. Szót fogadunk neki, és azt az ajtót választjuk, amit ő javasol. Még mindig érdemes-e azután váltani, hogy Monty megmutatot egy kecskét? A választ támasszuk alá webppl programmal!

## 4. alkalom

**4.1** Francia kártyapakliból kiválogatjuk a figurásokat (bubi, dáma, király, ász). Ez 16 lap. Kihúzunk visszatevés nélkül belőlük két lapot. Az alábbi program azokat az eseteket sorolja fel, amikor teljesül az A = "az egyik lap nem kőr vagy a másik lap nem király" esemény. Ezt tekintsük úgy, mint egy olyan P(X,Y) joint eloszlást, ahol a lyukas helyekhez tartozó valószínűség nulla, a többihez egyenletes.

````javascript
var kartya = function () {
  var szin1 = randomInteger(4) + 1;
  var figura1 = randomInteger(4) + 1;
  var szin2 = randomInteger(4) + 1;
  var figura2 = randomInteger(4) + 1;
  var huzas1 = [szin1,figura1];
  var huzas2 = [szin2,figura2];
  condition(!(szin1 == szin2 && figura1 == figura2) && 
            (!(szin1 == 1) || !(figura2 == 1)) &&
            (!(szin2 == 1) || !(figura1 == 1))
             );
           
  return [huzas1,huzas2];
  };

var eloszlas = Enumerate(kartya);

print(eloszlas);

viz.auto(eloszlas);
````

a) Rajzold fel a P(X) = P( X = x<sub>i</sub> ) = ∑<sub>j</sub>P( X = x<sub>i</sub> , Y = y<sub>j</sub> ) marginális eloszlást (vigyázat! ez nem lesz ugyanaz, mint amit a "marginals" parancs ad vissza a webppl-ben!).

b) Számoljuk ki a P( X = treff király vagy X = treff ász | Y = pikk dáma ) feltételes valószínűséget!

**4.2.** Egymás utáni három héten kérdeztük meg, hogy a "Nagyra nőjetek" ételszolgáltató kft. rántotthús fogása mennyire ízlett az napocskás ovis csoportnak. A három mérési adat: 25-nek a 31 főből, 26-nak a 30 főből (egy beteg lett), és 28 a 31 főből (visszajött a beteg) ovisnak ízlett. 

a) Tervezzünk generatív modellt az óvodások válaszainak modellezésére. Dogmatikus beta priort feltételezzünk, amely kifejezi, hogy igen ínyükre van ez az étel! 

A pillangós példa ez volt:

````javascript
var data = [{name: 'napocskas', n:20, k:5},
            {name: 'holdacskas', n:23, k:8},
            {name: 'napraforgo', n:19, k:17},
           ]

var complexModel = function() {
  
  var p = beta(30,90);
  
  map(function(d){observe(Binomial({p: p, n: d.n}), d.k)}, data);
  
  var prior = beta(30,90);
  
    // 20 fős csoportokra normálva
  
  var predictivePosterior = binomial({p: p, n: 20});
  
  var predictivePrior = binomial({p: prior, n: 20});
  
  return {Prior: prior, 
          PredictivePrior: predictivePrior, 
          Posterior: p, 
          PredictivePosterior: predictivePosterior};
}

var opts = {method: 'MCMC', samples: 20000}

var output_2 = Infer(opts, complexModel)

viz.marginals(output_2)
````

**4.3** Határozzuk meg próbálgatással a posterior eloszlás várható értékét és a kredibilitási intervallumot 95%-ra!

Ez a pillangós példánál ez volt:

````javascript
var model = function() {  
  var p = beta(30,90);
  observe(Binomial({p : p, n: 20}), 4);

  var poszterior_predikativ = binomial(p,20);

  var prior_p = beta(30,90);

  var prior_predikativ = binomial(prior_p,20);

return p;
};

var output = Infer({model: model, samples: 10000, method: 'MCMC'});
viz.auto(output);
print(expectation(output));
expectation(output,function(p){0.1<p && p<0.31})
````

**4.4** Hörcsögünk súlyának mérési adatai: 28 g, 31 g, 44 g, 29 g. Lexikonbeli adatok: átalgos súly: 32 g, szórás: 10 g. Mi lesz a prediktív posterior eloszlás és a várható érték?  

````javascript
var data = [{k: 5},
            {k: 16},
            {k: 17},
           ]

var simpleModel = function() {
  
  var m = uniform(4,18);
   
  map(function(d){observe(Gaussian({mu: m, sigma: 1}),d.k)},data);
  
  var Prior = uniform(4,18);
  
  var PredictivePosterior = gaussian(m,1);
  
  return {
          Prior: Prior, 
          Posterior: m,
          PosteriorPredictive: PredictivePosterior
   

      };
}

var opts = {method: 'SMC', particles: 2000, rejuvSteps: 5}

var output_1 = Infer(opts, simpleModel)

viz.marginals(output_1)
````

# 5-6. alkalom

**5.1** Egy másik lexikonban a hörcsög súlyára 40 g-ot mondanak 8 g szórással. Melyik modell magyarázza jobban az adatokat?

**5.2** (King-Ace Paradox, folytatás) Azt vizsgáljuk, hogy az "Ha ász van a kezemben, akkor király van, **vagy** ha nincs ász a kezemben, akkor király van a kezemben" mondatot hogyan értékelik az emberek, abban a tekintetben, hogy követlezik-e belőle a "Király van a kezemben" mondat. A három-választós teszt: "biztosan" "nem feltétlenül" "épp ellenkezőleg". Az egyik modell szerint a válaszok aránya, ilyen sorrendben (0.8, 0.1, 0.1), a másik modell szerint (0.2,0.7,0.1). Írj egy programot, ami az alany válaszát generálja. Kérdezz meg egy embert és találd ki, melyik modell szerint működik! Kérdezz meg 3 embert és mond meg, az "emberek" melyik modell szerint gondolkodnak!

**5.3** Készíts generatív modellt, amelyik megjósolja, hogy elkésem-e a munkából, ha az függ attól, hogy kirándultam-e a hétvégén. Ha kirándultam, akkor ninkább nem kések el. Viszont az, hogy kirándultam-e az attól függ, hogy esett-e az eső a hétvégén. Milyen eloszlások esetén erősen esőre következtetni abból, hogy elkéstem-e vagy sem?

**5.4** Készíts generatív modellt, amelyik megjósolja, hogy vizes-e a fű, ha tudjuk, hogy ez attól függ, hogy felhős-e az ég vagy sem ill, hogy felvette-e a szemüvegét a kertész, aki bekapcsolja a locsolót, vagy sem. Ha nem veszi fel a szemüvegét nem tudja, hogy felhős-e ezért inkább locsol. 

