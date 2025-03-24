# Types of Statistics

## Descriptive Statistics
* Involves methods for summarizing and organizing data to make it understandable.
* Used in studies

### Measure of Central Tendency
#### Mean 
#### Median
#### Mode
<br>
### Measure of Dispersion
#### Variance

#### Standard Deviation
<br>
### Data Distribution
#### Histograms
#### Box Plot

### Pie Chart
#### PDF, PMF
<br>
### Summary Statistics
#### Five Number Summary
* Q1
* Q2
* Q3
* Max
* Mix
<br> <br>
## Inferential Statistics
* Makes inferences/conclusions from data
* involves methods for making predictions or inferences about a population based on a sample of data. 
* Allows for Hypothesis testing, estimation, and drawing conclusions. 

### Types of Sampling Techniques

#### Probability Sampling
* Simple random sampling
	* every member of the population has an equal chance of being selected
* Systematic sampling
	* select every nth member of the population after a random starting point
* Stratified sampling
	* Divide the population into strata (groups) based on specific characteristics and them randomly sample from each strata
		* EX: Divide employees by department then select a proportional number from each dept to form a survey sample
* Cluster Sampling
	* Divide the population into clusters, randomly select the clusters. Sample all the members in the selected clusters.
		* EX: Select several schools from a district and survey all teachers within selected schools. 
* Multi-Stage Sampling
	* Coming several sampling methods. Usually involves selecting clusters, then randomly sampling within those clusters.


<br>
#### Non-Probability Sampling
	Select individuals who are easier to reach
* Convenience Sampling
	* Selecting individuals who are easiest to reach
* Judgmental (Purposive) Sampling
	* Select individuals based on the researches judgment -- useful or representative
		* EX: Choose experts in a field to participate
* Snowball Sampling
	* Existing study subjects recruit future subjects from among their acquaintances.
		* EX: Survey members of a rare disease
* Quota Sampling
	* Age, group, gender, caste
<br>

### Hypothesis Testing
### P value
<br>
### Confidence Interval

<br>
### Statistical Analysis Test
#### Z-Test
#### T-Test
#### Anova Test / F Test

#### Chi Square Test

<br>


<br> <br>
# Types of Data


## Quantitative
	Numerical Data
### Discrete
	Whole numbers
	Must be positive
		EX: # of bank accounts / Children in family
### Continuous
	Any numerical value, can have deciman or be negative
	EX: Weights / float / temp / speed
<br>
## Qualitative
	Categorical Data
### Nominal
	Cannot be ranked
	EX: Gender, blood type, zip code
### Ordinal
	Can be ranked
	EX: Customer feedback (good, bad, mixed)

<br>
## Nominal
* This scale classifies data into distinct categories (Qualitative/Categorial) that do now have an intrinsic order
* Data is categorized based on labels, names, or qualities
* Categories are mutually exclusive
* No logical order among categories (no rank)
## Ordinal
* This Scale classifies the data into categories that can be ranked or ordered
* Data is categorized and ranked in a specific order
* The interval between ranks are not necessarily equal
	* EX: Education level
		* HS, BA, MA, PHD
## Interval
* Categorizes, orders, and specify the exact difference between intervals.
* Cannot be zero
* EX: Years: 2000, 2005, 2010
	* Temperature: 10, 20, 30, 40
## Ratio
* Is often ratiod/divided and output can be organized/compared
* Order matters
* Differences are mesaureable
* Can contain 0
* EX: Student score in class /100
	* 0,90,60,30,75,45


<br> <br>
# Common Calculations

## Variance
	Average of squared deviations from mean
	Measures data spread/dispersion
	Squares differences to eliminate negatives
	Quantifies average distance from central tendency

	σ² = Σ(x - μ)²/n
		Calculate mean (μ)
		Find differences (x - μ)
		Square differences
		Sum squares
		Divide by n (population) or n-1 (sample)
		
	Properties
		Always non-negative
		Units are squared (meters → meters²)
		Sensitive to outliers
		Zero only when all values identical
	Population vs. Sample
		Population: denominator is n
		Sample: denominator is n-1 (Bessel's correction)
		n-1 provides unbiased population estimate
		
	Relationship to Standard Deviation
		Standard deviation = √variance
		Returns to original units
		More intuitive for interpretation
		In normal distributions: 68-95-99.7 rule
		
	Applications
		Risk assessment
		Data comparison
		Statistical testing (ANOVA, F-test)
		Portfolio theory
		Quality control
		Experimental design

## Standard Dev
	Square root of variance
	Measures data dispersion in original units
	Most common measure of statistical dispersion
	Symbol: σ (population) or s (sample)

	Formula: σ = √(Σ(x - μ)²/n)
		Calculate mean (μ)
		Find differences from mean (x - μ)
		Square differences
		Average squares
		Take square root

	Properties
		Same units as original data
		Always non-negative
		Increases with data spread
		Zero only when all values identical
		Less sensitive to outliers than variance (but still affected)

	Population vs. Sample
		Population: denominator is n
		Sample: denominator is n-1 (Bessel's correction)
		Sample standard deviation slightly larger than population standard deviation

	Normal Distribution Applications
		68% of data falls within ±1σ
		95% of data falls within ±2σ
		99.7% of data falls within ±3σ (Empirical Rule)

	Practical Uses
		Comparing variability across datasets with different units
		Determining confidence intervals

## 5 Number Summary
	Minimum: The smallest value in the dataset
	Q1 (First quartile): The 25th percentile
	Median (Second quartile): The 50th percentile
	Q3 (Third quartile): The 75th percentile
	Maximum: The largest value in the dataset
This summary is often visualized with a box-and-whisker plot, where the box represents Q1, median, and Q3, while the whiskers extend to the minimum and maximum (or to 1.5 × IQR from the quartiles, with outliers shown as points).
![[Pasted image 20250323162651.png]]

## 2


<br>
# 1