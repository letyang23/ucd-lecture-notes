# ARE 107 Assignment 3

**I. Empirical Exercise: Shall-Carry Laws and Violent Crime Rates.** Using the Guns Data and the Guns P rogram provided to you, replicate the two tables on pages 24 and 28 in Lecture 12.

(a) Write down the regression equation as well as the R commands to obtain each column of the table on p. 24.

- For Table 1 on page 24, the R commands to obtain each column are:

- Column 1:

  - ```R
    regpols<-lm(vio~shall,data=mydata)
    ```

- Column 2:

  - ```R
    regfes<-lm(vio~shall+factor(stateid),data=mydata)
    ```

- Column 3:

  - ```R
    regfesy<-lm(vio~shall+factor(year)+factor(stateid),data=mydata)
    ```

- Column 4:

  - ```R
    regfesyt<-lm(vio~shall+factor(year)+factor(stateid)+factor(stateid):year,data=mydata)
    
    ```

- Column 5:

  - ```R
    regfesytmulti<-lm(vio~shall+density+avginc+incarc_rate+pm1029+factor(year)+factor(stateid),data=mydata)
    ```

- The regression equations for each column can be written as:
  1. vio = β0 + β1 * shall
  2. vio = β0 + β1 * shall + state fixed effects
  3. vio = β0 + β1 * shall + state fixed effects + year fixed effects
  4. vio = β0 + β1 * shall + state fixed effects + year fixed effects + state-year interactions
  5. vio = β0 + β1 * shall + β2 * density + β3 * avginc + β4 * incarc_rate + β5 * pm1029 + state fixed effects + year fixed effects

(b) Write down the regression equation as well as the R commands to obtain each column of the table on p. 28.

- Column 1:

  - ```R
    pregpols <- plm(vio~shall, model="pooling", data=mydatap)
    coeftest(pregpols, vcov=rvpols)
    ```

- Column 2:

  - ```R
    pregfes <- plm(vio~shall, model="within", data=mydatap)
    coeftest(pregfes, vcov=rvfes)	
    ```

- Column 3:

  - ```R
    pregfesy <- plm(vio~shall, effect="twoway", model="within", data=mydatap)
    coeftest(pregfesy, vcov=rvfesy)
    ```

- Column 4:

  - ```R
    pregfesymulti <- plm(vio~shall+density+avginc+incarc_rate+pm1029, effect="twoway", model="within", data=mydatap)
    coeftest(pregfesymulti, vcov=rvfesymulti)
    ```

- The regression equations for each column can be written as:
  1. vio = β0 + β1 * shall
  2. vio = β0 + β1 * shall + state fixed effects
  3. vio = β0 + β1 * shall + state fixed effects + year fixed effects
  4. vio = β0 + β1 * shall + β2 * density + β3 * avginc + β4 * incarc_rate + β5 * pm1029 + state fixed effects + year fixed effects

(c) Why are the standard errors different between the two tables? Which command leads to this difference? Which standard errors should we use in practice and why?

- The standard errors are different between the two tables because they are calculated using different methods. In Table 1, the standard errors are calculated using the OLS (Ordinary Least Squares) method, while in Table 2, the standard errors are computed using panel data methods with heteroskedasticity and autocorrelation consistent (HAC) estimators. 
- The command that leads to this difference is the `coeftest()` function with the `vcovHC()` function, which calculates the HAC standard errors. 
- In practice, we should use the standard errors from Table 2 (calculated using the HAC estimators) because they account for potential heteroskedasticity and autocorrelation in the panel data.

(d) Do you think that the data suggests that “shall” carry laws have a (positive or negative) effect on violent crime rates? Explain your answer and perform any statistical tests to support your answer.

- Based on the output, the coefficients for the "shall" variable are negative, suggesting a potential negative relationship between "shall" carry laws and violent crime rates. However, the p-values in both cases are considerably higher than 0.05, indicating that the results are not statistically significant. Thus, based on this analysis, we cannot confidently conclude that "shall" carry laws have a significant effect (positive or negative) on violent crime rates. The evidence from the provided data and these models does not support a strong relationship between the two.

**II. Model Selection in the Sales and Advertising Example.** Using the “Advertising” data set which we used extensively in our class, write an R program that can perform the best subset selection as well as LASSO: 

(a) Perform best subset selection using (1) Akaike information criterion (AIC), (2) Bayesian Schwarz information criterion (BIC), and (3) leave-one-out cross-validation (CV). Below are the steps you need to perform for best subset selection:

-Install and load the package “forecast”. 

-Using the “Advertising” data set, perform regressions of sales on all possible combinations of the three regressors in your data set. 

-For each regression, using the function CV () to compute the AIC, BIC and CV. 

-Find the model that minimizes AIC, BIC and CV among all models considered. 

Report the results in a table similar to the table below Example: credit scores (continued) in the following link https://www.otexts.org/fpp/5/3.

