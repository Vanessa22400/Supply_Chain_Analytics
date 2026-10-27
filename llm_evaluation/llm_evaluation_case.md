# LLM Response Evaluation — Original Prompt, Responses, and Scores

This file preserves the prompt and the two AI-generated responses used in the supplementary evaluation of the Supply Chain Analytics project. The responses are reproduced as supplied, without rewriting or correcting their content.

## 1. Evaluation prompt

> You are a business analytics consultant. Write a concise recommendation for a retail operations manager based only on the findings below.
>
> - Forecasting: The selected product-level methods achieved 7.4% WAPE in a 20-week walk-forward evaluation. Models were selected using the same evaluation period.
> - Inventory: A simulated planning scenario identified 185 shortage units and 120 surplus units across four regional locations.
> - Optimization: A Linear Programming model recommended transferring 120 units and purchasing 65 units. Estimated replenishment cost fell from $6,538.60 to $3,253.50, representing potential savings of 50.2% under simulated cost and inventory assumptions.
> - Risk: Monte Carlo simulation found shortages in 91.3% of scenarios, with an average emergency replenishment cost of $1,252.91 under hypothetical assumptions.
> - Customers: Champions and At-Risk High Value customers contributed 77.1% of completed-order revenue.
> - Profitability: 18.6% of completed-order lines generated negative profit, totaling $1.26 million in gross losses.
>
> Provide three prioritized business recommendations in approximately 150 words. Explain the business reasoning, distinguish actual observations from simulated results, and do not introduce unsupported claims.

## 2. Response A — Claude

**Recommendations**

**1. Rebalance inventory before buying new stock.** The simulated planning scenario shows 185 shortage units against 120 surplus units, and the optimization model covers the gap by transferring all 120 surplus units and purchasing only 65. Under simulated cost assumptions, replenishment cost falls from $6,538.60 to $3,253.50 (**50.2%** potential savings). Treat this as a pilot, since the savings depend on assumed costs.

**2. Hold a safety buffer despite the optimized plan.** The Monte Carlo simulation found shortages in **91.3%** of hypothetical scenarios, with emergency replenishment averaging $1,252.91. Forecast accuracy is reasonably strong at **7.4% WAPE**, but models were selected on the same 20-week period used to evaluate them, so real-world error is likely higher.

**3. Investigate negative-profit lines and protect top customers.** These are actual observations: **18.6%** of completed-order lines lost money, totaling **$1.26 million**, while Champions and At-Risk High Value customers drive **77.1%** of revenue. Review pricing, discounting, and product mix on loss-making lines, and prioritize service levels for these two segments.

## 3. Response B — ChatGPT

### Prioritized Business Recommendations

1. Optimize inventory allocation and replenishment

   Prioritize testing regional inventory transfers before placing new purchase orders. The simulated optimization recommended transferring 120 units and purchasing 65 units, potentially reducing replenishment costs by 50.2% ($6,538.60 to $3,253.50). These savings depend on simulated assumptions and require operational validation. The 91.3% shortage probability observed in Monte Carlo scenarios reinforces the need for contingency planning.

2. Improve demand forecasting and inventory planning

   Integrate product-level forecasts into replenishment decisions. The selected methods achieved 7.4% WAPE over 20 weeks, although model selection and evaluation used the same period. Independent validation is recommended before deployment to assess reliability and support inventory planning.

3. Protect high-value customers and improve profitability

   Prioritize retention strategies for Champions and At-Risk High Value customers, who generated 77.1% of completed-order revenue. Simultaneously, investigate pricing, discounts, and costs associated with the 18.6% of completed-order lines generating negative profit, representing $1.26 million in gross losses.

## 4. Human evaluation scores

Scores use a 1–5 scale (5 = excellent).

| Criterion | Response A (Claude) | Response B (ChatGPT) |
|---|---:|---:|
| Accuracy | 5 | 5 |
| Groundedness | 4 | 5 |
| Clarity | 5 | 5 |
| Actionability | 5 | 4 |
| Limitations | 4 | 5 |
| **Total** | **23/25** | **24/25** |

**Preferred response:** B (ChatGPT).

**Evaluation note:** Response A was more specific about safety stock, but the claim that future errors are *likely higher* is not established by the provided evidence. Response B was more cautious about this limitation, though somewhat less specific operationally. The reviewer selected Response B but did not supply a separate written rationale.

**Scope:** Two responses to one prompt. The evaluation was not fully blinded or independent and does not establish general model superiority. The simulated supply-chain results are not actual observed company savings.
