##### Problem 1 Dominant Firm Theory

Suppose demand for wind turbines is $Q = 110-3P$, where $P$ is the price. The dominant producer in this industry is “Winnie’s Wind Turbines”. There are also a number of small price-taking firms that can be represented by the supply function $S(P)=P-10$. The marginal cost of production for the dominant firm is given by $MC_d=10$ and the total cost function is given by $10q_d$.  

a. Derive the residual demand curve for the dominant firm

- $Q_d = Q(P)-S(P) = 110-3P-(P-10)$

  $Q_d = 120-4P$

b. What quantity would Winnie’s Wind Turbines supply in the wind turbine market?

- Dom's Inverse Demand: $P_d = 30-\frac{Q_d}{4}$

  $\pi_d  = PQ_d-TC_d = (30-\frac{1}{4}Q_d)Q_d-10Q_d$

  $max\space\pi_d: \frac{\part\pi}{\part Q_d} : (30-\frac{Q_d}{2})-10 = 0$

  $Q_d = 40$

c. What would be the price for a wind turbine?  

- $P(Q_d) = 30-\frac{Q_d}{4} = 30-40/10 = 20$

d. What quantity would the fringe supply in the wind turbine market? 

- $S(P) = P-10 = 20-10 = 10$

##### Problem 2 Strategic Competition

Suppose Toyota and Honda both plan to develop a new all-electric car, with no comparable counterpart on the market. If one firm enters the market, it will be a monopoly and earn monopoly profits. The other manufacturer will suffer losses (they will not recover their research and development costs). Finally, if neither firm enters the status quo will be preserved. Profits for each under the various scenarios are shown below,

|             | Toyota   |             |
| ----------- | -------- | ----------- |
| **Honda**   | Enter    | Don't Enter |
| Enter       | (-3, -3) | (4, -1)     |
| Don't Enter | (-1, 4)  | (0, 0)      |

a. What are the Nash equilibria? Are the companies indifferent among the equilibria? 

- The Nash Equilibria are {Enter, Don't Enter} and {Don't Enter, Enter}.  Each companies would prefer the one in which that company is the one to enter and the competitors stays out.

Now suppose that Toyota is the incumbent firm in the all-electric car market and facing the possible entry of Honda, a competitor. Toyota must choose whether to invest in capacity to deter entry by Honda. Once Honda observes Toyota’s investment level, it decides whether or not to enter the market, knowing that it will incur fixed entry costs K, upon entry.  Profits to Toyota are 8 if Honda does not enter when investment is high and 9 when investment is low. If the Honda does not enter, it receives a payoff of 0. If Honda enters then payoff are {6,5-K} when investment was low and {4, 3-K} when investment was high.

b. At what values of K will Toyota choose low capacity investment? Why? 

- ```mermaid
  flowchart TD
  	A[Toyota] -- Low --> B[Honda]
  	A -- High --> C[Honda]
  	B -- Enter --> D{6, 5-k}
  	B -- Don't Enter --> E{9, 0}
  	C -- Enter --> F{4, 3-k}
  	C -- Don't Enter --> G{8, 0}
  ```

- If k < 3, Honda will always choose Enter, and because of that, Toyota will choose Low {6, 5-k} since 6 > 4

- If $k \ge 5$, Honda will always choose Don't Enter, and because of that, Toyota will choose Low {9, 0} since 9 > 8

c. At what values of K will Toyota choose high capacity investment? Why?

- If $3 \le K <5$, Honda will choose Enter if Toyota choose Low, and choose Don't Enter if Toyota choose High. Since 8 > 6, Toyota will choose High and Honda will choose Don't Enter. Therefore, Toyota will choose High to maximize its profits, so {High, Don't Enter} if $3 \le K <5$



##### Problem 3 Austin’s Autos 

Austin’s autos is currently the sole producer of cars. Its cost function is $C(q) = 36+3q$ and the market demand function is $D(P)=83-P$. There is a large pool of potential entrants, each of which has the same cost function as Austin. Assume the Bain Sylos postulate. Let the incumbent firm’s output be denoted $q^I$.

a. Derive the inverse residual demand function for a new firm in terms of $q^I$ and $q^E$?

- Demand: $D(P)=83-P$

  $P = 83-Q = 83-q^I-q^E$

  Residual Demand: $q^E = 83-P-q^I$

b. Given that the incumbent firm is currently producing $q^I$, if a potential entrant was to enter, how much would it produce? 

- $\pi_E = (83-q^I-q^E)q^E - (36+3q^E)$

  $\frac{\part \pi_E}{\part q_E} : 83-q^I-2q^E-3 = 0$

  Entrants response function: $q^E = 40-\frac{1}{2}q^I$

c. Find the limit price. Hint: Find the output for Austin such that the slope of a new firm’s average cost curve equals the slope of a new firm’s residual demand curve. 

- Average Cost Function: $AC_E =\frac{C(q)}{q} = \frac{36+3q_E}{q_E} = \frac{36}{q_E}+3$

  $\frac{\part AC_E}{\part q_E} :-\frac{36}{q_E^2}$

  $P = 83-Q = 83-q^I-q^E$

  $\frac{\part P}{\part q_E}  = -1$

  since average cost curve = demand curve

  $-\frac{36}{q_E^2} = -1$

  $q_E = 6$, That means if entrant pick the quantity 6, there would be 0 profit.

  By the response curve: $q^E = 40-\frac{1}{2}q^I$

  $6 = 40-q^I/2$

  $q^I = 68$, To block competitive opponent qi = 68

  Therefore the limit price is $P^{limit} = 83-68 = 15$

d. Suppose instead of assuming Bain-Sylos, we now assume active firms expect to achieve a Cournot solution. Does entry depend on $q^I$? Will there be entry?  

- The entrant knows that the incumbent will not choose the pre-entry output if it enters the market. Instead, the incumbent will choose a quantity that maximizes his profits which results in the Cournot solution. New firms will enter and continue to enter until there are no profits to entry.
