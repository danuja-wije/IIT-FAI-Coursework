**Module:** 7COSC013W Foundations of AI

**Coursework:** 2 of 2

**Project title:** Dispatch Lab revisited: a critical and ethical analysis of AI scheduling for the Sri Lankan grid

**Student name:** Danuja Wijerathna

**UoW number:** 20251143

**Submission date:** 12 January 2027

```{=openxml}
<w:p><w:r><w:br w:type="page"/></w:r></w:p>
```

# Dispatch Lab revisited: a critical and ethical analysis of AI scheduling for the Sri Lankan grid

## 1. Introduction

My CW1 project, Dispatch Lab, rebuilt the Ceylon Electricity Board's daily power plant scheduling problem from the regulator's published record: 33 plants, 96 quarter-hour intervals per day, and 675 days of record. Four AI methods answered the same question on the same days. A knowledge graph with a rule base reproduced the operator's explainable logic, simulated annealing searched for cheaper schedules, an exact optimiser proved the best possible schedule on an hourly version of each day, and a machine learning model forecast the next day's demand. Three results matter here. Optimal loading of the plants CEB already chose was worth 9.6% of the bill. The search landed 1.5% above the proven optimum, where CEB's own commitment sat 5.2% above it. And two of my planned design choices failed, in ways I could measure.

This report reflects on that project as a case study. Part A places it in the history and conceptual landscape of AI, because the four arms re-run several decades of the field's development on one dataset. Part B examines what it means to point AI at critical infrastructure: who is harmed when scheduling fails, what the environment gains and loses, and how law sees a system like this.

## Part A: Critical and historical analysis

### 2. Historical context: two lineages meeting on one grid

Dispatch Lab sits at the meeting point of two research traditions that developed almost independently.

The first is symbolic knowledge engineering. Newell and Simon's early work treated intelligence as the manipulation of explicit symbol structures (Newell and Simon, 1976), and the expert systems era built on that: encode what the human expert knows as rules, and let a simple engine apply them. Feigenbaum's lesson from that era was that the power lies in the knowledge, not the inference machinery (Feigenbaum, 1977). My knowledge graph and rule base are direct descendants. The graph holds plants, fuels, river cascades and operating rules as explicit symbols, and every scheduling decision carries the rule that caused it. I also met the era's famous failure mode personally: the knowledge acquisition bottleneck. Whether a plant is contractually must-run cannot be read from dispatch data, because a plant that runs constantly because it is free looks identical to one that runs because it must. My rule base leaves that rule deliberately empty for exactly the reason hand-built expert systems stalled in the 1980s: some knowledge is expensive to obtain and no algorithm can shortcut it. Conversely, the river cascade structure could not be learned from the data either; I had to assert it from domain knowledge, and only then could the data corroborate it.

The second lineage is optimisation for power systems, surveyed by Padhy (2004). Its stages map onto my arms almost one to one. Early utilities used priority lists: sort plants cheapest first and commit down the list, which is precisely my merit order arm, and its measured weakness (13.7% above the optimum, because it cannot look ahead) is the weakness that drove the field onwards. The 1980s and 1990s brought metaheuristics. Simulated annealing began as a physics simulation method (Metropolis et al., 1953), became a general optimiser (Kirkpatrick, Gelatt and Vecchi, 1983), and was applied to unit commitment within seven years (Zhuang and Galiana, 1990). My Arm B is that lineage, with parameters derived from my own data rather than copied. Finally, the modern era belongs to mixed-integer programming. Research there shifted from inventing new searches to writing tighter formulations for provable solvers (Morales-España, Latorre and Ramos, 2013; Knueven, Ostrowski and Watson, 2020), and my Arm C implements both a textbook and a tight formulation to measure the difference on Sri Lankan data. The machine learning arm belongs to a third tradition, statistical learning: gradient boosting (Friedman, 2001) applied to load forecasting, a field with its own history of strong simple baselines (Taylor, 2003; Hong and Fan, 2016).

