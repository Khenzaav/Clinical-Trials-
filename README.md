### Clinical-Trials-

#### Project Overview
This project analyzes a Kaggle dataset of 5,783 registered clinical trials to understand trends in trial design, enrollment, phases, and sponsorship. The workflow focused on data cleaning, exploratory data analysis (EDA), and statistical hypothesis testing, without building predictive models. Cleaning steps standardized text fields, harmonized date formats, handled missing values with median-based imputations, and normalized sponsor categories. EDA explored trial distributions across statuses, phases, and years, while survival analysis estimated trial durations. Finally, statistical tests validated patterns such as enrollment differences across phases, statuses, and sponsor types. Overall, the project demonstrates a reproducible workflow for preparing, exploring, and testing insights from complex healthcare datasets.

#### Dataset Description
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
#### Methodology
##### 1. Data Cleaning
Data cleaning was an essential step to prepare the clinical trials dataset for analysis. The following transformations and imputations were performed:
Standardizing Column Names
All column names were converted to lowercase, and special characters/spaces were replaced with underscores using a custom canon() function.
Example: "Primary Completion Date" → primary_completion_date.
Removing Unnecessary Columns
Dropped identifiers or metadata columns not required for analysis:
url, other_ids → redundant identifiers
results_first_posted, study_documents → >95% missing values
Standardizing Date Columns
Columns like start_date, primary_completion_date, completion_date, first_posted, last_update_posted had mixed formats (e.g., 9-Nov-20, May-21, 2020-11-09). A robust parser (pd.to_datetime with errors="coerce" and infer_datetime_format=True) was applied to standardize formats.
Invalid or unparsed values were mostly missing (NaT).
Handling Missing Dates (Median-Based Imputation)
Two key intervals were defined:
Gap1: start_date → primary_completion_date (median ≈ 189 days)
Gap2: primary_completion_date → completion_date (median ≈ 19 days)
Missing dates were imputed using these median gaps:
If start_date present but primary_completion_date missing → add median gap
If primary_completion_date present but completion_date missing → add median gap
Symmetric rules applied when completion_date was present but primary_completion_date missing
Standardizing Text Columns
Categorical/text fields were converted to lowercase and stripped of whitespace:
title, status, conditions, interventions, sponsor_collaborators, funded_bys, study_type, study_designs, outcome_measures, locations.
Multi-value fields (study_designs, conditions, interventions) were split on | into Python lists for easier analysis.
Sponsor Normalization
Long, inconsistent sponsor names were split on | and normalized.
A new categorical column sponsor_type was created by grouping:
University, Hospital, Industry, Government/NGO, Other
Distribution:
University: 2,630
Other: 1,498
Hospital: 827
Industry: 533
Government/NGO: 295
Gender Standardization
Converted to lowercase
Replaced "both" and "na" with "all"
Duplicate Removal
Dropped duplicate rows based on the unique trial identifier nct_number.
Removal of Temporary Columns
Columns created during intermediate steps (e.g., gap1, gap2, start_year, completion_year) were dropped to keep the final dataset clean.
##### 2. Exploratory Data Analysis (EDA)
EDA was conducted to uncover patterns, assess data quality, and generate domain-specific insights.
Univariate Analysis
Count plots and histograms explored distributions of categorical and numeric variables.
Key fields analyzed: study_type, status, phase, gender, enrollment.
Bivariate & Multivariate Analysis
Relationships between variables visualized using boxplots, bar charts, and scatter plots.
Examples:
Examples:
enrollment vs phase (boxplot)
status by phase (stacked bar chart)
completion_rate by phase (bar chart)
Time-Based Trends
Extracted start_year and completion_year from parsed dates.
Analyzed trial activity over time with line and scatter plots.
Compared trials started vs completed per year to identify operational gaps.
Survival Analysis
Kaplan-Meier curves estimated trial duration across phases.
lifelines library was used to compute survival probabilities and confidence intervals.
Sponsor & Intervention Profiling
Aggregated and visualized top sponsors and interventions using horizontal bar charts.
Grouped sponsors by type (University, Hospital, etc.) for categorical insights.
Enrollment Diagnostics
Explored enrollment distribution using histograms and boxplots (linear and log scale).
Identified outliers and skewness to inform modeling and imputation strategies.
Completion Rate Calculation
Defined completion rate as:
completion_rate = completed_trials / total_trials_per_phase
Visualized phase-wise completion rates to assess trial success likelihood.
Correlation Analysis
Generated heatmap of numeric features (e.g., age, income, education).
Assessed feature relationships and checked for multicollinearity.
Visual Styling & Annotation
Consistent styling applied using seaborn and matplotlib.
Titles, axis labels, legends, and key plot annotations added for clarity.
###### 3. Statistical Testing
A series of hypothesis tests validated whether observed patterns were statistically significant:
Trials Started vs Completed per Year
Method: Paired t-test (fallback Wilcoxon test)
Result: p > 0.05 → Fail to reject H₀. Yearly counts of trials started ≈ completed.
Strong correlation (r ≈ 0.70, p < 0.001) indicates trends move together.
Enrollment vs Trial Status
Method: Kruskal–Wallis H-test (non-parametric) + post-hoc Dunn test
Result: p ≪ 0.05 → Reject H₀. Enrollment differs significantly by status, especially between Completed, Withdrawn, Terminated vs active trials.
Enrollment vs Trial Phase
Method: Kruskal–Wallis H-test + post-hoc Dunn test
Result: p ≪ 0.05 → Reject H₀. Later-phase trials (Phase 3–4) have higher enrollments.
Enrollment vs Sponsor Type
Method: Kruskal–Wallis H-test + post-hoc Dunn test
Result: p ≪ 0.05 → Reject H₀. Industry- and Government/NGO-sponsored trials have higher enrollments than Universities, Hospitals, or “Other.” Universities, Hospitals, and Other sponsors show similar enrollments.
#### Conclusion
Trial phase, sponsor type, and status strongly influence enrollment size and trial duration.
Later-phase and industry/government-sponsored studies operate on larger scales, while early-phase and withdrawn trials remain small.
These findings reflect structured differences in trial design, not random variation, aligning with the medical research pipeline.
#### Future Work
Conduct disease-specific subgroup analyses (e.g., oncology vs cardiology trials)
Apply machine learning models to predict enrollment size from trial metadata
Study geographic disparities in trial distribution
Expand temporal analysis to assess post-COVID research growth
If you want, I can also convert this into a fully Markdown-ready GitHub README with table of contents, badges, and collapsible sections to make it look professional and easy to navigate. Do you want me to do that?
