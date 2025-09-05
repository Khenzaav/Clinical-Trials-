Clinical-Trials-

Project Overview
This project analyzes a Kaggle dataset of 5,783 registered clinical trials to understand trends in trial design, enrollment, phases, and sponsorship. The workflow focused on data cleaning, exploratory data analysis (EDA), and statistical hypothesis testing, without building predictive models. Cleaning steps standardized text fields, harmonized date formats, handled missing values with median-based imputations, and normalized sponsor categories. EDA explored trial distributions across statuses, phases, and years, while survival analysis estimated trial durations. Finally, statistical tests validated patterns such as enrollment differences across phases, statuses, and sponsor types. Overall, the project demonstrates a reproducible workflow for preparing, exploring, and testing insights from complex healthcare datasets.

Dataset Description
Source: Kaggle
Shape: 5,783 rows × 27 columns
Each row = one registered clinical trial
Key variables:
nct_number: unique trial ID
status: trial status (Completed, Recruiting, etc.)
phases: trial phase (I–IV, N/A)
enrollment: participant counts
conditions: medical conditions studied
sponsor_collaborators: sponsor information
start_date, completion_date: timeline variables
Methodology
Data Cleaning
• Data cleaning was an essential step to prepare the clinical trials dataset for analysis. The following transformations and imputations were performed:

Standardizing Column Names • All column names were converted to lowercase and special characters/spaces were replaced with underscores using a custom canon() function. • Example: "Primary Completion Date" → primary_completion_date.
Removing Unnecessary Columns • Dropped identifiers or metadata columns not required for analysis: o url, other_ids → redundant identifiers. o results_first_posted, study_documents → >95% missing values, not useful.
Date Parsing & Cleaning • Date columns (start_date, primary_completion_date, completion_date, first_posted, last_update_posted) came in mixed formats (9-Nov-20, May-21, 2020-11-09). • Applied a robust parser (pd.to_datetime with errors="coerce" and infer_datetime_format=True) to standardize formats. • Checked invalid or unparsed values — mostly missing (NaT).
Handling Missing Dates (Median-Based Imputation) • Defined two key intervals: o Gap1: time (days) from start_date → primary_completion_date (median = ~189 days). o Gap2: time from primary_completion_date → completion_date (median = ~19 days). • Used these median gaps to impute missing dates: o If start_date present but primary_completion_date missing → add median gap. o If primary_completion_date present but completion_date missing → add median gap. o Symmetric rules applied when completion_date was present but primary_completion_date missing.
Standardizing Text Columns • Converted categorical/text fields to lowercase and stripped whitespace: o title, status, conditions, interventions, sponsor_collaborators, funded_bys, study_type, study_designs, outcome_measures, locations. • Split multi-value fields (study_designs, conditions, interventions) on | into Python lists for easier analysis.
Sponsor Normalization • Many sponsor names were long, inconsistent, or contained multiple organizations. • Split sponsors on | and normalized. • Created a new categorical column sponsor_type by grouping: o "University", "Hospital", "Industry", "Government/NGO", "Other". • Distribution: University (2,630), Other (1,498), Hospital (827), Industry (533), Government/NGO (295).
Gender Standardization • Cleaned gender values: o Converted to lowercase. o Replaced "both" and "na" with "all".
Duplicate Removal • Dropped duplicate rows based on the unique trial identifier (nct_number).
Removal of Extra Temporary Columns • Columns created during intermediate steps (gap1, gap2, start_year, completion_year) were dropped to keep the final dataset clean.
Exploratory Data Analysis (EDA)
• EDA was conducted to uncover patterns, assess data quality, and generate domain-specific insights. The following steps were performed:

Univariate Analysis
• Count plots and histograms were used to explore distributions of categorical and numeric variables.
• Key fields analyzed: study_type, status, phase, gender, enrollment.
Bivariate & Multivariate Analysis
• Relationships between variables were visualized using boxplots, bar charts, and scatter plots.
• Examples:
o enrollment vs phase (boxplot)
o status by phase (stacked bar chart)
o completion_rate by phase (bar chart)
Time-Based Trends
• Extracted startyear and completion year from parsed dates.
• Analyzed trial activity over time using line plots and scatter plots.
• Compared trials started vs completed per year to identify operational gaps.
Survival Analysis
• Kaplan-Meier curves were generated to estimate trial duration across phases.
• Used lifelines library to compute survival probabilities and confidence intervals.
Sponsor & Intervention Profiling
• Aggregated and visualized top sponsors and interventions using horizontal bar charts.
• Grouped sponsors by type (University, Hospital, etc.) for categorical insights.
Enrollment Diagnostics
• Explored enrollment distribution using histograms and boxplots (linear and log scale).
• Identified outliers and skewness to inform modeling and imputation strategies.
Completion Rate Calculation
• Defined completion rate as:
completionrate = completedtrials / totaltrialsper_phase
• Visualized phase-wise completion rates to assess trial success likelihood.
Correlation Analysis
• Generated heatmap of numeric features (age, income, education, etc.)
• Assessed feature relationships and checked for multicollinearity.
Visual Styling & Annotation
• Applied consistent styling using seaborn and matplotlib.
• Added titles, axis labels, and legends for clarity.
• Annotated key plots with insights to guide interpretation.
Statistical Testing
• A series of hypothesis tests were conducted to validate whether observed patterns are statistically significant.

Trials Started vs. Completed per Year Tested: Whether the number of trials started differs significantly from the number completed each year. Method: Paired t-test (with normality check); fallback Wilcoxon test. Result: p > 0.05 → Fail to reject H₀. On average, yearly counts of trials started ≈ completed. Strong correlation (r ≈ 0.70, p < 0.001) indicates trends move together.

Enrollment vs. Trial Status Tested: Whether participant enrollment distributions differ across trial statuses. Method: Kruskal–Wallis H-test (non-parametric, skewed data). Result: p ≪ 0.05 → Reject H₀. Enrollment significantly differs by status. Post-hoc Dunn test showed largest differences between Completed, Withdrawn, and Terminated vs. active categories.

Enrollment vs. Trial Phase Tested: Whether later-phase trials (Phase 3–4) have higher enrollments than early-phase trials (Phase 1–2). Method: Kruskal–Wallis H-test with post-hoc Dunn comparisons. Result: p ≪ 0.05 → Reject H₀. Strong evidence that trial enrollment increases with later phases.

Enrollment vs. Sponsor Type Tested: Whether trial enrollment differs by sponsor type (University, Hospital, Industry, Government/NGO). Method: Kruskal–Wallis test with post-hoc Dunn test. Result: p ≪ 0.05 → Reject H₀. Industry- and Government/NGO-sponsored trials have significantly higher enrollments compared to Universities, Hospitals, and “Other.” However, Universities, Hospitals, and Other sponsors do not differ much among themselves.

Conclusion
This study confirms that trial phase, sponsor type, and status strongly influence enrollment size and trial duration. Later-phase and industry/government-sponsored studies operate on larger scales, while early-phase and withdrawn trials remain small. The findings reflect structured differences in trial design rather than random variation, aligning with the medical research pipeline.

Future Work
I plan to extend this research by: Conducting disease-specific subgroup analyses (e.g., oncology vs. cardiology trials). Applying machine learning models to predict enrollment size from trial metadata. Studying geographic disparities in trial distribution. Expanding to temporal analysis of post-COVID research growth.