Running all of these on one dataset makes the project a small living museum: the history of the field, re-executed and re-measured on 2023 to 2026 Sri Lankan data.

### 3. Conceptual foundations and paradigm placement

In the classic division of AI, my rule base and knowledge graph are textbook symbolic AI: a physical symbol system in Newell and Simon's sense, whose every conclusion has an explicit, inspectable derivation. The forecasting arm is statistical learning. One placement point needs care, because the module frames the divide as symbolic versus connectionist. Nothing in Dispatch Lab is connectionist. Gradient boosting is an ensemble of decision trees, and a tree is itself a set of if-then rules learned from data, so my learning arm is a statistical learner with symbolic structure, not a neural network. The absence of deep learning was a choice, not a gap. The data is tabular and modest in size, the application demands explanations, and a simpler model already beats the strong baseline by 26%. An opaque model would have to offer something substantial to justify its opacity, and here it has nothing to offer.

Breiman's two cultures essay (Breiman, 2001) gives a sharper frame than the symbolic versus connectionist binary. My four arms span three cultures: hand-encoded knowledge, algorithmic search, and the proof culture of operations research, where the answer comes with a mathematical guarantee. The project's most instructive finding sits at the seam between cultures. I coupled the knowledge graph to the search, letting it propose which plants can substitute for each other, and expected the claimed contribution of a knowledge-guided search. Measured properly, the guided search was 0.8% worse than a blind one (p = 0.003), because the regulator publishes cost per plant type, so the graph links plants of identical cost and 96% of its suggested swaps cannot change the bill at all. The conceptual lesson is that knowledge encoded for one purpose (explanation) is not automatically useful for another (search). Representation is fit for purpose or it is not, and hybrid designs need the coupling tested, not assumed. I kept the negative result in the notebook with its mechanism, because it says something true about the limits of naive neuro-symbolic combinations.

### 4. Contemporary applications and impact

Unit commitment is not a museum piece. Grid operators worldwide run mixed-integer programs of exactly Arm C's kind every day to schedule national power systems, which is why formulation research remains active. The contemporary pressure on those systems is visible inside my own dataset: rooftop solar jumps from about 1% of the recorded feed to about 18.6% when the regulator starts estimating it in 2026, and Sri Lanka's February 2025 nationwide blackout was attributed to low system inertia during high midday solar. My data shows the operator's response, a minimum synchronised machine count that rises from 8 to 9 after that date. The renewable transition is turning security constraints that were once slack into active ones, and scheduling tools have to carry them.

The forecasting arm connects to a live research question. My model beat the seasonal naive on accuracy, yet the correlation between forecast accuracy and the money the forecast error caused was only 0.22, and the most accurate day caused the largest cost penalty because its error sat at the evening peak. This is the motivating observation of decision-focused learning: predictive models that feed decisions should be trained and judged on decision cost, not statistical error. My project reached that conclusion empirically before I knew the literature had a name for it.

The same arm demonstrates concept drift, the standard reason a deployed model decays, and of a kind worth separating from the usual one. Recorded demand is 24.3% higher in my test period, and a profile learned on the earlier regime and applied unchanged to the later one scores 19.1% error against 9.7% once the level is corrected. But that shift is mostly not load. Rooftop entries go from 0.00% of the recorded feed to 16.5% when the regulator starts publishing its estimate, and excluding them the same comparison is only 3.8%. Around five sixths of the apparent growth is generation that existed all along and merely started being counted. The two kinds of drift have different remedies: when the world moves, retraining catches up, but when the measurement process moves, retraining learns the new definition without ever revealing that the definition changed, and a model can be accurate against a target that no longer means what its report says. My design survives this through structure rather than foresight, since predicting shape against a trailing level absorbs a level shift whatever its cause. The honest limit is that the 2025 gap belongs to no defined regime, so I cannot say where the transition fell. For this domain, though, the most significant breakthrough remains the tight MILP formulation work. It converted unit commitment from a craft of heuristics into an engineering discipline where claims can be proved, and my whole evaluation method depends on that conversion.

