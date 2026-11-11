# Recurring Stock Discrepancies
## From the headline balance to the next action

![Power BI Dashboard](Recurring%20Stock%20Dicrepancies.png)

## Overview

This project investigates recurring stock discrepancies in a fictional SME warehouse using Power BI, Power Query and a purpose-built synthetic dataset.

The analysis begins with stock variance over time, then moves beneath the warehouse total to examine discrepancy frequency, individual bin balances and the receipt or movement records behind selected differences.

The central business question is:

> When the same stock differences keep returning, what should the business investigate before posting another correction?

The case study follows a practical reporting workflow:

**Synthetic stock tables → Power Query preparation → Power BI measures and visuals → movement reconciliation → operational recommendations**

It is relevant to warehouse managers, SME owners, operational teams and ERP reporting users, including those working with SAP Business One. The dataset is not a SAP Business One export and does not reproduce SAP or add-on database structures.

---

# The Problem

A warehouse-level total can agree while individual bin balances remain unreliable.

A shortage in one location may be offset by a surplus elsewhere. The headline variance is zero, but the person picking the stock still encounters a different quantity from the one shown in the system.

Visible discrepancies also need investigation. A count difference might require an adjustment, or it might be resolved by a movement posted after the count.

A generic adjustment reason such as “Count reconciliation” describes the correction without explaining why the discrepancy occurred.

The project therefore asks:

- Which products and bins show recurring discrepancies?
- Which differences disappear when bin quantities are combined?
- What do the underlying receipts, transfers and dispatches show?
- Which differences are resolved by later postings?
- What should the team investigate to reduce recurring corrections?

---

# The Approach

## Dataset and Scope

The synthetic dataset covers:

- One warehouse, WH01.
- 24 products.
- Three bins: RES-A, PICK-A and HOLD-A.
- 12 weekly stock-count cycles from 7 August to 23 October 2026.
- 864 item/bin/cycle count observations.
- Physical warehouse tasks, system movement lines and approved count adjustments.

The source package includes an Excel workbook and CSV tables:

| Table | Purpose |
|---|---|
| Products | Product names, case sizes and illustrative standard unit costs |
| Bins | Warehouse locations and bin descriptions |
| UOM | Reference conversions by product and unit of measure |
| OpeningStock | Starting balances by product and bin |
| WarehouseTasks | Completed physical receipts, transfers, dispatches and other tasks |
| StockLedger | Recorded system movements, quantities and posting dates |
| CountCycles | Count dates and review cut-offs |
| StockCounts | Frozen system balances, first counts, recounts and accepted counts |
| Adjustments | Approved residual count adjustments |

A data dictionary accompanies the source package.

## Stage 1: Identify the Pattern

The first stage compares accepted counts with frozen system quantities.

The analysis examines:

- Signed stock variance over time.
- Discrepancy frequency by product and bin.
- Absolute standard-cost exposure.
- Detailed bin differences for products with a zero warehouse-level variance.

Accurate counts remain in the frequency denominator. This allows the report to distinguish occasional differences from repeated discrepancies.

## Stage 2: Trace the Supporting Records

The second stage follows selected discrepancies back to their evidence.

The investigation compares:

- Receipt quantities and units of measure with the conversion applied.
- Physical transfer destinations with recorded system destinations.
- Physical movement dates with system posting dates.
- Count variances with subsequent postings and approved adjustments.

The receipt and transfer examples use the 11 September count. The late-posting example uses the 18 September count, where the selected dispatch timing differences are present.

---

# The Solution

## Power Query Preparation

The investigation combines system movement records with supporting warehouse tasks using task IDs.

Reference unit conversions are matched using both the product and unit of measure. This composite match avoids treating a unit such as CASE as if it had the same conversion for every product.

Transfers require particular care: one physical task can produce multiple system movement lines. The outgoing and incoming lines represent different sides of the transfer and should not be treated as duplicate transactions.

The approach preserves the distinction between:

- Physical activity.
- Recorded system movements.
- Count snapshots.
- Approved count adjustments.

## Power BI Measures

The principal calculations are:

