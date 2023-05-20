[toc]

# Midterm Review

## What is Empirical?

"Empirical Research"

- Empirical is ...
  1. Originating in or based on <u>observation</u> or <u>experience</u>
  2. Capable of being <u>verified</u> or <u>disproved</u> by observation or experiment

### How do we do Empirical Research?

We conduct empirical research through ...

- A program of inquiry conforming to the scientific method

The scientific method is ...

- (in general) The principles and procedures for the systematic pursuit of knowledge involving the recognition and formulation of a problem, the collection of data through observation and experiment, and the formulation and testing of hypotheses

### How HCI research differs from other fields?

1. Source of data
   - Many disciplines collect national data sets using established methodological controls
     - National data on income, employment, family
     - Statistics Canada, U.S. Census Bureau, EuroStat
     - Large sets are publicly available which researchers can analyze
   - Typically, HCI researchers must collect their own data
     - Leads to smaller size data sets
     - Or a combination of automated data collection (e.g. big data and/or text parsing) and smaller, more in-depth studies

1. How observations are made
   - Other disciplines such as sociology and medicine often track longitudinal data (data collection over time), over decades
   - HCI researchers rarely collect longitudinal data
     - Technological change may make it hard to compare devices over time
     - But other data points, such as time usage, communication patterns, or psychological well-being, are appropriate for longitudinal study
   - The lack of longitudinal data limits the impact of HCI research on other fields and populations

1. Who are the study participants
   - For many HCI research, ideally, you should have representative participants, not college students.
     - Representative in terms of:
       - Age
       - Educational experience
       - Technical experience
       - Domain knowledge/job experience
   - If you are evaluating something simple like motor performance, not usage patterns, then college students may be appropriate

### Understanding differences in methods and measurement

- Difference between micro-HCI and macro-HCI research (Shneiderman, 2011)
- Micro-HCI research
  - Task performance, time performance, error rate, time to learn, retention over time, user satisfaction
  - Frequently can be studied in a lab setting
- Macro-HCI research
  - Motivation, collaboration, social participation, trust, empathy, and other societal-level impacts
  - Often cannot be studied **only** in a lab setting

### A quick discussion of "research bias"

- Discuss with people sitting next to you
- What do we mean by "bias" in research?
  - Systematic error introduced into sampling or testing by selecting or encouraging one outcome or answer over others
- What are possible biases in HCI research?
  - Think about research design, data collection, and reporting

### Controlled laboratory studies vs. "in the wild" field studies

- Either type of research can come first, but both types of research should be done if possible
- Findings may differ inside the lab and outside
  - Field studies may be better for mobile device research
  - Lab studies may be better for evaluating novel interventions/designs

- Field studies help understand the context of users and their environments, e.g. outside noise and distractions, as well as network latencies
- Field studies may allow for more diverse users to participate
- Informed consent may also be harder in field settings

## Chapter 2: Experimental Research

### Types of behavioral research

- Descriptive investigations: constructing an accurate description of what is happening
- Relational investigations: 
  - identify relations between multiple factors
  - can rarely determine the causal relationship between multiple factors

- Experimental research: the establishment of causal relationship

### Types of behavioral research

Table 2.1 Relationship Between Descriptive Research, Relational Research, and Experimental Research

| Type of Research | Focus                                             | General Claims         | Typical Methods                                       |
| ---------------- | ------------------------------------------------- | ---------------------- | ----------------------------------------------------- |
| Descriptive      | Describe a situation or a set of events           | X is happening         | Observations, field studies, focus groups, interviews |
| Relational       | Identify relations between multiple variables     | X is related to Y      | Observations, field studies, surveys                  |
| Experimental     | Identify causes of a situation or a set of events | X is responsible for Y | Controlled experiments                                |

### Game playing and touch-typing

<img src="Midterm_2.assets/Screen Shot 2023-05-19 at 5.03.08 PM.png" alt="Screen Shot 2023-05-19 at 5.03.08 PM" style="zoom:50%;" />

### Types of Measurement

| Level of Measurement | Description                                                  | Example                        |
| -------------------- | ------------------------------------------------------------ | ------------------------------ |
| Nominal              | Arbitrary assignment of a code to an attribute, e.g., gender | 1= Male, 2= Female             |
| Ordinal              | Ordered categories, but not equal intervals between units, e.g., educational level | High school, College, Graduate |
| Interval             | Equal distance between units, but no absolute zero point, e.g., temperature | 20°C, 30°C, 40°C               |
| Ratio                | Absolute zero point, ratios are meaningful, e.g., typing speed | 20 wpm, 40 wpm, 60 wpm         |

