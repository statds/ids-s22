---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3 (ipykernel)
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
    1. Take Brooklyn as an example, overlay the building locations of
       the job applications on a Google map.
    1. Based on the data, come up a meaningful question that can be
       answered by the data, and report your findings.

1. The NYC MV collisions data contains a column of the number of
   persons killed and a column of the number of persons killed.
    1. Check if the number of persons killed is the summation of the
       number of pedestrians killed, cyclist killed, and motorists
       killed. From now on, use the number of persons killed as the
       sum of the pedestrians, cyclists, and motorists killed
    1. Construct a cross table for the number of persons killed by the
       contributing factors of vehicle one. Collapse the contributing
       factors with a count of less than 100 to "other". Is there any
       association between the contributing factors and the number of
       persons killed? 
    1. Create a new variable `death` which is one if the number of
       persons killed is 1 or more; and zero otherwise. Construct a
       cross  table for `death` versus borough. Test the null
       hypothesis that the two variables are not associated.
    1. Fit a logistic model with `death` as the outcome
       variable and covariates that are available in the data or can
       be engineered from the data. Example covariates are crash hour,
       borough, number of vehicles involved, etc. Interprete your
       results.
    1. In preparation for the class event at the NYC Open Data Week on
       March 8, suggest a meaningful question that can be answered by
       the data (but no need answer it this time; that will be for
       next week). You may think about comparison
       with data from other periods, in which case, you will need to
       download the right data.

1. Consider the NYC DOB job applications dataset. The column
   "Professional Cert" indicates whether or not the application was
   submitted as Professionally Certified. A Professional Engineer (PE)
   or Registered Architect (RA) can certify compliance with applicable
   laws and codes on applications filed by him/her as
   applicant. Include this variable in the data frame that was
   prepared earlier.
    1. Compare the distribution of the waiting time until approval by
       professionally certified or not. Test the null hypothesis that
       the waiting time until approval is independent of whether the
       application was professionally certified.
    1. Compare the distribution of the waiting time until approval by
       borough. Test the null hypothesis that the waiting time are the
       same across all boroughs.
    1. Compare the distribution of the waiting time until approval by
       five equally sized groups ordered by the logarithm of the
       estimated cost. Test the null hypothesis that the waiting time
       is independent of the estimated cost.
    1. Build a linear regression model to predict the waiting time
       until approval with all the covariates you have
       created. Interpret your results.
    1. In preparation for the class event as the NYC Open Data Week on
       March 8, suggest a meaningful questions that can be answered by
       the data (again, work on the answer next week). 

1. Open data preparation for the NYC Open Data Week event on Tuesday,
   March 8. Coordinate among the groups who worked on the NYC
   collision data and the NYC DOB job application data so that each
   data is analyzed by two teams, one on exploratory
   analysis/visualization and the other on analytics.
    1. Cretate your presentation slides using `Jupyter Notebook`.  One
       tutorial that may be useful is this [Medium
       article](https://medium.com/learning-machine-learning/present-your-data-science-projects-with-jupyter-slides-75f20735eb0f). No
       powerpoint please. Target your presentations for 15 minutes plus
       5 minutes for questions.
    1. Keep in mind that the audience are the general public. So
       please make your presentation accessible and attractive.
    1. In a separate markdown file named `contributions.md`, document
       the contributions of each team member. The team leaders will be
       finalized on Tuesday, March 1.

1. Consider a subset of the [311 service requests data from the NYC Open
   Data](https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2010-to-Present/erm2-nwe9),
   which contains the service request created in the week from 12:00 am 01/23 to
   12 pm 01/29, 2022. The dataset in CSV format is now in our repo
   (`data/311_012322_013022.csv`).
    1. Plot the frequencies of the requests versus the hours they were created
      by borough using `plotnine`.
    1. For those requests that are closed, visualize the time period it took to
       close them by the hours they were created.
    1. Formulate a hypothesis to test whether there is an association between
       the hours created and the time to close for the requests. State your
       hypothesis, and test it with an appropriate test, and summarize the
       result.
    1. Create a binary variable to indicate whether the time to close is longer
       than 24 hours. Note that for many requests that are still open, this
       variable can be calculated. Fit a logistic model to predict this
       variable. Interprete two of the regression coefficients.
    1. Overlay the locations of the incidents on a Google map by day.

1. Project proposal. A data science project proposal consists of the following
   components: 
    + Problem statement. This is where your research question is
      formulated. Given background on the research question, why it is
      interesting, who are interested, what has been known, and some references.
    + Data. Describe the data sources that you will use to answer the research
      question you formulated. Make sure the data are available and
      adequate. Give a summary of the data preparation you expect to do.
    + Analytic tasks. Give some specific tasks that you plan for your
      analysis. It could include exploratory analysis, visualizations, modeling,
      inferences, tests, prediction, and comparison with competing methods,
      among others.
	  
1. Interim project discussion. Please sign up a 20-minute slot to discuss your
   final project with me on Thursday, 04/14 and Friday, 04/15.

|  Date    |  Time              |  Name
|-------|------------|------
| 04/14 | 10:40--11:00 | 
|           | 11:00--11:20 | 
|           | 11:20--11:40 | 
|           | 11:40--12:00 | 
|           | 12:00--12:20 | 
|           | 12:20--12:40 | 
|           | 12:40--13:00 | 
|           | 13:00--13:20 | 
|           | 13:20--13:40 | 
|           | 13:40--14:00 | 
|	        | 14:00--14:20 | Sam Hughes
|04/15 | 09:00--09:20 | 
|           | 09:20--09:40 | Thalia Taffe
|           | 09:40--10:00 | 
|           | 10:00--10:20 | 
|           | 10:20--10:40 | 
|           | 10:40--11:00 | 
|           | 11:00--11:20 | 
|           | 11:20--11:40 | 
|           | 11:40--12:00 | 
|           | 14:00--14:20 | 
|           | 14:20--14:40 | Sinchan Sharma
|           | 14:40--15:00 | Robbie Shamirian
|           | 15:00--15:20 | 
|           | 15:20--15:40 | 
|           | 15:40--16:00 | 
|           | 16:00--16:20 | Taelor McClurg
|           | 16:20--16:40 |

```{code-cell} ipython3

```