| Metric | Definition | Interpretation |
|---|---|---|
| Stock variance quantity | Accepted count minus frozen system quantity | Positive means counted stock exceeds the system balance; negative means a shortage |
| Discrepancy frequency | Discrepant observations divided by all count observations in the selected scope | Includes accurate counts in the denominator |
| Absolute standard-cost exposure | Sum of absolute item/bin/count variances multiplied by product standard unit cost | Shows discrepancy scale without allowing positive and negative quantities to cancel |
| Late posting quantity | Net signed movements that occurred by the count but were posted after it and by review | Identifies timing differences relevant to that count |
| Approved adjustment quantity | Approved residual count correction | Separates adjustments from ordinary late postings |
| Unexplained variance quantity | Stock variance minus late posting quantity minus approved adjustment quantity | Checks whether the selected difference reconciles |

A zero unexplained variance means the numerical reconciliation is complete. It does not establish that the underlying process has improved or that the reason for the mismatch is fully understood.

---

# Dashboard Highlights

## Headline Variance and Discrepancy Frequency

The initial chart shows which products have visible warehouse-level differences.

The product/bin matrix then reveals recurring discrepancies that the headline chart conceals. Frequency is considered alongside absolute standard-cost exposure so that repeated location problems remain visible.

## Detailed Bin Comparison

Mounting brackets and rubber feet are examined across all 12 cycles.

Signed bin quantities show how a shortage in HOLD-A is offset by a surplus in PICK-A, leaving a zero warehouse-level variance.

## Receipt and Transfer Investigation

The supporting detail compares expected receipt quantities with system quantities, and physical transfer destinations with recorded destinations.

This connects the variance to specific evidence rather than relying on a general adjustment reason.

## Late Posting Reconciliation

The final investigation separates differences resolved by later dispatch postings from those requiring approved adjustments.

The report closes with practical checks and recommendations.

---

# Key Findings

## 1. Zero Warehouse Variance Can Conceal Recurring Bin Differences

Mounting brackets, rubber feet and safety clips show discrepancies in both HOLD-A and PICK-A during nine of the 12 cycles: **75% of observations in each affected bin**.

The positive and negative differences cancel at warehouse level.

For example, in cycle CC02:

- Mounting brackets show −11 units in HOLD-A and +11 in PICK-A.
- Rubber feet show −12 units in HOLD-A and +12 in PICK-A.

Both products have a warehouse-level variance of zero for that cycle.

## 2. Receipt Conversion Mismatches Explain Selected Visible Differences

For the 11 September count:

| Product | Receipt quantity | Expected units per case | Expected total units | System total units | Difference |
|---|---:|---:|---:|---:|---:|
| M8 washers | 4 cases | 12 | 48 | 4 | +44 |
| Packing sleeves | 4 cases | 10 | 40 | 4 | +36 |

Both entries show CASE as the entered unit of measure, but the applied conversion factor is one.

The evidence identifies a conversion mismatch. It does not establish whether setup, entry behaviour or another process caused that mismatch.

## 3. Recorded Bin Destinations Differ From Physical Destinations

The supporting transfer records for the 11 September count show movements from RES-A to PICK-A.

The system records the destination as HOLD-A.

The selected quantities are seven mounting brackets, eight rubber feet and eight safety clips. This explains the corresponding surpluses in PICK-A and shortages in HOLD-A.

The evidence supports investigating how destinations are selected and confirmed. It does not demonstrate a fault in SAP Business One or any warehouse add-on.

## 4. Late Dispatch Postings Resolve Selected Shortages

At the 18 September count:

- Carton tape shows a shortage of ten units.
- Foam pads show a shortage of eight units.

The dispatches occurred before the count but were posted on 21 September.

Those later postings account for the shortages. No additional stock adjustment was needed for those rows.

## 5. A Completed Reconciliation Is Not the Same as a Resolved Process Issue

The selected reconciliation rows have no remaining unexplained quantity after late postings and approved adjustments are considered.

That demonstrates how the differences were resolved numerically. Further investigation is still needed to establish why conversion or destination mismatches occurred and whether corrective action would reduce recurrence.

---

# Operational Recommendations

## 1. Check Receipt Conversions

Compare receipt quantities, units of measure, units per case and resulting stock quantities. Investigate why CASE entries used a conversion factor of one and validate the relevant setup and receipt process.

## 2. Compare Physical and Recorded Destinations

Review the transfers recorded to HOLD-A when the supporting records show PICK-A. Involve warehouse staff in identifying where destination selection or confirmation diverges from physical handling.

## 3. Review Outstanding Postings Before Adjusting Stock

