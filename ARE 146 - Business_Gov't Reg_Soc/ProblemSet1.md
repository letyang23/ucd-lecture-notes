##### Problem 1 Hallie’s Hats 

Hallie’s hats is the only supplier of hats in and around San Francisco. Inverse demand for hats is given by $𝑃= 1820 −10𝑄$, and the total cost is characterized by $T𝐶 = 20𝑄$, where $𝑄$ is the number of hats and $P$ is the price in dollars per hat.

a. Assuming Hallie’s Hats operates as a monopoly, what is the price and quantity of hats in the market? Calculate the consumer and producer surplus. 

- Monopoly	$P = 1820-10Q$

- $TR = PQ = (1820-10Q)Q$

  $\pi(profit) = TR-TC = (1820-10Q)Q - 20Q$

  $\frac{\part\pi}{\part Q} = 1820-20Q-20 = 0$

  $Q = 90$ (under monopoly)

  $P = 1820-10(90) = 920$ (under monopoly)

  $CS = \frac{1}{2}(1820 - 920)90 = 40500$

  $PS = (920 - 20)90 = 81000$ since Producer Surplus = Total Revenue - Marginal Cost

  `920不知道为什么要-20	`

b. Assume the market for hats is perfectly competitive. Find the equilibrium price and quantity. Calculate the consumer and producer surplus.  

- Perfect Competition $P = 1820-10Q$

- $Max_\pi Q : TR-TC = PQ-TC = PQ - 20Q$

  $\frac{\part \pi}{\part Q}: P-20 = 0$

  $P = 20$

  $1820 - 10Q = 20$

  $Q = 180$

  $CS = \frac{1}{2}(1820-20)180 = 162,000$

  $PS = TR-MC = PQ - 20Q = 20*1620-20*1620 = 0$
  
  `Not sure how to calculate Producer Surplus`

c. Estimate the deadweight loss imposed on society if Hallie’s hats remains a monopoly.  

- $DWL = CS+PS(Perfect\space Competition) - (CS+PS)(Monopoly)$

​					$ = 162000 - 40500-81000 = 40500$

- Other way: $DWL = (1/2)(P2-P1)(Q2-Q1) =  \frac{1}{2}(920-20)(180-90) = 40500$

`DWL不知道怎么算（在constant supply curve下）Quantity difference怎么算？`



##### Problem 2 - Soda Oligopoly

Suppose that Pepsi and Coke compete as Cournot oligopolists in the production of soda. Demand for soda is given by $P = 140 −2Q$ where $Q = q_c+q_p$ is the total number of cans of soda supplied by Pepsi and Coke together, measured in cans per year, and price is measured in dollars per can. The two firms face identical cost functions given by $C(q) = 200+8q$.



a. What are the Cournot equilibrium prices, quantities and profits of the two firms? 

- Demand $P = 140- 2Q$ ,	$C(q) = 200+8q$

  $Max_\pi = TR-TC = PQ - TC = (140-2(q_c+q_p))q_c-(200+8q_c)$

  $\frac{\part \pi_c}{\part q_c} = 140-4q_c-2q_p -8 = 0$

  $140 -2q_p - 8=4q_c $

  $q_c = 35-\frac{1}{2}q_p-2$

  $q_c = 33-\frac{1}{2}q_p$ **Reaction Function**

  since $q_c = q_p$

  so $q_c = 35-\frac{1}{2}q_c - 2$

  $\frac{3}{2}q_c = 33$

  $q_c = \frac{2}{3}*33 = 22$

  $Q = 22*2 = 44$

  $P = 140-2(22+22) = 52$

  $Max_\pi = 22*52-(200+8*22) = 768$

  

b. Suppose Pepsi and Coke collude to maximize joint profits. If the two firms collude, what will be the total quantity of soda supplied to the market, the equilibrium price and the profits earned by each firm?  

- Demand $P = 140- 2Q$ ,	$C(q) = 200+8q$

  $\pi_1+\pi_2 = (140-2(q_1+q_2))q_1+(140-2(q_1+q_2))q_2-(200+8(q_1+q_2))$

  $\frac{\part (\pi_1+\pi_2)}{\part q_1} = 140-4q_1-2q_2-2q_2-8 = 0$

  $35-q_2-2 = q_1$

  $q_1 = 33-q_2$

  Since $q_1 = q_2$

  $q1 = 16.5$

  Price per can: $140-2(16.5+16.5) = 74$

  Profits: $\pi_1=\pi_2 = 16.5*74-200-8*16.5 = 889$



