# BankCo Premium Card Retention Assistant

## Week 0 Warm-Up Exercise

### Working Assumption

The workbook does not specify a run date. For this analysis, the run date is assumed to be **January 15, 2026**, aligned with the latest customer activity date in the dataset. On that date, both customers are 30-90 days from annual-fee renewal. If the workflow runs on a different date, the agent must recalculate the renewal window before assessing risk.

## Step 1: Clarify the Goal

Relationship Managers lack a prioritized, consolidated view of premium customers approaching renewal, the reasons each customer may leave, and the compliant offer most likely to address those reasons. BankCo's goal is to help RMs intervene earlier with relevant, policy-compliant outreach, improving premium-card retention while reducing manual effort and avoiding unnecessary incentive spend.

**Primary success metric:** Premium-card renewal rate among agent-identified at-risk customers.

**Secondary success metric:** Percentage of agent recommendations reviewed and actioned by RMs before the annual-fee due date.

Supporting operational measures should include time saved per RM, offer acceptance rate, recommendation override rate, and policy-compliance error rate. These are useful diagnostics but should not replace the primary customer outcome.

## Step 2: Five Signals That Matter

1. **annual_fee_due_date** - Establishes whether the customer is inside the required 30-90 day intervention window and determines outreach urgency.
2. **nps_last** - Provides a direct signal of dissatisfaction; a score at or below -20 is severe enough to trigger immediate review.
3. **complaints_90d** - Captures recent service friction; two or more complaints indicate severe risk and favor service recovery over a generic reward.
4. **travel_share_change** - Detects declining use of a core premium-card category and can reveal disengagement even when total spend remains moderate.
5. **redemptions_90d** - Shows whether the customer is realizing rewards value; zero redemptions may indicate that premium benefits are not understood or valued.

The agent should also retain tier, tenure, disputes, benefit denials, lounge visits, category spend, preferred channel, and offer-cap history as eligibility, personalization, or compliance inputs.

## Step 3: Agent Workflow

1. **Load and validate daily data.** Read CustomerData, Offer Policy, and RM ownership data; validate required fields, date formats, duplicate customer IDs, and missing values. Stop and flag the batch if critical data is invalid.
2. **Apply the renewal-window filter.** Calculate days until `annual_fee_due_date` and retain only premium customers 30-90 days from renewal.
3. **Evaluate risk signals.** Apply the 2-of-N and severe-signal rules to each eligible customer. Store the triggered facts rather than only a generic high/medium/low label.
4. **Prioritize the queue.** Rank severe-risk customers first, followed by the number and intensity of triggered signals, proximity to renewal, and customer value. Do not allow an opaque model score to override explicit policy rules.
5. **Determine eligible offers.** Evaluate every Offer Policy rule deterministically, including exclusions, validity dates, cost bands, and annual caps. If several offers qualify, choose the lowest-cost option that directly addresses the primary risk driver.
6. **Generate an RM-ready recommendation.** Use generative AI to summarize up to three evidence-backed risk reasons and draft a personalized email using only approved policy copy and customer facts. The model must not invent benefits, causes, or commitments.
7. **Log the recommendation.** Write the run date, customer ID, RM ID, renewal-window days, three risk reasons, recommended offer ID, draft message, and unsent status to Retention Recommendation.
8. **Notify the RM.** Email the assigned RM that recommendations are ready for review. The RM can approve, edit, reject, or defer; only the RM may contact the customer or apply an offer.

## Step 4: System Sketch

```text
Spend Signals -----------\
                         \
Experience Signals -------> Premium Retention Assistant <------- Offer Policy
                         /                 |
Premium Customers -------/                  | writes recommendations
                                            v
                              Retention Recommendations Log
                                            |
                                            | sends readiness notice
                                            v
                                RM Email Notification ---> RMs
                                                               |
                                                               | review, edit, approve
                                                               v
                                                     Premium Customers
```

The return path from the RM to the customer is intentionally human-controlled. The agent produces decision support and drafts; it does not send customer communications or change account settings.

## Dataset-Based Recommendations

### 1. CUST1003 - Highest Priority