## Part B: Ethical, legal and social analysis

### 5. Ethical implications

Electricity scheduling is a quiet activity with loud failure modes. The ethical analysis starts from asymmetry: a schedule that is slightly expensive wastes money, but a schedule that cannot actually be run sheds load, and load shedding is not a statistical inconvenience. Sri Lanka lived this in 2022, when fuel shortages brought hours of daily power cuts, and again in the February 2025 blackout.

That asymmetry shaped three design decisions I would now defend as ethical requirements, in line with the EU High-Level Expert Group's principles of prevention of harm and human oversight (High-Level Expert Group on AI, 2019). First, feasibility is reported separately from cost and never averaged into it, because a cheap schedule that breaks limits is not a better schedule, and a single blended score would hide exactly the failure that matters. Second, unserved energy is priced brutally high and reported, never silently absorbed, so a schedule that sheds load looks as bad in the results as it would be in reality. Third, the system is advisory. It reproduces and critiques the operator's decisions after the fact; it does not make them. The human stays in command, and the design supports rather than erodes that: every commitment in the rule arm carries the rule that caused it, and the arm can also answer why a unit was not committed, which is the interpretability that Rudin (2019) argues high-stakes decisions demand.

The architecture underneath those choices is a symbolic layer wrapping a stochastic one, and it is what makes the less transparent arms defensible too. The annealer is random and can propose anything, but a deterministic feasibility check and repair step stands between it and any schedule the system reports, so nothing the search invents is called feasible unless the symbolic layer agrees. That is the division of labour Kambhampati et al. (2024) argue is the only reliable way to use a generative model in a planning role: the generator supplies breadth and correctness comes from external critics, never from the generator's own confidence. My project is that pattern with no language model in it, which shows the pattern is about verification rather than about neural networks.

There is also an ethics of measurement here, and it has a name. Goodhart's law holds that when a measure becomes a target it stops being a good measure, and the forecast result in Section 4 is a clean instance: a model selected to minimise average error, on a metric that barely tracks the harm the errors cause. Choosing a metric is choosing what harms are visible, so a team that ships the most accurate model may still ship the most harmful one. The engineering response is not a better single number but a constrained objective, minimising cost subject to feasibility rather than folding the two together, which is what my evaluation does by refusing to blend violations into cost.

### 6. Equity, diversity and inclusion

The dataset contains no personal data, so the familiar individual-level bias questions do not arise. The equity questions in this domain operate at the level of communities, and they are severe.

When scheduling fails, the harm does not fall evenly. Blackouts are survivable inconvenience for households with generators and battery backup, and something else entirely for those without: families whose food spoils, students who cannot study, patients on home medical equipment, and small businesses that lose a day's income. Backup power is wealth. Every hour of load shedding is therefore quietly regressive, and both nationwide blackouts in my dataset (December 2023 and February 2025) were borne most heavily by those least equipped to ride them out. Cost matters for equity too: generation costs feed tariffs, and on the days I studied a single day's generation ran between roughly 200 and 1,000 million rupees, mostly imported fuel. Savings from better scheduling are relief that reaches every consumer, including those for whom electricity is a major household expense.

My own results carry a subtle equity warning. The spinning reserve margin, the grid's insurance against sudden failures, turned out to cost only 0.10% of the bill across its whole plausible range, even though it constrains 98% of candidate schedules. Security on this system is nearly free. If a future cost-cutting argument ever proposed trimming that margin, my numbers say the saving would be trivial while the risk it transfers falls on exactly the people least able to absorb it. I also have to be honest about what my evaluation does not measure: it scores cost and feasibility, not who is served. An equity-aware version would weight unserved energy by the vulnerability of who loses supply, and my framework prices all megawatt-hours equally. Finally, there is an inclusion point in the data itself: PUCSL publishing this record openly is what allowed an outsider with a laptop to audit national scheduling at all. Open infrastructure data is a form of accountability, and this project is evidence that it works.

### 7. Sustainability and environmental impact

The project touches sustainability twice, as subject and as practice.

