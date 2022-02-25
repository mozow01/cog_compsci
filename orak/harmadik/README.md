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