**RM:** RM001 - Aisha Mehta  
**Renewal date:** March 10, 2026  
**Renewal window:** 54 days  
**Recommended offer:** OFF008 - Retention Specialist Email Outreach

**Why this customer is at risk:**

- Severe dissatisfaction: NPS is -40.
- Repeated service friction: two complaints and one dispute in the past 90 days.
- Declining engagement: total spend is 3,100, travel share declined 40%, and there were no lounge visits or reward redemptions.

**Offer reasoning:** OFF008 is eligible because NPS is at or below zero and complaints are at least two. It is preferable to an immediate monetary incentive because the policy copy explicitly prioritizes root-cause resolution before generic offers. OFF001 is not eligible because the customer has a recent dispute.

**RM-ready email draft:**

> Subject: I would like to review your recent card experience
>
> Hi [Customer Name],
>
> I noticed that your recent experience with your Platinum card may not have met expectations. I would like to personally review what happened and understand how we can better support you before your upcoming renewal. Rather than send a generic offer, I will first look into the concerns raised and propose the most appropriate next step.
>
> Please reply with a convenient time for a brief conversation.
>
> Regards,  
> Aisha Mehta

### 2. CUST1001 - High Priority

**RM:** RM001 - Aisha Mehta  
**Renewal date:** February 28, 2026  
**Renewal window:** 44 days  
**Recommended offer:** OFF007 - Service Recovery Credit ($50) + Benefit Review

**Why this customer is at risk:**

- Reduced premium-benefit engagement: travel share declined 35%, with no lounge visits or reward redemptions in 90 days.
- Recent negative experience: one complaint and one benefit denial.
- Negative sentiment: NPS is -10.

**Offer reasoning:** OFF007 is eligible because the customer has a complaint and a benefit denial. The service review directly addresses the negative experience, while the $50 credit provides a controlled, low-cost recovery gesture. OFF002 and OFF006 are also eligible, but they focus on future spend rather than resolving the existing service issue.

**RM-ready email draft:**

> Subject: Let us review your Platinum card benefits
>
> Hi [Customer Name],
>
> I am reaching out ahead of your upcoming Platinum card renewal. I noticed a recent service concern and would like to make sure you receive the full value of your card benefits. I can request a fast benefit review and apply a $50 courtesy credit to recognize the inconvenience.
>
> Please let me know a convenient time to discuss your experience and any benefits you would like help using.
>
> Regards,  
> Aisha Mehta

## Human Decision-Making and Controls

- **RM approval is mandatory:** The agent may draft and recommend, but it cannot contact customers or apply offers.
- **Policy evaluation is deterministic:** Eligibility, exclusions, caps, and expiry dates should be enforced by rules, not interpreted freely by a language model.
- **Explanations must cite customer facts:** Every recommendation should show the signals and policy conditions used so the RM can challenge it.
- **Sensitive cases require escalation:** Disputes, repeated complaints, very low NPS, missing data, or conflicting eligibility results should be routed for human review.
- **Customer data is minimized:** The model receives only fields necessary for risk explanation and approved-message personalization.
- **All actions are auditable:** Store input snapshot, triggered rules, offer decision, generated draft, RM edits, approval status, and timestamps.
- **Fairness is monitored:** Compare recommendation and offer rates across relevant customer segments and investigate unexplained disparities.

## Implementation Approach

Start with a narrow pilot for RM001 and the two supplied customers. Run the agent daily in recommendation-only mode, require RM approval, and capture accept, edit, reject, and reason codes. Before expanding, validate policy accuracy, recommendation usefulness, RM time saved, and renewal outcomes against a comparable group using the existing manual process.

The first production version should use rules for renewal filtering, risk detection, prioritization boundaries, and offer eligibility. Generative AI should be limited to explaining the decision and drafting approved, personalized communication. This separation makes the system easier to audit, test, and improve.

## Key Product Trade-Off

Optimizing only for renewal rate could encourage excessive discounts. The agent should therefore maximize retained customer value subject to compliance, customer relevance, and offer-cost constraints. A successful system retains the right customers with the right intervention, not simply the most expensive incentive.
