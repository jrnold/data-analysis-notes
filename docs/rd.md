
# Regression Discontinuity

Summary: If there are thresholds whereby some observations receive the
treatment above it, other those below it do not, and those immediately above or
below that threshold are similar, we can use the difference of the outcome
between those just above and those just below the threshold to estimate the
causal effect of the treatment.

Suppose there is a running variable $x$ such that any person receives the treatment, $d$ if $x \geq a$ and does not if $x \leq a$,
$$
d = \begin{cases}
1 & x \geq a \\
0 & x < a
\end{cases}
$$

A simple regression discontinuity model is,
$$
\begin{aligned}[t]
y_i = \alpha + \beta x_i + \tau d_i + \gamma x_i d_i + \epsilon_i
\end{aligned}
$$
The local causal effect of the treatment at the discontinuity is $\tau$.

<div class="figure">
<img src="rd_files/figure-html/unnamed-chunk-2-1.svg" alt="Fake Example of a Regression Discontinuity. The difference at the threshold (50) is the effect of the treatment." width="672" />
<p class="caption">(\#fig:unnamed-chunk-2)Fake Example of a Regression Discontinuity. The difference at the threshold (50) is the effect of the treatment.</p>
</div>

However, there are several choices

-   Functional form of the trends before and after the discontinuity
-   The size of the window of observations before and after the trend which to compare.

How to choose?

-   parametric: chooses specific functional forms
-   non-parametric: uses flexible forms, and chooses a bandwidth [@ImbensKalyanaraman2011a]

Sharp vs. Fuzzy Discontinuity?

-   Sharp: the assignment of the treatment occurs with certainty at the threshold.
-   Fuzzy: the assignment of the treatment occurs only probabilistically at the threshold.

Suppose that the causal effect of treatment $T \in \{0, 1\}$ on unit $i$ is $\tau_i = Y_i(1) - Y_i(0)$ where $Y_i(1)$ is the potential outcome of $i$ under the treatment and $Y_i(0)$ is the potential outcome of $i$ under the control.
If potential outcomes are distributed smoothly at the cut-point $c$, then the average causal effect of the treatment at the cut-point, $Z_i = c$:
$$
\tau_{RD} = \E[Y_{i}(1) - Y_i(0)| Z_i = c] = \lim_{Z_i \downarrow c}\E[Y_{i}(1) | Z_i = c] - \lim_{Z_i \uparrow c}\E[Y_i(0)| Z_i = c]
$$

An advantage of RD designs is that unlike selection on observables or IV, its identifying assumptions are more observable and testable.

There are two basic tests (@LeeLemieux2010a):

1.  Continuity of pre-treatment covariates. E.g. density test of McCrary (2008). Whether the ratio of treated to control units departs from chance.
    A difficulty is that balance only holds in the limit, and covariance balance may still be present in finite samples.

1.  Irrelevance of covariates to the treatment-outcome relationship. There should be no systematic association between covariates and treatment, so controlling for them shouldn't affect the estimates.

## Examples

-   @ThistlethwaiteCampbell1960a was the first example of RD.

    -   Outcome: Career choices in teaching  
    -   Running variable: PSAS scores
    -   Cutoff: receiving National Merit Finalist
    -   Discussed: @AngristPischke2014a [Ch 4]

-   @CarpenterDobkin2011a, @CarpenterDobkin2009a

    -   Running variable: age
    -   Cutoff: ability to drink alcohol legally
    -   Outcome: Death, accidents
    -   Discussed: @AngristPischke2014a [Ch 4]

-   @AbdulkadirogluAngristPathak2014a

    -   Running variable: exam score
    -   Cutoff: above threshold receive an offer from a school. This is fuzzy since not all those who receive the offer attend.
    -   Outcome: Educational outcomes
    -   Discussed: @AngristPischke2014a [Ch 4]

-   @EggersHainmueller2009a

    -   units: UK MPs
    -   outcome: personal wealth
    -   treatment: winning an election (holding office)
    -   running variable: vote share

-   @LitschigMorrison2013a

    -   units: Brazilian municipalities
    -   outcome: education, literacy, poverty rate
    -   treatment: receiving a cash transfer from the central government (there are population cutoffs)
    -   running variable: population

-   @GelmanHill2007a [p. 213-217]

    -   units: US Congressional members
    -   outcome: ideology of representative
    -   treatment: winning election
    -   running variable: vote share

-   @GelmanKatz2007a, @GelmanHill2007a [p. 232]

    -   units: patients
    -   outcome: length of hospital stay
    -   treatment: new surgery method
    -   cutoff: not performed on those over 80
    -   running variable: age

-   @LeeMorettiButler2004a. Also see derived examples in @Bailey2016a [Ex. 6.3]. See @Button2015a for a replication.

    -   units: congressional districts
    -   outcome: ideology of nominees
    -   treatment: election
    -   running variable: vote share

-   @JacobLefgren2004a

    -   units: students
    -   outcome: education achievement
    -   treatment: summer school, retention
    -   running variable: standardized test

## Example: Close Elections

A common use of RD in political science and econ is election outcomes.
In this case the "treatment" is winning the election; it is applied to the candidate whose vote exceeds the threshold of 50%, but not to candidates arbitrarily below that threshold.
Thus "close" elections are a common use of RD designs.
This design was formalized in @Lee2008a.

Several papers question whether close elections satisfy the assumptions of RD:

-   @CaugheySekhon2011a look at US House elections (1942-2008). They find that close elections are more imbalanced. They attribute this to national partisan waves.
-   @GrimmerHershFeinsteinEtAl2011a look at all US House elections 1880-2008. They find that structurally advantaged candidates (strong party, incumbents) are more likely to win close elections.

The ways in which close elections can be non-random are lawsuit challenges and fraud.

@EggersFowlerHainmuellerEtAl2014a addresses these concerns with a systematic review of 40,000 close elections:  "U.S. House in other time periods, statewide, state legislative, and mayoral races in the U.S. and national or local elections in nine other countries"
Only the US House appears to have these issues.

## Software

See the R packages

-   **[rddtools](https://cran.r-project.org/package=rddtools)**: a new and fairly complete package of regression discontinuity from primary data viz to other tests.
-   **[rdd](https://cran.r-project.org/package=rdd)**
-   **[rdrobust](https://cran.r-project.org/package=rdrobust)**: Tools for data-driven graphical and analytical statistical inference in RD.
-   **[rdpower](https://cran.r-project.org/package=rdpower)**: Calculate power for RD designs.
-   **[rdmulti](https://cran.r-project.org/package=rdmulti)**: Analyze designs with multiple cutoffs.

See entries in the [Econometrics](https://cran.r-project.org/web/views/Econometrics.html) task view.

## References

Textbooks and Reviews:

-   @AngristPischke2014a [Ch. 4]
-   @GelmanHill2007a [Sec. 10.4]
-   @Bailey2016a [Ch. 11]
-   @LindenAdamsRoberts2006a for applications to medicine
-   @HahnToddKlaauw2001a An early review of RD in economics

Methods:

-   @ImbensKalyanaraman2011a propose an optimal bandwidth selection method
