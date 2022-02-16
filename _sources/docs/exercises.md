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

1. The most accessed dataset at the NYC Open Data is DOB Job
   Application Fillings
   <https://data.cityofnewyork.us/Housing-Development/DOB-Job-Application-Filings/ic3t-wcy2>.
   This dataset contains all job applications submitted through the
   Borough Offices, through eFiling, or through the HUB, which have a
   "Latest Action Date" since January 1, 2000.   The data dictionary
   contains the [variable
   definitions](https://data.cityofnewyork.us/api/views/ic3t-wcy2/files/9bb3688e-1bf8-4f8f-9541-ce936f1d00f4?download=true&filename=DD_DOB%20Job%20Application%20Filings_2019-06-19.xlsx).
   We downloaded a subset containing jobs with a fully permitted date
   in the calendar year of 2021.
    1. Use the raw data to create a data frame containing those jobs
       of type `A2` (an application with multiple types of work that
       do not affect the use, egress, or occupancy of the building)
       and building type `1-2-3 FAMILY`, 
	   with the following columns:
        + `borough`, the burough where the job will take place;
		+ `entry_date`, the date on which the data entry is complete
          and payment has been made;
        + `approved_date`, the date on which the job has been approved
          by the plan examiner;
        + `latitude`, geographical latitude of the building;
		+ `longitude`, geographical longitude of the building;
		+ `cost_est`, dollar amount of the cost estimate.
    1. Plot the distribution of the number of days that it take to
       approve a job application; do it by borough. Comment on your
       findings.
    1. Take brooklyn as an example, overlay the building locations of
       the job applications on a Google map.
    1. Based on the data, come up a meaningful question that can be
       answered by the data, and report your findings.
