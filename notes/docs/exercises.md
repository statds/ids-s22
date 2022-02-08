---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

(exercises)=

# Exercises

1. Write a function to demonstrate the Monty Hall problem through
   simulation. The function takes two arguments `ndoors` and
   `ntrials`, representing the number of doors in the experiment and
   the number of trails in a simulation, respectively. The function
   should return the proportion of wins for both the switch and
   no-switch strategy. Apply your function with 3 doors and 5 doors,
   both with 1000 trials.

1. Write a function to do a Monte Carlo approximation of $\pi$. The
   function takes a Monte Carlo sample size `n` as input, and returns
   a point estimate of $\pi$ and a 95% confidence interval. Apply your
   function with sample size 1000, 2000, 4000, and 8000. Comment on
   the results.

1. Find the first 10-digit prime number occurring in consecutive
   digits of $e$. This was a
   [Google recruiting ad](http://mathworld.wolfram.com/news/2004-10-13/google/).

1. This is simulation study for the MLE.
    1. Write a function to obtain the MLE of the parameters of a
      generalized extreme value distribution with a random sample. 
      The input is the random sample vector `x`. The outputs are the
	  MLE vector of the three parameters and the vector of their
      standard errors. 
    1. Let $\theta$ be the true parameter vector containing the
      location, scale, and the shape parameters. Write a function to
      conduct a simulation study; the inputs are $\theta$, sample size
	  $n$, and the number of replicates `nrep`. 
	1. Conduct the simulation study with $\theta = (0, 1, 0.2)$, sample
      size $n \in \{100, 200\}$, with `nrep = 200`.
    1. Summarize your results; commenting on whether your MLE on
      average recover the true $\theta$ and whether your standard
      errors on average reflect the true standard error of the MLE.

1. The NYC motor vehicle collisions crash table contains details on
   the crashes. The dataset we are using contains the crashes in
   January 2022.
    1. Create a frequency table of the number of crashes by borough.
	1. Create a `hour` variable with integer values from 0 to 23, and
       plot of the histogram of crashes by hour.
    1. Take Brooklyn as an example, overlay the locations of the
       crashes on the map of Brooklyn.
    1. Make the same map but by every 3 hours in a day.

   