**Use ratio measurements where possible.**

*See [Level of measurement](http://en.wikipedia.org/wiki/Level_of_measurement) for more information.*

### Example of Nominal Data in HCI

- Observe students "on the move" on university campus
- Code and count students by ...
  - Gender (male, female)
  - Mobile phone usage (not using, using)


| Gender | Mobile Phone Using | Mobile Phone Not Using | Total | % |
|--------------------|--------|-----------|-------|-------|
| Male               | 683    | 98        | 781   | 51.1% |
| Female             | 644    | 102       | 746   | 48.9% |
| Total              | 1327   | 200       | 1527  | 100%  |

### Interval Data

- Equal distances between adjacent values
- But, no absolute zero
- Classical example: temperature on the Fahrenheit or Celsius scale
- Statistical mean possible
  - E.g., the mean midday temperature during July

- Ratios not possible
  - Cannot say 10°C is twice 5°C


### Example of Interval Data in HCI

- Questionnaires solicit a level of agreement to a statement

- Responses on a Likert scale

- Likert scale characteristics:

  1. Responses are symmetric about a neutral middle value
  2. Gradations between responses are equal (more-or-less)

- Assuming "equal gradations," the statistical mean is valid (and related statistical tests are possible)

Form vs. Function

```
Aesthetically "cool" but...
- Expensive
- Awkward to use
- Difficult to wash
- Seeds mix with juice
- Hard to store

Aesthetically "bland" but...
- Inexpensive
- Simple to use
- Easy to wash
- Seeds separated from juice
- Easy to store
```

Research Questions

- We conduct empirical research to answer (and raise) "research questions"
- Consider the following questions:
  - Is it viable (workable)?
  - Is it as good as or better than what's available now?
  - Which of several design alternatives is best?
  - What are its performance limits and capabilities?
  - What are its strengths and weaknesses?
  - Does it work well for novices, for experts?
  - How much practice is required to become proficient?

### Testable Research Questions

- Previous questions, while unquestionably relevant, not all of them are testable
- Try to re-cast as testable questions (even though the new question may appear less important)
  - Scenario: You have invented a new text entry technique for mobile phones, and you think it's pretty good. In fact, you think it is better than the commonly used multi-tap technique. You decide to undertake a program of empirical inquiry to evaluate your invention. What are your research questions?

### A Tradeoff

```
If error rates are kept
under 2%, is the new
High technique faster than
multi-tap within one
hour of use? Accuracy of
Answer Is the new Low technique better
than multi-tap?
Narrow Broad

 Internal validity
 \ Breadth of Question

 External validity
```

### Internal Validity

- Definition: The extent to which the effects observed are due to the test conditions (e.g., multitap vs. new).
- Statistically...
  - differences (in the means) are due to inherent properties of the test conditions.
  - Variances are due to participant individual differences ("predispositions").
  - Other potential sources of variance are controlled or exist equally and randomly across the test conditions.

### External Validity

- Definition: The extent to which results are generalizable to other people and other situations.
- Statistically...
  - the participants are representative of the broader intended population of users.
  - Situations: The test environment and experimental procedures are representative of real-world situations where the interface or technique will be used.

### Experimental Design Terminology

- Terms to know:
  - Participant
  - Independent variable (test conditions)
  - Dependent variable
  - Control variable
  - Random variable
  - Confounding variable

### From Measurements to Variables

- Dependent variables (DV):
  - The outcome or effect of interest.
  - Dependent on a participant's behavior or the changes in the independent variables (IVs).
  - Usually the outcomes that the researchers need to measure.
- Independent variables (IV):
  - The factors of interest or the possible "cause" of the change in the dependent variable.
  - Independent of a participant's behavior.
  - Usually the treatments or conditions that the researchers can control.

### Independent Variable

- How to identify IVs? Better to go with theories or well-thought-out reasons.
- <u>Avoid</u> research questions like "what are the factors Xs that influence Y?"
  - Logically speaking, the number of factors that make a (tiny) influence is infinite, not enumerable.
  - Typically not a very interesting question to HCI research.
  - No appropriate motivations, hypotheses, or reasons to do so.
  - We'd rather know very deeply how one key factor X influences Y (e.g., how keyboard layout influences typing speed).

### Test Conditions

- The levels, values, or settings for an independent variable are the test conditions.
- Provide a name for the <u>factor</u> (independent variable).
- Provide names for the <u>levels</u> (test conditions).
- Use these names consistently throughout the manuscript.
- For any experiments, you need to have at least two levels.
- Examples:
  - Factor: Device
    - Test Conditions (Levels): mouse, trackball, joystick
  - Factor: Feedback mode
    - Test Conditions (Levels): audio, tactile, none
  - Factor: Task
    - Test Conditions (Levels): pointing, dragging
  - Factor: Visualization
    - Test Conditions (Levels): 2D, 3D, animated
  - Factor: Search interface
    - Test Conditions (Levels): Google, custom

### Dependent Variable

- A dependent variable is a variable representing the measurements or observations.
- Examples include task completion time, speed, accuracy, error rate, throughput, target re-entries, task retries, presses of backspace, etc.
- Give a name to the dependent variable, separate from its units (e.g., "Text Entry Speed" is a dependent variable with units "words per minute").

### Three "Other" Variables

- Important but usually given less attention are:
  - Control variables
  - Random variables
  - Confounding variables

### Practice Activity: "Evaluate the Non-Evaluable"

- Discuss in groups.
- Watch the video of Touch & Activate, one of the newest and coolest inventions at UIST 2013: [Video Link](https://www.youtube.com/watch?v=Xgxxi6w8lQc).
- Propose an experimental study, state what the research question you're about to answer, and then figure out what's your independent variable, dependent variable, control variable, random variable, etc.

### Key Consideration: Should every participant use every alternative?

### Example: Which vacuum cleaner is more effective?

### Between vs. within subjects

- Within subjects:
  - All participants try all conditions.
  - Pros: Can isolate the effect of individual differences, requires fewer participants.
  - Cons: Ordering (learning, carry-over, etc.) and fatigue effects.
- Between subjects:
  - Each participant tries one condition.
  - Pros: No ordering effects, less fatigue.
  - Cons: Cannot isolate effects due to individual differences, need more participants.

### Counterbalancing

- For within subjects designs, participants' performance may improve with practice as they progress from one test condition to the next. Thus, participants may perform better on the second condition simply because they benefited from practice on the first. This is bad news.
- To compensate, the order of presenting conditions is counterbalanced.
- Participants are divided into **groups**, and a different order of administration is used for each group.
- **Ordering group**, then, is a between subjects factor.
- The order can be governed by a **Latin Square** when there are too many conditions to test.

### Counterbalancing

- Latin Square Design (Table 2.3):

| Order of administration | 1    | 2    | 3    | 4    |
| ----------------------- | ---- | ---- | ---- | ---- |
| Sequence 1              | A    | B    | C    | D    |
| Sequence 2              | B    | C    | D    | A    |
| Sequence 3              | C    | D    | A    | B    |
| Sequence 4              | D    | A    | B    | C    |

### Succinct Statement of Design

- "3 x 2 repeated-measures design" refers to an experiment with two factors, having three levels on the first and two levels on the second. There are six test conditions in total. Both factors are repeated measures, meaning all participants were tested on all test conditions.
- Note: A mixed design is also possible. In this case, the levels for one factor are administered to all participants (within subjects), while the levels for another factor are administered to separate groups of participants (between subjects).
- An example of a mixed design: [Mixed Design Example](http://www.yorku.ca/mack/uist01.html)

### Statistics

- Statistics: The science of collecting, describing, and interpreting data.
  - Typically considered as a branch of mathematics.
  - A way to describe a set of data and make inferences about a population.
- Two areas of statistics:
  - **Descriptive Statistics**: Collection, presentation, and description of sample data.
  - **Inferential Statistics**: Making decisions and drawing conclusions about populations.
- Some Basic Terms:
  - **Population**: A collection or set of individuals, objects, or events whose properties are to be analyzed.
  - **Sample**: A subset of the population.
  - **Variable**: A characteristic about each individual element of a population or sample.
  - **Statistic**: A numerical value summarizing the sample data.

### Descriptive or Inferential?

- ***Example***: A recent study examined the math and verbal SAT scores of high school seniors across the country. Which of the following statements are descriptive in nature and which are inferential?
  - The mean math SAT score was 492.
  - The mean verbal SAT score was 475.
  - Students in the Northeast scored higher in math but lower in verbal.
  - 80% of all students taking the exam were headed for college.
  - 32% of the students scored above 610 on the verbal SAT.
  - The math SAT scores are higher than they were 10 years ago.

### Descriptive Statistics

- Measures of central tendency (of a sample):
  - Mean
  - Median
  - Mode
- Measures of spread (of a sample):
  - Range
  - Variance
  - Standard deviations

### Measure and Variability

- No matter what the response variable is, there will always be **variability** in the data.
- One of the primary objectives of statistics is measuring and characterizing variability.
- Controlling (or reducing) variability in a manufacturing process: statistical process control.

### Significance Tests

- Why do we need significance tests?
  - When the values of the members of the comparison groups are all known, you can directly compare them and draw a conclusion. No significance test is needed since there is no uncertainty involved.
  - When the **population is large**, we can only sample a **sub-group of people** from the entire population.
  - Significance tests allow us to determine how confident we are that the results observed from the sampling population can be generalized to the entire population.

### Comparing means

- Summary of methods: Commonly Used Significance Tests for Comparing Means and Their Application.

| Context                    | Experiment                | Independent Variables (IV) | Test Method |
| -------------------------- | ------------------------- | -------------------------- | ----------- |
| Independent-samples t test | Between-group             | 2 or more                  | 2 or more   |
| Factorial ANOVA            | Between-group             | 2 or more                  | 2 or more   |
| Paired-samples t test      | Within-group              | 1                          | 3 or more   |
| Repeated measures ANOVA    | Within-group              | 1                          | 3 or more   |
| Repeated measures ANOVA    | Between- and within-group | 2 or more                  | 2 or more   |
| Split-plot ANOVA           | Between- and within-group | 2 or more                  | 2 or more   |

### Answering Research Questions

- We want to know if the measured performance on a variable (e.g., speed) is different between conditions, so:
  - We conduct a user study and measure the performance on each test condition with a group of participants.
  - For each test condition, we compute the mean score over the group of participants.
- Then, what's next?

### Answering Empirical Questions

- Four questions:
  1. Is there a difference?
  2. Is the difference large or small?
  3. Is the difference statistically significant (or is it due to chance)?
  4. Is the difference of practical significance?
- Question #1: Obvious (some difference is likely; means are not going to be exactly the same).
- Question #2: Statistics can't help (Is a 5% difference large or small?). Subject to human interpretation.
- Question #3: **Statistics can help.**
- Question #4: Statistics can't help (Is a 5% difference useful? People resist change!). Marketing and business vision.

---

- **Hypotheses**: For statistical inference, these are predictions about a population expressed in terms of parameters (e.g., population means or proportions or correlations) for the variables considered in a study.

- A significance test uses data to evaluate a hypothesis by comparing sample point estimates of parameters to values predicted by the hypothesis.
- We answer a question such as, "If the (null) hypothesis were true, would it be unlikely to get data such as we obtained?" i.e., is p (data | null hypothesis) small enough so that null hypothesis can be falsified?

### Five Parts of a Significance Test

1. **Assumptions** about type of data (quantitative, categorical), sampling method (random), population distribution (e.g., normal, binary), sample size (large enough?).
2. **Hypotheses**:
   - Null hypothesis (H0): A statement that parameter(s) take specific value(s) (Usually: "no effect").
   - Alternative hypothesis (H1): States that parameter value(s) falls in some alternative range of values (an "effect").
3. **Test Statistic**: Compares data to what null hypothesis H0 predicts, often by finding the standard errors between sample point estimate and H0 value of the parameter.
4. **P-value (P)**: A probability measure of evidence about H0. The smaller the P-value, the stronger the evidence against H0.
5. **Conclusion**:
   - If no decision is needed, report and interpret the P-value.
   - If a decision is needed, select a cutoff point (such as 0.05 or 0.01) and reject H0 if P-value ≤ that value.
   - The most widely accepted cutoff point is 0.05, and the test is said to be "significant at the 0.05 level" if the P-value ≤ 0.05.
   - If the P-value is not sufficiently small, we fail to reject H0 (then, H0 is not necessarily true, but it is plausible).
   - The process is analogous to the American judicial system:
     - H0: Defendant is innocent.
     - H1: Defendant is guilty.

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*

### Significance Test for Mean (t-test)

- Assumptions: Randomization, quantitative variable, normal population distribution.
- Null Hypothesis: H0: μ = μ0, where μ0 is a particular value for the population mean (for two conditions, μH1 = μH2, so μH1 - μH2 = 0).
  - Typically "no effect" or "no change" from a standard.
- Alternative Hypothesis: H1: μ ≠ μ0 (2-sided alternative includes both > and < μ0 value).
- Test Statistic: The number of standard errors that the sample mean falls from the H0 value.
  - t = (Ybar - μ0) / (s / sqrt(n))

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*

### Type I and Type II errors

- All significance tests are subject to the risk of Type I and Type II errors.
- A Type I error (also called an α error or a "false positive") refers to the mistake of rejecting the null hypothesis when it is true and should not be rejected.
- A Type II error (also called a β error or a "false negative") refers to the mistake of not rejecting the null hypothesis when it is false and should be rejected.

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*

### What is a survey?

- Is a survey the same thing as a questionnaire?
  - A questionnaire is the actual list of questions.
  - A survey is the complete methodological approach.
- But the two terms are often used interchangeably.

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*

### How to sample?

- Two major types of sampling methods:
  - Probabilistic sampling: Where there is a known probability of someone being chosen.
  - Non-Probabilistic sampling: It is not exactly known what the likelihood of being chosen is.
- Note that non-probabilistic sampling is considered valid in HCI research, although some social sciences are not as accepting of it.

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*

### Interaction Response size

- What response is considered to be sufficient for a random sample? It depends on the confidence level and margin of error that you consider acceptable.
- For instance, to get a 95% confidence level and ±5% margin of error, you need 384 responses.
- If the sample is large (5-10%) compared to the population size, the margin of error is smaller.
- This info is only relevant for random sampling in surveys, not for any other method or approach in HCI research.
- The reader is encouraged to consult (Muller et. al., 2014) for more information on sample sizes in HCI survey research.

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*

### Discussion: How would you study "non-users"?

- Imagine that you're interested in understanding people's perceived and subjective knowledge (a.k.a. mental model) about an electronic car that does auto-driving. The understanding obtained is supposed to inform the design/engineering team for their design decisions. You'd like to start with a survey study. How would you recruit your participants?

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*

### Re: SEARCH METHODS IN INTERACTION Developing survey questions

- The overall goal is to develop well-written, non-biased questions.
- Since most surveys are self-administered, the questions need to stand alone, without any explanations.
- You need to focus on both:
  - The overall structure of the entire survey.
  - The wording of specific questions.

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*

### Interaction Types of questions

- Types of survey questions include:
  - Open-ended response.
  - Closed response.
  - Semantic differential scales.
  - Agreement and rating scales.
  - Ranking scales.
  - Checklists.
- Also consider how you would analyze the responses.

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*

### Overall survey structure

- All surveys must begin with instructions.
  - On paper, should checkboxes and ovals be filled in, checked, or have an "X" placed in them?
  - Should all respondents fill out all questions?
- A reminder of who qualifies to participate and who does not.
- Each section of the survey should have a heading.
- What path through the survey should the respondent take?

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*

### Existing surveys from HCI

- There are some existing surveys that have been tested and validated in the HCI literature, primarily for usability testing and evaluation:
  - Computer System Usability Questionnaire.
  - Interface Consistency Testing Questionnaire.
  - Questionnaire for User Interaction Satisfaction.
  - Website Analysis and Measurement Inventory.
- See the book (p.124) for a detailed list of information on existing HCI surveys.

*Slides ©2017 Lazar, Feng, and Hochheiser, Creative Commons, Attribution-NonCommercial-ShareAlike 4.0 License*



|      | Visual Studio     | Eclipse           |                   |
| ---- | ----------------- | ----------------- | ----------------- |
| C#   | Mean: 4219.745614 | Mean: 2548.978912 | Mean: 3384.362263 |
| Java | Mean: 6905.884887 | Mean: 2263.375209 | Mean: 4584.630048 |
|      | Mean: 5562.815251 | Mean:2406.177061  | Mean: 3984.496156 |



|      | Visual Studio                        | Eclipse                             |                                     |
| ---- | ------------------------------------ | ----------------------------------- | ----------------------------------- |
| C#   | Standard deviation: 2298.3100200959  | Standard deviation: 2018.7185398514 | Standard deviation: 2318.7479571817 |
| Java | Standard deviation: 5321.4228064148  | Standard deviation: 1222.8078803046 | Standard deviation: 4504.9554853556 |
|      | Standard deviation:  4313.2030864591 | Standard deviation: 1675.0027451835 | Standard deviation: 3632.598064     |

