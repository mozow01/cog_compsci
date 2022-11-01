````r
options(warn = -1)
library(ggplot2)
library(ggridges)
library(ggExtra)
library(dplyr)
library(coda)
library(rjags)
library(openintro)


# DEFINE the model
hier_SDT_model <- "model{
    for (i in 1:length(H)){
    # Likelihood model
    H[i] ~ dbin(thetah[i], s[i])
    F[i] ~ dbin(thetaf[i], n[i])
    
    # Prior model
    thetah[i] <- phi(d[i]/2-c[i])
    thetaf[i] <- phi(-d[i]/2-c[i])
    d[i] ~ dnorm(mud, lambdad)
    c[i] ~ dnorm(muc, lambdac)
    }
    
    muc ~ dnorm(0, 0.001)
    mud ~ dnorm(0, 0.001)
    lambdac ~ dgamma(0.001, 0.001)
    lambdad ~ dgamma(0.001, 0.001)
    sigmad <- 1/sqrt(lambdad)
    sigmac <- 1/sqrt(lambdac)
    
}"

# COMPILE the model    
NATCOND_SDT_jags <- jags.model(textConnection(hier_SDT_model), 
                       data = list(H = list(3,2,2,2,4,1,0,1,1,3,2,4,3,4,4,2) , F= list(0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0), s= list(6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6), n=list(0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0)),
                       inits = list(.RNG.name = "base::Wichmann-Hill", .RNG.seed = 100))



MUSCOND_SDT_jags <- jags.model(textConnection(hier_SDT_model), 
                        data = list(H = list(3,6,2,4,4,5,5,1,3,0,1,2,2,5,6,4) , F= list(0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0), s= list(6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6), n=list(0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0)),
                        inits = list(.RNG.name = "base::Wichmann-Hill", .RNG.seed = 100))


# SIMULATE the posterior

NATCOND_sim_hf <- coda.samples(model = NATCOND_SDT_jags, variable.names = c("muc","mud"), n.iter = 10000)
MUSCOND_sim_hf <- coda.samples(model = MUSCOND_SDT_jags, variable.names = c("muc","mud"), n.iter = 10000)
# MAXCOND_sim_hf <- coda.samples(model = MAXCOND_SDT_jags, variable.names = c("muc","mud"), n.iter = 10000)

# PLOT the posterior

NATCOND_stim_chains <- data.frame(NATCOND_sim_hf[[1]], iter = 1:10000)
MUSCOND_stim_chains <- data.frame(MUSCOND_sim_hf[[1]], iter = 1:10000)
# MAXCOND_stim_chains <- data.frame(MAXCOND_sim_hf[[1]], iter = 1:10000)

COND_comp <- rbind(NATCOND_stim_chains, MUSCOND_stim_chains)
COND_comp$Kondíció <- c(rep("NAT",10000),rep("MUS",10000))

COND_comp_2 <- rbind(NATCOND_stim_chains, MAXCOND_stim_chains)
COND_comp_2$Kondíció <- c(rep("NAT",10000),rep("MAX",10000))

ggplot(COND_comp, aes(x=mud, y=muc, colour=Kondíció))+
    geom_point()

# ggplot(COND_comp_2, aes(x=mud, y=muc, colour=Kondíció))+
#  geom_point()

NAT_bias_mean <- mean(NATCOND_stim_chains[[1]])
NAT_bias_sd <- sd(NATCOND_stim_chains[[1]])
NAT_disc_mean <- mean(NATCOND_stim_chains[[2]])
NAT_disc_sd <- sd(NATCOND_stim_chains[[2]])

MUS_bias_mean <- mean(MUSCOND_stim_chains[[1]])
MUS_bias_sd <- sd(MUSCOND_stim_chains[[1]])
MUS_disc_mean <- mean(MUSCOND_stim_chains[[2]])
MUS_disc_sd <- sd(MUSCOND_stim_chains[[2]])

library(LaplacesDemon)

# px <- dnorm(runif(100),0,1)
# py <- dnorm(runif(100),0.1,0.9)

py <- NATCOND_stim_chains[[2]]
px <- dnorm(runif(10000),0,1)
kld1 <- KLD(px,py,2)

py2 <- MUSCOND_stim_chains[[2]]
px2 <- NATCOND_stim_chains[[2]]
kld2 <- KLD(px2,py2,2)


````