As subject: most of Sri Lanka's generation cost is imported fossil fuel, so cheaper schedules and lower fuel burn largely coincide, and the 9.6% loading result is, in fuel terms, an environmental result. But I report the alignment honestly rather than claiming it. My optimiser minimises cost, not carbon, and the two can diverge. My emissions figures are modelled from assumed factors because the regulator publishes continuous emissions data for only 3 of 80 plants, so every CO2 number in the project is flagged as assumed and swept across a range rather than asserted. The dataset also captures the transition's hard edge: rising solar is exactly what the February 2025 blackout implicated, and the tightened inertia constraint that followed is now a permanent feature of the scheduling problem. Decarbonisation is not free of security consequences, and honest scheduling tools must carry both.

As practice: this is small-footprint AI by construction, in the spirit of the Green AI argument (Schwartz et al., 2020) and against the trend of ever-larger models whose training carries real environmental cost (Strubell, Ganesh and McCallum, 2019). Everything ran on a consumer laptop with no GPU: the nested dispatch program solves in tens of milliseconds, the exact solver proves an optimum in about 18 seconds, and the graded notebook reads cached results so it re-runs in minutes. The expensive computations were run once, cached, and never repeated. For a problem of national significance, the entire computational footprint was a few laptop-hours, which is itself a demonstration that impactful AI need not be resource-hungry AI.

### 8. Legal and regulatory considerations

The data raises the first legal question. The PUCSL portal states no licence for its generation data. I used it on the reasonable footing that a regulator publishes such data for public information, cached it with deliberate rate limiting rather than hammering a public server, credited the source throughout, and used it for assessment only. But the absence of a licence is itself a finding: open government data without stated terms leaves every reuser in a grey zone, and contrasts with the clear CC-BY and ODbL licences on the census and map data my alternative project ideas used. No personal data is involved anywhere, so data protection law is not engaged; the record describes machines, not people.

Against that uncertainty the practical defence is documentation. My project records where the data came from, what share of each field is missing and how each gap is handled, and flags every fact as measured, published or asserted. That is a datasheet for the dataset in the sense Gebru et al. (2021) propose, arrived at because the analysis needed it rather than because a standard demanded it.

Regulation of the system itself is best examined through the EU AI Act (Regulation (EU) 2024/1689), the most developed framework available. The Act lists AI used as a safety component in the management and operation of critical infrastructure, explicitly including electricity supply, among its high-risk categories. Dispatch Lab as built is a research prototype and advisory tool, not a deployed safety component, so it would not itself carry those obligations. The instructive exercise is what would happen if it ever crossed that line. The high-risk requirements are risk management, logging, transparency, human oversight and demonstrated accuracy, and these map almost exactly onto features the project already has for scientific reasons: derivation traces on every decision, provenance flags separating measured from assumed figures, an advisory human-in-command design, and evaluation against a proven optimum. Good science and regulatory readiness turn out to be the same work. Accountability follows the same logic: in an advisory design the operator retains responsibility for the schedule, and recent EU product liability reform extending strict liability to software (Directive (EU) 2024/2853) is a reminder that "it was only a recommendation" weakens as a defence the more autonomous a tool becomes. Keeping the human decision explicit is legal prudence as well as ethics.

### 9. Conclusion

One thread runs through both halves of this reflection: the discipline of provable honesty. Historically, AI's winters followed overclaiming, and the parts of this project that would have overclaimed (a knowledge-guided search that was actually worse, an accurate forecast that was actually costly) were caught only because every claim was measured against a proven bound or an ablation. Ethically, the same discipline is what makes the system defensible: feasibility never hidden inside cost, assumptions flagged and swept, decisions explainable, humans in command. Legally, it is what regulators are now writing into law for critical infrastructure AI. The lesson I take from Dispatch Lab is that these are not three separate virtues. In high-stakes AI, rigorous evaluation, ethical design and regulatory readiness are one habit, practised three ways.

## References

Breiman, L. (2001) 'Statistical modeling: the two cultures', *Statistical Science*, 16(3), pp. 199-231.

