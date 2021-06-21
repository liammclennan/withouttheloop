---
title: A Modern Approach to Fixed-price Software Product Development
author: Liam McLennan
date: 2020-09-30 20:32
template: article.jade
---

When purchasing software development services the customer wishes to optimize three variables: value, cost and risk. The approach described below is designed to maximize value and minimize cost and risk.

# Fixed-price, Fixed-effort

Consider the project ABCD.

![Project ABCD](project-abcd.png)
Figure 1: The ABCD project

ABCD consists of four high-level components: A, B, C and D. Each component is subject to a high-level analysis to identify an appropriate budget. The budget is based on the expected value of the component and expert opinion of the investment required to deliver a quality, effective solution. 

| Component | &nbsp;&nbsp;&nbsp; &nbsp; | Effort Budget (days)  |
|---|-|---|
| A  || 15  |
| B  || 10  |
| C  || 30  |
| D  || 35  |
| **Total** || **90** |

<br/>The fixed-price is the vendor’s price for ninety days of effort. The fixed-effort is ninety days. The vendor and customer now form a fixed-price, fixed-effort agreement to deliver project ABCD. 

During delivery the customer and vendor prioritize and negotiate the details of each component to ensure that they fit within the effort budget.

# Fixed-price

Fixing the price has the benefit of controlling the customer’s cost risk. While not denying the value of controlling cost there are some caveats that must be noted. Firstly, like the hydra, risk is a many-headed beast and cost is rarely the risk that causes failure. The greater risk is the risk of delivering a solution that does not achieve the required outcome. It does not matter how effectively the cost risk is controlled if the solution does not solve the problem. In this case every dollar that was spent returned nothing. 

Unfortunately, the most common technique for controlling cost, fixed-price fixed-scope, dramatically increases the chance of delivering a solution that is not fit for purpose (see [Why Fixed-price, Fixed-scope Is Bad For The Customer](/articles/2020-09-30-fixed-price-bad-idea/)).

# Fixed-effort

Fixing the effort ensures efficiency. 

Imagine a scenario where the planned implementation of a blockchain storage mechanism is discovered to require twice as much effort as expected, but a database solution is available for less than the original estimate, delivering 99% of the functionality. Because the customer and vendor are required to jointly prioritize and negotiate the details of each component to fit the effort estimate their motivations are aligned to make the sensible decision and choose the database solution. 

Each feature and decision is necessarily scrutinized to ensure that it serves the goals of the project. It becomes very difficult for pet features, unjustified features and gold-plating to sneak in. 

# Conclusion

The final reason to support a fixed-price, fixed-effort approach is that other fixed-price approaches empirically do not work. The control of cost risk is lost to predatory change requests and the necessity of following the plan prevents the team from pursuing value and a system that will achieve its outcome. 

Finally, fixed-price fixed scope misalignes incentives in a way that prevents efficient delivery. For more, see [Why Fixed-price, Fixed-scope Is Bad For The Customer](/articles/2020-09-30-fixed-price-bad-idea/).



