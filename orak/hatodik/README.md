# Gráfmodellek (folytatás)

## Ismétlés 

A gráfmodell alapjául szolgáló _G_ irányított körmentes gráf (DAG) egy spéci címkézett osztályba tarozik. A bekarikázott csúcsok valószínűségi változókat reprezentálnak (és elég ezeket feltüntetni, de nem kell csak ezeket), a bekarikázatlanok determinisztikus változókat. A nyilak a p(x<sub>0</sub>,...x<sub>K</sub>) joint valószínűség alábbi alakú szorzattá bonthatóságát fejezi ki:

<img src="https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle%20%5Cmathrm%7Bp%7D(%5Cmathbf%7Bx%7D)%3D%5Cprod_%7Bk%3D1%7D%5E%7BK%7D%5Cmathrm%7Bp%7D(x_k%5Cmid%5Cmathrm%7Bpare%7D_k)">

ahol pare<sub>k</sub> az x<sub>k</sub> csúcs (közvetlen) szülei. A szorzatban tehát pontosan akkor szerepel a p(x<sub>k</sub> | pare<sub>k</sub> ) tényező, ha G-ben van pare<sub>k<sub> pontjauiból x<sub>k</sub>-ba mutató nyíl. A besatírozott csúcsok olyan valószínűségi változók, amelyek a megfigyelt változókat reprezentálják. Tehát G gráfmondellje a p(x<sub>0</sub>,...x<sub>K</sub>) joint valószínűségnek, ha a fenti fakrotizációs tulajdonság teljesül.

## Bevezető példa

  
Ez most egy 
