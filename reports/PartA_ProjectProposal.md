# Dispatch Lab: unit commitment for the Sri Lankan grid

**Part A: Project Proposal**
7COSC013W Foundations of AI. Coursework 1 of 2.
Danuja Wijerathna, UoW number 20251143. Submission: 5 January 2027.

## 1. Problem statement and real-world context

Every evening, the Ceylon Electricity Board (CEB) has to decide which power plants will run the next day, and how hard each one will run, for all 96 quarter-hours of that day. This is called the unit commitment problem, and it is genuinely hard. Plants cannot drop below a minimum stable output. Once a plant starts, it must stay on for hours. Output can only change so fast between one interval and the next. And the reservoirs behind the hydro plants hold one day of water, no more.

The stakes are real. On the days studied here, a single day of generation costs between 200 and 1,000 million rupees, and most of that is imported fuel. Sri Lanka lived through a fuel crisis in 2022, and on 9 February 2025 the whole country lost power in a blackout that was blamed on having too few large machines spinning at midday. A better schedule saves money. A careless one sheds load.

The question this project asks is simple: on the days we can check, could CEB have scheduled better? And if so, where exactly does the saving come from?

## 2. Proposed AI techniques

The project uses three of the module's technique categories, built as four arms that all solve the same problem.

**Knowledge representation.** The generation system is held as a knowledge graph: plants, fuels, river basins, and the rules that govern them. Every fact records where it came from (measured from the data, published by the regulator, or asserted from domain knowledge such as which river a dam sits on). A rule base with forward chaining drives Arm A, a merit-order scheduler that can explain every decision it makes.

**Search.** Arm B is simulated annealing over the on/off matrix: it perturbs the schedule, prices the result, and sometimes accepts a worse schedule so it can escape local traps. Arm C is an exact mixed-integer program that solves an hourly version of each day to proven optimality. Arm C matters because it changes what we can claim: not just "we found something cheaper" but "this is how far everyone is from the best schedule that exists".

**Machine learning.** Arm D is a gradient boosting model that predicts tomorrow's demand from what was knowable the evening before. Its forecast is then fed into the optimiser, so the cost of predicting badly can be separated from the cost of scheduling badly.

The head-to-head is fair by construction. All four arms receive the same problem object, the same data, and the same constraints, and they return the same schedule type. They differ only in how they decide.

## 3. Data

The Public Utilities Commission of Sri Lanka (PUCSL) publishes actual generation for every plant at 15-minute steps on its open data portal (gendata.pucsl.gov.lk). The project uses a local cache of 1,009 days from January 2023 to July 2026, of which 675 days are usable after handling two breaks in the record: the feed changed format in December 2023, and rooftop solar estimates entered it in January 2026. The fleet is 33 real, schedulable units plus 5 aggregated feeds (wind, mini hydro, rooftop solar) that are taken as given.

Costs come from PUCSL's Daily Generation Cost reports (October 2023 to March 2025), which give a rupees-per-kWh figure per plant type. Start-up costs and CO2 factors are published nowhere, so they are declared as assumptions and swept across a range rather than asserted.

The portal states no licence for the data. It is public information published by the regulator, and it is used here for assessment only, with the source credited. All data ships with the notebook as local files, so the notebook runs end to end with no network connection.

## 4. Success criteria and evaluation metrics

The primary metric is the **optimality ratio**: each schedule's cost divided by a proven optimum from the exact solver, on the same instance. Alongside it:

* **Total cost** against two kinds of baseline. Trivial: commit every plant, or commit at random. Strong: CEB's own schedule, given the same optimal loading step as the arms, so the comparison is schedule against schedule.
* **Feasibility, reported separately.** Constraint violations are counted, never blended into the cost. A cheap schedule that cannot be run is not a better answer.
* **Forecast quality**: MAPE, MAE and RMSE against a seasonal naive baseline that scores 7.39% MAPE, which is a real bar. Then the cost that forecast error causes when the schedule built on it meets the real day.
* **Statistical care**: the annealer is random, so it runs 15 seeds per day, with Wilcoxon and Friedman tests and distribution plots rather than single numbers.

Success means beating the trivial baselines clearly, landing close to the proven optimum, matching or beating the operator on like-for-like terms, and being able to explain every result, including the ones that went against expectation.
