# Gráfmodellek (folytatás)

## Ismétlés 

A gráfmodell alapjául szolgáló _G_ irányított körmentes gráf (DAG) egy spéci címkézett osztályba tarozik. A bekarikázott csúcsok valószínűségi változókat reprezentálnak (és elég ezeket feltüntetni, de nem kell csak ezeket), a bekarikázatlanok determinisztikus változókat. A nyilak a p(x<sub>0</sub>,...x<sub>K</sub>) joint valószínűség alábbi alakú szorzattá bonthatóságát fejezi ki:

<img src="https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle%20%5Cmathrm%7Bp%7D(%5Cmathbf%7Bx%7D)%3D%5Cprod_%7Bk%3D1%7D%5E%7BK%7D%5Cmathrm%7Bp%7D(x_k%5Cmid%5Cmathrm%7Bpare%7D_k)">

ahol pare<sub>k</sub> az x<sub>k</sub> csúcs (közvetlen) szülei. A szorzatban tehát pontosan akkor szerepel a p(x<sub>k</sub> : pare<sub>k</sub> ) tényező, ha G-ben van pare<sub>k</sub> pontjauiból x<sub>k</sub>-ba mutató nyíl. A besatírozott csúcsok olyan valószínűségi változók, amelyek a megfigyelt változókat reprezentálják. Tehát G gráfmondellje a p(x<sub>0</sub>,...x<sub>K</sub>) joint valószínűségnek, ha a fenti fakrotizációs tulajdonság teljesül.

## Bevezető példa

(locsoló-eső)

## Multinomiális eloszlás

Legyen adva k hely, ahova n = 1 vagy 2 vagy 3 ... k golyót rakunk le. Ezzel megadjuk, hogy egy kategóriális változó mely értékeit jelüljük ki. Például van egy kérdés, amelyekre az a) -- b) -- c) -- d) -- ... válaszokat választhatjuk ki. Ha több válasz is, de mindig ugyanannyi megadható, akkor n > 1. Valahonnan tudjuk, hogy az egyes lehetőségeket az emberek a p<sub>1</sub>,...,p<sub>k</sub> valószínűségekkel választják. (Persze p<sub>1</sub>+...+p<sub>k</sub> = 1.) A kérdés, hogy mi annak a valószínűség hogy valaki az a)-t éppen n<sub>a</sub>, a b)-t n<sub>b</sub>, ... db-szor választotta. 


<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/ketevi_2.png" width=500>

````javascript

var multiModel = function() {  

  var data =[{v: [1,0,0,0]}, {v: [1,0,0,0]}, {v: [1,0,0,0]}, {v: [1,0,0,0]}, 

             {v: [1,0,0,0]}, {v: [1,0,0,0]}, {v: [1,0,0,0]}, {v: [1,0,0,0]},

             {v: [0,1,0,0]}, {v: [0,1,0,0]}, {v: [0,1,0,0]}, {v: [0,1,0,0]},

             {v: [0,1,0,0]}, {v: [0,1,0,0]}, {v: [0,1,0,0]}, {v: [0,1,0,0]},

             {v: [0,1,0,0]}, {v: [0,1,0,0]}, {v: [0,0,1,0]}, {v: [0,0,1,0]},

             {v: [0,0,1,0]}, {v: [0,0,1,0]}, {v: [0,0,0,1]}, {v: [0,0,0,1]},

             {v: [0,0,0,1]}, {v: [0,0,0,1]}, {v: [0,0,0,1]},

            ];


    var vector = Vector([4,6,4,2]);
    
    //[6, 13, 44, 73]
    
    //[8, 10, 4, 4]
    
    var x = dirichlet(vector);

    var x1 = (x.data)[0];

    var x2 = (x.data)[1];

    var x3 = (x.data)[2];

    var x4 = (x.data)[3];

   var s = x1 + x2 + x3 + x4;


map(function(d){observe(Multinomial({ps: [x1/s,x2/s,x3/s,x4/s], n: 1}), d.v)}, data);

   
  var w = multinomial({ps: [x1/s,x2/s,x3/s,x4/s], n: 1});

    return {
      PredictivePosterior: w
    };

}      


var opts = {method: 'MCMC', samples: 10000}

var output_1 = Infer(opts,multiModel)

````


