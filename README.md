# BankCo-Premium-Card-Retention-Assistant
The Premium Retention Assistant identifies at-risk cardholders nearing renewal, explains the risk, recommends a compliant offer, drafts personalized outreach, and alerts the Relationship Manager. The RM reviews every recommendation and remains responsible for contacting customers or changing accounts.

**1. Problem statement**
BankCo wants to retain premium credit-card customers who may cancel or downgrade when their annual fee renews.
The current process has several problems:

Relationship Managers do not have one consolidated view of customers at risk.
Customer lists and risk assessments are prepared manually.
Outreach may happen too late.
Generic messages and offers may not address the customer’s actual concern.
Inconsistent offer selection creates cost, fairness, and compliance risks.
Losing premium customers reduces annual-fee revenue, spending volume, customer lifetime value, NPS, and cross-sell opportunities.

The specific objective is:

Identify premium customers who are 30–90 days from annual-fee renewal and show signs of disengagement or dissatisfaction, then prepare personalized, policy-compliant recommendations for their Relationship Managers.

**2. Solution requested by the exercise**
The exercise asks for the design of an internal AI retention assistant. No implementation or coding is required.
The requested work has four parts:
Clarify the goal
Describe:
The problem experienced by Relationship Managers.
BankCo’s business objective.
One primary success metric.
One secondary success metric.
Identify important signals

_Select five customer-data fields that help:_
Detect cancellation risk.
Explain why a customer is at risk.
Choose an appropriate offer.
Personalize the outreach message.
Define the agent workflow

_Describe six to eight sequential steps showing how the agent would:_
Find customers within the renewal window.
evaluate risk signals.
Generate understandable risk reasons.
Check offer eligibility.
Select a compliant offer.
Draft a personalized email.
Record the recommendation.
Notify the Relationship Manager.
Draw the system flow

Show how customer, spending, experience, RM, and offer-policy data move through the agent and into the recommendation log and RM notification.

**3. My analysis**
Date assumption

The workbook does not contain an explicit run date. I used January 15, 2026, because that is the latest customer-activity date in the dataset.

Using this date:
CUST1001 renews in 44 days.
CUST1003 renews in 54 days.

Therefore, both customers are inside the required 30–90 day renewal window.

In a real implementation, the agent would calculate this window using the actual daily run date.

**Risk logic**
A customer is considered at risk when either:
_Any two general-risk signals are present; or
At least one severe-risk signal is present._

General-risk signals include:
_Total 90-day spend below 4,000.
Travel-share decline of at least 20%.
No lounge visits.
No reward redemptions.
At least one complaint.
At least one dispute.
At least one benefit denial.
NPS of zero or below._

Severe signals include:
_Two or more complaints.
NPS of -20 or below.
Two or more benefit denials._

**CUST1003 analysis**
CUST1003 is the highest-priority customer.

Risk signals:

NPS is -40, which is a severe-risk signal.
Two complaints were recorded, another severe-risk signal.
One dispute was recorded.
Total 90-day spend is 3,100, below the 4,000 threshold.
Travel share declined by 40%.
No lounge visits occurred.
No rewards were redeemed.

This customer shows both serious dissatisfaction and declining engagement.

Eligible offers:

OFF007: Service Recovery Credit and Benefit Review.
OFF008: Retention Specialist Email Outreach.

Recommended action:

Select OFF008 first.
Investigate and resolve the customer’s underlying concerns before presenting a generic financial incentive.

OFF001, the annual-fee credit, is not allowed because the customer has a recent dispute.

CUST1001 analysis

CUST1001 is also high risk, but the indicators are less severe.

Risk signals:

Travel share declined by 35%.
No lounge visits occurred.
No rewards were redeemed.
One complaint was recorded.
One benefit denial was recorded.
NPS is -10.

The customer is still spending, but appears to be receiving limited value from the card’s premium benefits and has experienced a service problem.

Eligible offers:

OFF002: 10,000 Bonus Points.
OFF006: 2x Rewards Accelerator.
OFF007: Service Recovery Credit and Benefit Review.
OFF008: Retention Specialist Email Outreach.

Recommended action:

Select OFF007.
Resolve the service and benefit issue with a benefit review and a controlled $50 courtesy credit.

OFF007 is more relevant than bonus points because it directly addresses the customer’s negative experience.

**4. Final solution**

The final design is a human-controlled Premium Retention Assistant.

How it works
_The agent reads the customer, RM, and offer-policy data every day.
It validates the input data and calculates the days until renewal.
It selects customers who are 30–90 days from renewal.
It evaluates the general and severe risk rules.
It ranks customers by severity, number of signals, renewal proximity, and value.
It evaluates the offer-policy rules, exclusions, validity dates, costs, and annual caps.
It selects the lowest-cost eligible offer that addresses the customer’s primary problem.
It generates evidence-based risk reasons and a personalized email draft.
It records the result in the Retention Recommendation sheet.
It emails the RM that the recommendation is ready._

Human role
The agent does not contact customers or modify their accounts.

The Relationship Manager must:
_Review the supporting risk signals.
Approve, edit, reject, or defer the recommendation.
Decide whether to contact the customer.
Apply any approved offer through the existing process._

**Final recommendations**
Priority	Customer	Main problem	Recommended action
1	CUST1003	Severe dissatisfaction and declining engagement	OFF008: Retention Specialist Email Outreach
2	CUST1001	Benefit underuse and recent service failure	OFF007: $50 Service Recovery Credit and Benefit Review
Success measures

Primary metric: Renewal rate among agent-identified at-risk customers.

Secondary metric: Percentage of recommendations reviewed and actioned by RMs before renewal.

Supporting measures include:

RM time saved.
Offer acceptance rate.
Recommendation approval and override rates.
Policy-compliance accuracy.
Customer response rate.
Retention cost per customer.

The essential design principle is to use deterministic rules for risk and offer eligibility while using generative AI only for explanations and communication drafts. This makes the solution personalized and efficient while remaining understandable, auditable, and under human control.