Directive (EU) 2024/2853 of the European Parliament and of the Council of 23 October 2024 on liability for defective products. *Official Journal of the European Union*, L series.

Feigenbaum, E.A. (1977) 'The art of artificial intelligence: themes and case studies of knowledge engineering', *Proceedings of the 5th International Joint Conference on Artificial Intelligence*, Cambridge, MA, pp. 1014-1029.

Friedman, J.H. (2001) 'Greedy function approximation: a gradient boosting machine', *Annals of Statistics*, 29(5), pp. 1189-1232.

Gebru, T., Morgenstern, J., Vecchione, B., Vaughan, J.W., Wallach, H., Daume III, H. and Crawford, K. (2021) 'Datasheets for datasets', *Communications of the ACM*, 64(12), pp. 86-92.

High-Level Expert Group on AI (2019) *Ethics guidelines for trustworthy AI*. Brussels: European Commission.

Hong, T. and Fan, S. (2016) 'Probabilistic electric load forecasting: a tutorial review', *International Journal of Forecasting*, 32(3), pp. 914-938.

Kambhampati, S., Valmeekam, K., Guan, L., Verma, M., Stechly, K., Bhambri, S., Saldyt, L. and Murthy, A. (2024) 'LLMs can't plan, but can help planning in LLM-Modulo frameworks', *Proceedings of the 41st International Conference on Machine Learning*, Vienna, pp. 22895-22907.

Kirkpatrick, S., Gelatt, C.D. and Vecchi, M.P. (1983) 'Optimization by simulated annealing', *Science*, 220(4598), pp. 671-680.

Knueven, B., Ostrowski, J. and Watson, J.-P. (2020) 'On mixed-integer programming formulations for the unit commitment problem', *INFORMS Journal on Computing*, 32(4), pp. 857-876.

Metropolis, N., Rosenbluth, A.W., Rosenbluth, M.N., Teller, A.H. and Teller, E. (1953) 'Equation of state calculations by fast computing machines', *Journal of Chemical Physics*, 21(6), pp. 1087-1092.

Morales-España, G., Latorre, J.M. and Ramos, A. (2013) 'Tight and compact MILP formulation for the thermal unit commitment problem', *IEEE Transactions on Power Systems*, 28(4), pp. 4897-4908.

Newell, A. and Simon, H.A. (1976) 'Computer science as empirical inquiry: symbols and search', *Communications of the ACM*, 19(3), pp. 113-126.

Padhy, N.P. (2004) 'Unit commitment: a bibliographical survey', *IEEE Transactions on Power Systems*, 19(2), pp. 1196-1205.

PUCSL (2026) *Electricity Generation Data Platform*. Public Utilities Commission of Sri Lanka. Available at: https://gendata.pucsl.gov.lk (Accessed: 27 July 2026).

Regulation (EU) 2024/1689 of the European Parliament and of the Council of 13 June 2024 laying down harmonised rules on artificial intelligence (Artificial Intelligence Act). *Official Journal of the European Union*, L series.

Rudin, C. (2019) 'Stop explaining black box machine learning models for high stakes decisions and use interpretable models instead', *Nature Machine Intelligence*, 1(5), pp. 206-215.

Schwartz, R., Dodge, J., Smith, N.A. and Etzioni, O. (2020) 'Green AI', *Communications of the ACM*, 63(12), pp. 54-63.

Strubell, E., Ganesh, A. and McCallum, A. (2019) 'Energy and policy considerations for deep learning in NLP', *Proceedings of the 57th Annual Meeting of the Association for Computational Linguistics*, Florence, pp. 3645-3650.

Taylor, J.W. (2003) 'Short-term electricity demand forecasting using double seasonal exponential smoothing', *Journal of the Operational Research Society*, 54(8), pp. 799-805.

Zhuang, F. and Galiana, F.D. (1990) 'Unit commitment by simulated annealing', *IEEE Transactions on Power Systems*, 5(1), pp. 311-318.
