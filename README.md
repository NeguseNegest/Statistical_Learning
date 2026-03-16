# Statistical Analysis of the 2022 SLS Super Crown LCQ

This repository contains a statistical and probabilistic analysis of the 2022 Street League Skateboarding (SLS) Super Crown Championship. The main goal is to model skateboarders’ run and trick scores and use those models to predict which four skaters from the Last Chance Qualifier (LCQ) would advance to the final.

## Project overview

In the LCQ, each skateboarder performs two runs and four single-trick attempts. A trick that is not landed receives a score of zero, and the final LCQ score is computed as:

- the best run score
- plus the two best trick scores

The task in this project is to use data from earlier 2022 SLS events to build predictive models for the LCQ and compare the simulated outcomes with the real qualifiers.

## Dataset

The analysis is based on `SLS22.csv`, which contains competition data from three earlier events in the 2022 season. The dataset includes:

- skater ID
- competition location
- month and year
- competition type
- heat number
- run scores
- trick scores

The score columns are:

- `run 1`, `run 2`
- `trick 1` to `trick 6`

For skaters eliminated early in finals, `trick 5` and `trick 6` may be missing.

## Implemented work

### 1. Data preparation and exploratory analysis

The completed preprocessing and exploratory work includes:

- loading the dataset into a Pandas dataframe
- normalizing run and trick scores from `[0, 10]` to `[0, 1]`
- plotting a histogram of trick scores for tricks 1–4
- creating binary indicator columns `make 1` to `make 4` to mark whether a trick was landed
- estimating empirical trick-success probabilities
- plotting `run 1` against `run 2` to inspect possible dependence between run performances

The histogram suggests a large mass at zero, which supports the idea that failed tricks should be modeled separately from landed tricks.

### 2. Frequentist model

A frequentist model was developed for each skateboarder.

#### Trick-score model
Trick scores are modeled as a two-part process:

- a Bernoulli variable for whether the trick is landed
- a Beta-distributed score for landed tricks

That is, a trick score is:
- `0` if the trick is not landed
- `Z ~ Beta(alpha, beta)` if the trick is landed

The landing probability `theta` is estimated with a point estimate based on the observed trick outcomes.

The Beta parameters for landed trick scores are estimated using:

- maximum likelihood estimation
- Newton–Raphson iteration for the nonlinear system
- method of moments as an initialization step and fallback when needed

#### Run-score model
Run scores are modeled with a Beta distribution on `[0, 1]`, with parameters estimated separately for each skater.

### 3. LCQ simulation

Using the fitted frequentist models, 5000 LCQ competitions were simulated. In each simulation:

- every skater receives two simulated run scores
- every skater receives four simulated trick scores
- the best run and two best tricks are combined into a total score
- the top four skaters are selected as the simulated LCQ qualifiers

## Main result so far

From the 5000 simulated LCQs, the most frequent predicted winner set was:

- Huston
- Jordan
- Shirai
- Hoban

The real LCQ winners were:

- Gustavo
- Hoban
- Eaton
- Decenzo

So, the current frequentist model matches one of the four actual qualifiers.

## Bayesian work

The Bayesian section has been started but is not yet complete.

So far, the project includes:

- a proposed prior structure for the trick model parameters
- a reparameterization of the Beta distribution in terms of mean and precision
- derivation of the joint posterior up to proportionality
- an initial contour plot of the posterior in the `(alpha, beta)` plane
- an outline for using the Metropolis algorithm to generate posterior samples

## Current status

At the moment, the repository contains:

- completed data preparation
- completed exploratory analysis
- a working frequentist model
- LCQ simulation based on 5000 runs
- an initial Bayesian setup

The following parts are still unfinished:

- full posterior sampling for the Bayesian model
- posterior summaries for all skaters
- Bayesian posterior predictive LCQ simulations
- DAG-based independence discussion
- hierarchical Bayesian extension

## How to run

1. Place `SLS22.csv` in the same directory as the notebook/script.
2. Open the notebook and run the cells in order.
3. The notebook will:
   - preprocess the data
   - generate exploratory plots
   - estimate model parameters
   - simulate LCQ outcomes

## Repository purpose

This repository documents the progress of a course project in statistical modeling and prediction. It combines exploratory data analysis, likelihood-based estimation, simulation, and the beginnings of a Bayesian extension for a real sports dataset.

## Files

Typical files in this repository include:

- `StatProject.ipynb` – main notebook with code and markdown
- `ProjectReport.pdf` – written report
- `SLS22.csv` – input dataset

## Notes

This project is still in progress. The frequentist part is the most complete section, while the Bayesian and hierarchical Bayesian sections remain partially implemented.
