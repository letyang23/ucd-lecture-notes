# ARE 107 Assignment 2

I. Using the “Advertising” dataset, which consists of data on sales of computers in different locations as well as advertising spending on TV, radio and newspaper. Run the sample R program (“Advertising Sample Program”) provided to you on canvas.

(a) Perform the following regressions:

​	(i) regress Sales on TV 

```R
regst<-lm(Sales~TV, data=mydata)
summary(regst)
```

- TV coefficient is 0.047537.

​	(ii) regress Sales on TV and Newspaper,

```R
regstn<-lm(Sales~TV+Newspaper, data=mydata)
summary(regstn)
```

- The coefficient between Sales and TV/Newspaper are 0.0469 and 0.0442 respectively.

(iii) regress Sales on TV , Newspaper and Radio.

```R
regstnr<-lm(Sales~TV+Newspaper+Radio, data=mydata)
summary(regstnr)
```

- The coefficient between Sales and TV/Newspaper/Radio are 0.045765, -0.001037 and 0.188530 respectively.



(b) Does the coefficient on TV change in regressions (i) and (iii)? Explain in detail. Perform any additional regressions to support your reasoning.

- When new variables, such as newspaper and radio, are added to a regression, the coefficients of the existing variables in the model may be affected. In this instance, the coefficient of TV in the second regression (0.046) is marginally smaller than the coefficient in the first regression (0.048), but the difference is negligible. This is likely because TV and newspaper/radio are correlated, and newspaper/radio can explain a portion of the sales variance that TV also explains. Nonetheless, because the association is imperfect, the coefficient of TV does not alter significantly.



(c) Does the coefficient on Newspaper change in regressions (ii) and (iii)? Explain in detail. Perform any additional regressions to support your reasoning.

- The coefficient on Newspaper change from 0.0442 to -0.001037. This suggests that an increase in Newspaper advertising is associated with a decrease in Sales, when we hold constant the effects of TV and Radio advertising.



##### II. Empirical Exercise: Instrumental Variables Regression

Using the “broiler” dataset, which is described in the lecture notes. Run the R program (“Broiler Program”) provided to you on canvas. 

In this question, you construct a table that is similar to the table “Estimating the slope of the Demand Curve for Broiler Chicken” in the Week 5 Lecture Notes, i.e. Column (1) is a short regression of quantity on price only, Column (2) is a long regression of quantity on price and some control variables (to be selected by you), Column (3) is the instrumental variables regression.

(a) In constructing the results in Columns (2) and (3), write down your reasoning for each variable you would like to include in the regression model for quantity demanded in addition to price.

- In order to control for omitted variable bias and provide a more precise assessment of the impact of price on quantity demanded, control variables should be included in column (2). Potential control variables for the demand for broiler chicken in the United States include disposable income, beef price, corn price, feed price, population, consumer price index, and meat exports. In column (3), an instrument variable for price, such as the price of broiler chicken in Brazil, can be used to control for endogeneity and produce a reliable estimate of the impact of price on quantity demanded.

(b) Explain why the slope of the regression of quantity on price (column (1) of your table) is positive.

- The slope of the regression of quantity on price (column 1) is expected to be negative, indicating that as the price of broiler chicken rises, the quantity demanded is expected to fall. This is due to the law of demand, which holds that when the price of an item or service increases, ceteris paribus, the amount desired will decrease. This is because consumers will purchase less of a product or service at a higher price because they are less willing or able to pay the higher price. In contrast, when prices are reduced, people tend to purchase more of the product or service. Thus, we anticipate that the slope of the regression between quantity and price will be negative.

(c) Explain in detail the difference between the coefficient on P CHICK in columns (1) and (2). Perform any additional regressions you may need to answer this question.

- The coefficient on PCHICK in column (1) indicates the slope of the demand curve for broiler chicken, assuming that price is the only factor influencing quantity demanded. In contrast, the PCHICK coefficient in column (2) represents the impact of price on quantity demanded after controlling for other variables. The difference between the two coefficients is likely due to the introduction of control variables in column (2), which can explain a portion of the quantity demanded fluctuation previously attributed to price alone.

(d) What are the instruments used to compute the coefficients in instrumental variable re- gression (Column (3))? Write down the R command used to obtain the IV estimates and explain in detail how the two stage least squares procedure is performed.

- The instrument utilized in the IV regression in column (3) is the price of broiler chicken in Brazil. In the first stage of the two-stage least squares (2SLS) technique, PCHICK is regressed on the instrument and other exogenous variables. In the second stage, Q is regressed on PCHICK and other exogenous factors using the fitted values as the instrument for PCHICK. This method addresses endogeneity and offers reliable and unbiased estimates of the effect of PCHICK on Q. R's ivreg function from the AER package is utilized by the IV estimation R command.

  ```R
  library(AER)
  ivreg(Q ~ PCHICK | PBRAZIL + Y + PBEEF + PCOR + PFEED + POP + CPI + MEX, data = broiler)
  ```