Use the count cut-off to identify movements completed before the count but posted afterwards. Reconcile those movements before approving an additional adjustment.

## 4. Record Explanations That Support Follow-Up

Alongside the correction, record the supported explanation, evidence reviewed, person responsible and any remaining uncertainty.

“Count reconciliation” alone provides little help in preventing a repeat.

## 5. Monitor Whether the Pattern Changes

Review discrepancy frequency, absolute differences and bin-level variances at subsequent counts. Retain accurate observations in the denominator and compare recurrence before and after any future process change.

---

# Data Limitations

This is an illustrative case study using synthetic data.

- All quantities, costs and operational records are fictional.
- Patterns were deliberately designed for analysis; their frequency does not estimate prevalence in real warehouses.
- The dataset is not an actual SAP Business One or Produmex export.
- The selected investigations do not explain every discrepancy in the full dataset.
- No process intervention or measured improvement period is included.
- No realised savings, financial losses or service-level benefits are established.
- Standard-cost exposure is an analytical measure, not confirmed financial loss.
- Absolute bin exposure includes both sides of offsetting location differences and may include recurring differences across several counts.
- Physical event dates, posting dates and count cut-offs have different meanings and must be aligned before reconciliation.
- Generic reason codes describe adjustments rather than proving their operational causes.

The conclusions identify useful investigation paths. They do not replace validation against a real organisation’s documents, system configuration and working practices.

---

# Demo

Suggested walkthrough order:

1. Read the executive summary in the accompanying case study PDF.
2. Review stock variance over time.
3. Compare discrepancy frequency and absolute exposure by product and bin.
4. Examine the signed bin differences for mounting brackets and rubber feet.
5. Follow the 11 September receipt and transfer evidence.
6. Review the 18 September late-posting reconciliation.
7. Read the closing recommendations.

The PDF can be read independently of Power BI. The synthetic source tables support reproducing and extending the analysis.

---

# How to Run

## Requirements

For reading the case study:

- A PDF reader.

For reproducing the analysis:

- Power BI Desktop.
- The synthetic Excel workbook or CSV source tables.
- Power Query for preparing the investigation tables.

No SQL database is required for the workflow used in this case study.

## Steps

1. Download the case study PDF and synthetic source package.
2. In Power BI Desktop, import the workbook tables or the CSV files.
3. Set identifiers to text, quantities and costs to appropriate numeric types, and timestamps to date/time.
4. Use Products and Bins as shared dimensions, and connect CountCycles to StockCounts by cycle ID.
5. Keep count and movement facts at their own grains. Match unit conversions by product and unit of measure.
6. Create the variance, frequency, exposure and reconciliation measures described above.
7. Prepare movement evidence by matching ledger movements to warehouse tasks.
8. Filter the receipt and transfer investigation to the relevant activity preceding the selected count.
9. For late postings, retain movements occurring by the count but posted after it and by review.
10. Validate reconciliation at individual item/bin/count level before interpreting totals.

Approved adjustment movements are already represented in StockLedger. Do not append Adjustments as additional ledger movements.

When using physical task quantities in a merged evidence table, account for transfers with multiple ledger lines before aggregating.

---

# Suggested Business Use

This case study demonstrates an approach for:

- Investigating recurring stock adjustments.
- Identifying bin differences hidden by warehouse totals.
- Reviewing receipt unit conversions.
- Comparing physical movements with recorded locations.
- Separating late postings from residual count corrections.
- Supporting practical discussions between warehouse staff, managers and ERP support teams.

The approach can be adapted to SAP Business One or another stock system once its source records, relationships, units and count cut-offs have been verified.

> A useful stock report helps the team explain the difference, resolve it appropriately and recognise whether it returns.

---

# Tools Used

- Power BI Desktop.
- Power Query.
- DAX.
- Synthetic Excel and CSV source data.
- PDF report export.

GitHub and Kaggle are publication platforms for the portfolio case study.

---

# Project Status

Completed as a portfolio case study for StraightLine Data & Training, November 2026.

Potential future development includes:

- Testing the approach with permitted, anonymised operational data.
- Recording investigation outcomes and follow-up ownership.
- Validating targeted checks with warehouse users.
- Measuring recurrence after an implemented process change.

No improvement results are claimed in this version.

**StraightLine Data & Training**  
Clarity. Insight. Confidence.  
[www.straightlinedata.co.uk](https://www.straightlinedata.co.uk/)

