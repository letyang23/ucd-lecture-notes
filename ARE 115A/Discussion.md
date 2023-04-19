# Discussion #2 4/12

#### Human Development Index (HDI)

- combines three of these dimensions into a single index.
- Long and healthy life (Life Expectancy Index) + Knowledge (Education Index) + A decent standard of living (GNI Index)

- Life Expectancy $I(LE)$
- Education $I(E)$
- Income $I(GNI)$

$HDI_i = \sqrt[3]{I(GNI_i)\times I(E_i) \times I(LE_i)}$

The HDI will range from 0 to 1 since each index range from 0 to 1.

HDI = 0 if at least one is 0, and HDI = 1 if all three are 1.

##### Calculating Each Index

- Each index: $I_i = \frac{X_i-min(X)}{max(X)-min(X)}$
- $Xi$ is the actual value of the **indicator** for country i.

##### Careful with Max!

If Xi > max(X), Xi should be assign the value of max(X)



#### Income Index

$I(GNI_i) = \frac{ln(GNI_i - ln[min(GNI)])}{ln[max(GNI)] - ln[min(GNI)]}$



#### Education Index

- EYSCi = Expected Years of Schooling for Children: on avg, how many years of education will children in country i complete?
- MYSAi = Mean Years of Schooling of Adults: on avg, how many years of education have adults in country i completed?



$I(Ei) = \frac{[I(EYSC_i) + I(MYSA_i)]}{2}$

$I(EYSC_i) = \frac{EYSCi - min(EYSC)}{max(EYSC)- min(EYSC)}$

$I(MYSA_i) = \frac{MYSA_i - min(MYSA)}{max(MYSA)- min(MYSA)}$



GEOMEAN()

AVERAGE()

LN()

MIN(value1, value2, ...)