- ```R
  load(url("https://files.asmith.ucdavis.edu/Advertising.RData"))   #loads data
  install.packages("stargazer")
  library(stargazer)
  
  #Assignment I Problem III S2020
  
  regst<-lm(Sales~TV, data=mydata)
  regstn<-lm(Sales~TV+Newspaper, data=mydata)
  regsr<-lm(Sales~Radio, data=mydata)
  regsrt<-lm(Sales~Radio+TV, data=mydata)
  stargazer(regst,regstn,regsr,regsrt,type="text")
  
  # Load necessary libraries
  install.packages("leaps")
  install.packages("forecast")
  library(leaps)
  library(forecast)
  
  # Perform best subset selection
  all_subsets <- regsubsets(Sales ~ TV + Radio + Newspaper, data = mydata, nvmax = 3)
  all_subsets_summary <- summary(all_subsets)
  
  # Extract AIC, BIC, and adjusted R-squared
  aic <- all_subsets_summary$aic
  bic <- all_subsets_summary$bic
  adj_r_squared <- all_subsets_summary$adjr2
  
  # Find the models with minimum AIC, BIC, and maximum adjusted R-squared
  min_aic_model <- which.min(aic)
  min_bic_model <- which.min(bic)
  max_adj_r_squared_model <- which.max(adj_r_squared)
  
  # Perform leave-one-out cross-validation (CV)
  cv_error <- rep(0, 3)
  for (i in 1:3) {
    model <- lm(Sales ~ ., data = mydata, subset = (1:nrow(mydata)) != i)
    y_pred <- predict(model, newdata = mydata[i, ])
    cv_error[i] <- mean((mydata$Sales[i] - y_pred)^2)
  }
  
  min_cv_model <- which.min(cv_error)
  
  ```

(b) Perform the LASSO method to select the variables to include in the model to replicate the LASSO results in the Week 9 Lecture Notes.

```R
# Prepare the data
x <- model.matrix(Sales ~ TV + Radio + Newspaper, data = mydata)[, -1] # Remove the intercept column
y <- mydata$Sales

# Fit the LASSO model
lambda_seq <- 10^seq(3, -3, length.out = 100) # Create a sequence of lambda values
lasso_fit <- glmnet(x, y, alpha = 1, lambda = lambda_seq, standardize = TRUE)

# Find the optimal lambda using cross-validation
lasso_cv <- cv.glmnet(x, y, alpha = 1, lambda = lambda_seq, standardize = TRUE)
optimal_lambda <- lasso_cv$lambda.min

# Fit the LASSO model with the optimal lambda
lasso_final <- glmnet(x, y, alpha = 1, lambda = optimal_lambda, standardize = TRUE)
```

(c) Compare the results from the best subset selection method using the different criteria and the LASSO method.

- The best subset selection methods using AIC and BIC both suggest that the Newspaper variable can be excluded from the model, as the BIC and adjusted R-squared criteria both identify the model with only TV and Radio as the best. The AIC criterion, however, includes all three variables (TV, Radio, and Newspaper) in the model.

  The LASSO method identifies a similar result, where the coefficient for the Newspaper variable is effectively reduced to zero, which means that it has been excluded from the model. The optimal LASSO model includes the TV and Radio variables only.

  In conclusion, the best subset selection methods (using BIC, adjusted R-squared, and CV) and the LASSO method are largely in agreement, identifying TV and Radio as the most important variables for predicting Sales in the Advertising dataset. The Newspaper variable seems to have little to no impact on Sales and can be excluded from the model.

**III. Empirical Exercise: S&P500.** Using the Stock_Price_Data and the Stock_Price_Program provided to you, perform the following tasks. Notes on the data: SP500 is the index itself, SP500L is its lag; SP500D is its difference, SP500D1 is the lag of the difference; SP500R is the return on the S&P500, SP500RL is its lag.	

(a) Perform three OLS regressions (1) SP500 on its lag SP500L, (2) SP500D on its lag SP500D1, (3) SP500R on its lag SP500RL. Report the regression coefficients and the R2 ’s.

- ```R
  regsp<-lm(SP500~SP500L,data=mydata)
  regsd<-lm(SP500D~SP500D1,data=mydata)
  regsr<-lm(SP500R~SP500RL,data=mydata)
  ```

(b) Which of the regressions in (a) may be spurious? Why?

- Among the three regressions in (a), the first one (1) SP500 on its lag SP500L might be spurious. This is because both the SP500 and its lag SP500L are likely to be non-stationary time series, as stock price indices often have a unit root and exhibit a random walk behavior. If both the dependent variable and the independent variable are non-stationary, the regression can result in spurious relationships and the model can appear to have a high R-squared value even if there is no true relationship between the variables.

(c) In finance theory, it is well established that stock returns are slightly negatively correlated with their lags, does any of the regressions above support this theory? Refer to the appropriate regression and explain in words what it means.

- The third regression (3) SP500R on its lag SP500RL is the most relevant to this financial theory, as it models the relationship between stock returns and their lagged values. In words, if the regression results show a significant negative relationship between stock returns and their lagged values, it means that when the stock return is higher than average in the previous period (positive lagged return), it tends to be lower than average in the current period, and vice versa. This negative correlation between returns and their lags suggests that there is some degree of mean reversion in stock returns, which is consistent with the financial theory.