c. In a one-period game will Pepsi and Coke choose collusion or a Cournot solution?  Why?  

- |           | Cournot    | Collusion  |
  | --------- | ---------- | ---------- |
  | Cournot   | (768, 768) | (?, ?)     |
  | Collusion | (?, ?)     | (889, 889) |

- P1 Cournot, P2 Collude

  - P1 has $q_1 = 33-\frac{1}{2}(16.5) = 24.75, p = 140-2(16.5+24.75) = 57.5$

  - P2 has $q2 = 16.5$

  - P1 profit: $57.5(24.75) - 200-8*24.75 = 1025$

  - p2 profit: $57.5(16.5)-200-8*16.5 = 617$

  - So Cournot, Collusion = (1025, 617)

    

d. If Coke can set its output level before pepsi, how much will Coke produce? How much will Pepsi produce? What is the market price? What are the profits to each firm?  

- Stackelberg Model

- $P = 140-2Q, TC = 200+8q$

  **Reaction Function** (from Cournot Model): $q_1 = 33-(1/2)q_2$

  $\pi_1 = TR_1 - TC_1 = pq_1-TC_1$

  $ = (140-2(q1+q2))q1-200-8q1$

  $= (140-2(q1+33-(1/2)q1))q1-200-8q1$

  $ = -q_1^2+66q_1-200$

  $\frac{\part \pi_1}{\part q_1}: -2q_1+66 = 0 $

  $q_1=33$

- Coke will produce 33, Pepsi will produce $33-(1/2)33 = 16.5$
- Market Price will be $140-2(33+16.5) = 41$

- Profit to coke will be: $41*33-200-8*33 = 889$
- Profit to Pepsi will be: $41*16.5-200-8*16.5 = 344.5$



##### Problem 3 - Collusion in an Infinitely Repeated Game 

Demand for electric cars is given by $P = 500-2Q$ where $Q = q_T+q_H$ is the total number of electric cars supplied by Honda and Toyota together (there is no product differentiation), measured in cars per year, and price is measured in dollars per car. The two firms face identical cost functions given by $C = 5000+2q$

Assume an infinite time horizon. The two firms can continue to engage in collusion. Alternatively, a firm can choose to cheat. If a firm cheats in any period, the collusive agreement will break down and in all remaining periods each firm will produce the Cournot quantity. What interest rate is necessary to maintain this collusive agreement over time?   



- We need to first find Cournot Model profit, then collude model profit
- Cournot:

  - $Max\pi_1 =TR-TC = (500-2(q1+q2))q1-5000-20q1$
  - derivative of $q1$ = $500-4q1-2q2-20 = 0$
  - $q1 = 120-(\frac{1}{2})q2$ **Best Reaction Function**
  - since $q1 = q2$, so $(3/2)q1 = 120$, $q1 = q2 = 80$
  - Profit $ = (500-2(80+80))*80 - 5000-20*80 = 7800$
  - Therefore, $\pi_{cournot} = 7800$
- Collude:

  - $Max_{\pi1+\pi2} = (500-2(q1+q2))q1+(500-2(q1+q2))q2-5000-20(q1+q2)$
  - derivative of q1: $ 500-4q1-2q2-20 = 0$
  - $q1 = 120-q2$
  - $q1 = q2 = 60$
  - $\pi_1 = (500-2(120))*60-5000-20*60 = 9400$
  - Therefore, $\pi_{collude} = 9400$

  - Add interst rate = $\pi_{Collusion} = 9400+\frac{9400}{r}$
- Trigger Strategy (Cheat)

  - $q1 = 120-(1/2)(60) = 90$ **From Best Reaction Function**
  - $P = 500-2(60+90) = 200$ **Market Price**
  - $\pi_{cheat} = 200*90-5000-20*90 = 11200$
  - Add interest rate $ 11200+\frac{\pi_{cournot}}{r} = 11200 +\frac{7800}{r}$
- Find the interest rate when Collude > Cheat
  - Collude > Cheat
  - $9400+\frac{9400}{r} > 11200+\frac{7800}{r}$
  - $\frac{9400}{r} - \frac{7800}{r} > 11200-9400$
  - $\frac{9400}{r} - \frac{7800}{r} > 1800$
  - $\frac{9400-7800}{1800} > r$
  - $r<\frac{16}{18}$
  - so when $r<\frac{8}{9}$, agreement can continue