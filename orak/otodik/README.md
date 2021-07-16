# Példák 

## Binomiális kísérlet; különböző méretű óvodás csoportokra

Három csoportban kérdezték meg, hogy a pillangó virág-e, a mért változó értékei a data változó alatt találhatók. A két modellben a priorok a non-informative és a dogmatikus volt. 

````javascript
var data = [{name: 'napocskas', n:20, k:5},
            {name: 'holdacskas', n:23, k:8},
            {name: 'napraforgo', n:19, k:17},
           ]

var simpleModel = function() {
  
  var p = uniform(0,1);
  
  map(function(d){observe(Binomial({p: p, n: d.n}), d.k)}, data);
  
  var prior = uniform(0,1);
  
  // 20 fős csoportokra normálva
  
  var predictivePosterior = binomial({p: p, n: 20});
  
  var predictivePrior = binomial({p: prior, n: 20});
  
  return {Prior: prior, 
          PredictivePrior: predictivePrior, 
          Posterior: p, 
          PredictivePosterior: predictivePosterior};
}

var complexModel = function() {
  
  var p = beta(30,90);
  
  map(function(d){observe(Binomial({p: p, n: d.n}), d.k)}, data);
  
  var prior = beta(30,90);
  
  var predictivePosterior = binomial({p: p, n: 20});
  
  var predictivePrior = binomial({p: prior, n: 20});
  
  return {Prior: prior, 
          PredictivePrior: predictivePrior, 
          Posterior: p, 
          PredictivePosterior: predictivePosterior};
}

var opts = {method: 'MCMC', samples: 20000}

var output_1 = Infer(opts, simpleModel)

var output_2 = Infer(opts, complexModel)

viz.marginals(output_1)

viz.marginals(output_2)
````
## Aranyhal

Egy aranyhal súlyadatai: 5, 16 és 17 g, három mérés után. Úgy döntünk, hogy ha az átlag a 15-17 sávba esik, akkor átlagos mennységet adunk neki, ha kevesebb, akkor többet, ha több, akkor kevesebbet. A kérdés, hogy számtani közepet számoljunk-e (12.7 g, voltaképpen non-informatív prior) vagy (informatív) priorból dolgozzunk-e. Tudjuk, hogy ezen halfajta súlya normál eloszlást mutat, az átlaga 16 g, ennek az adatnak a szórása 0.2 g ± 0.05 g, továbbá a normál elszolás szórása. 

### Megoldás. 

Ha a prior uniform: 

![](https://github.com/mozow01/cog_compsci/blob/main/orak/files/gaussunif_1.png)

És egy hülye hierarchikus modell:



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

var complexModel = function() {
  
   // hiperprior
  
  var epsilon = gaussian(0.2,0.05);

  // prior
  
  var m = epsilon > 0 ? gaussian(16,epsilon) : gaussian(16,0.2);
  
  map(function(d){observe(Gaussian({mu: m, sigma: 1}),d.k)},data);
  
  var HyperPrior = gaussian(0.2,0.05);
  
  var Prior = gaussian(16,HyperPrior);
  
  var PredictivePosterior = gaussian(m,1);
  
  return {
           HyperPrior: HyperPrior,
           Prior: Prior, 
           Posterior: m,
           Posterior_eps: epsilon,
           PosteriorPredictive: PredictivePosterior};
}

var opts = {method: 'SMC', particles: 2000, rejuvSteps: 5}

var output_1 = Infer(opts, simpleModel)

var output_2 = Infer(opts, complexModel)

viz.marginals(output_1)

viz.marginals(output_2)
````