(e) Discuss how the inclusion of additional variables in the quantity equation affects the plausibility of the validity condition of the instruments you used in the previous question.

- If the variables are associated with the instrument or the error term, the validity of the instrument employed in the preceding inquiry may be compromised by the addition of new variables to the quantity equation. To evaluate the reasonableness of the validity criterion, we can examine the instrument's applicability and reliability. The IV estimates are reliable if the instrument is both relevant and valid.



(f) For each of the columns in your table, test whether the coefficient on P CHICK is
significant or not.

- To determine whether or not the coefficient on PCHICK is significant in each column of the table, t-tests can be performed on each coefficient. The null hypothesis states that the coefficient equals zero, while the alternative hypothesis states that the coefficient does not equal zero.
- The R command for the t-test of the coefficient on PCHICK in column (1) is:

```R
summary(lm(Q ~ PCHICK, data = broiler))$coefficients["PCHICK", c("Estimate", "Pr(>|t|)")]
```

- The R command for the t-test of the coefficient on PCHICK in column (2) is:

```R
summary(lm(Q ~ PCHICK + Y + PBEEF + PCOR + PFEED + POP + CPI + MEX, data = broiler))$coefficients["PCHICK", c("Estimate", "Pr(>|t|)")]
```

- The R command for the t-test of the coefficient on PCHICK in column (3) is:

```R
summary(ivreg(Q ~ PCHICK | PBRAZIL + Y + PBEEF + PCOR + PFEED + POP + CPI + MEX, data = broiler))$coefficients["PCHICK", c("Estimate", "Pr(>|t|)")]
```

- If the p-value is less than 0.05 in each example, we can reject the null hypothesis and conclude that the PCHICK coefficient is substantially different from zero at the 5% significance level. If the p-value is greater than 0.05, we cannot reject the null hypothesis and must conclude that the PCHICK coefficient is not substantially different from zero at the 5% significance level.



(g) Now estimate the first-stage regression of the IV estimation procedure. Are the coefficients on the instruments significant? Which of the assumptions of instrumental variables does this test? What are the implications of those results on your analysis.

- The first-stage regression of the IV estimation technique tests the instrument relevance assumption and offers evidence of the instrument's validity and relevance (PBRAZIL). A significant and positive coefficient on PBRAZIL is indicative of the instrument's suitability as a surrogate for PCHICK. The application of IV estimation to achieve consistent estimates of the effect of PCHICK on Q is supported by these results.

- ```R
  summary(lm(PCHICK ~ PBRAZIL + Y + PBEEF + PCOR + PFEED + POP + CPI + MEX, data = broiler))$coefficients
  ```



(h) Now suppose that PBEEF is also endogenous, how would you adjust your IV strategy? Describe which variables would be included in the first and second stage regression. Perform the IV estimation using the data accordingly. Report the estimation results in a table similar to the one you replicated in (a).

- If PBEEF is endogenous, we must find a new instrument for PBEEF and estimate the demand equation for broiler chicken using a two-stage least squares (2SLS) approach.

  One potential instrument for PBEEF may be the price of soybeans, as soybeans are used in the manufacturing of cattle feed and can influence the price of beef. Rather, we might utilize a separate instrument that is connected with PBEEF but not with the demand equation's error component.

  The first-stage regression would be:

  ```R
  lm(PBEEF ~ PSOYBEAN + Y + PCHICK + PCOR + PFEED + POP + CPI + MEX, data = broiler)
  ```

  where PSOYBEAN represents the instrument for PBEEF.

  The second-stage regression would be:

  ```R
  ivreg(Q ~ PCHICK + PBEEF_IV + Y + PCOR + PFEED + POP + CPI + MEX, data = broiler)
  ```

  where PBEEF_IV is the fitted value of PBEEF from the first-stage regression.

  The R commands to perform these regressions and report the results in a table similar to the one in part (a) are:

  ```R
  # First-stage regression
  fs_res <- summary(lm(PBEEF ~ PSOYBEAN + Y + PCHICK + PCOR + PFEED + POP + CPI + MEX, data = broiler))
  fs_coef <- fs_res$coefficients[,1:2]
  names(fs_coef)[2] <- "Std. Error"
  fs_coef
  
  # Second-stage regression
  ss_res <- summary(ivreg(Q ~ PCHICK + PBEEF_IV + Y + PCOR + PFEED + POP + CPI + MEX, data = broiler))
  ss_coef <- ss_res$coefficients[,1:2]
  names(ss_coef)[2] <- "Std. Error"
  ss_coef
  ```

  
