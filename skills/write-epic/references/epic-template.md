# Epic template — section by section

This is the canonical output format for an epic. Sections appear in this order.
ID conventions: **BR-001** business rules, **FR-001** functional requirements,
**AF-01** alternative flows. Every epic is written in English.

Section status:
- **Required**: Summary, Objectives & business value, Current situation, Scope,
  Users & roles, Functional flow, Business rules, Functional requirements,
  Assumptions & dependencies.
- **Conditional**: Non-functional requirements, Integrations with other systems,
  Success metrics — include them when there is real input; never fabricate them
  (see the missing-information protocol in SKILL.md).

---

## 1. Summary

A brief description of the initiative. It must let anyone quickly understand
what is going to be done, why, and for whom.

What it should answer:
- What is going to be built?
- What problem exists today?
- Who is the user?
- What do we want to achieve?

**Example**

> An automatic commission settlement module will be implemented for the sales
> team. The process is currently manual in Excel, produces errors, and takes 3
> business days. The new system will calculate commissions in real time when each
> sale is closed, reducing errors and processing time.

---

## 2. Objectives and business value

Describe the expected impact of the initiative, both for the business and for the
user. Whenever possible it must be measurable.

What to include:
- Primary objective (what change is being pursued)
- Secondary objectives (additional improvements)
- Associated metrics

**Examples**
- Reduce processing time by 60%
- Reduce manual errors by 80%
- Increase the "Cart" → "Successful payment" conversion by 10%

---

## 3. Current situation

Describe how the process works today. This section is key to understanding the
problem and to validating that the solution actually solves it.

What to include:
- Current process, step by step
- Systems involved
- Problems detected
- Impact of the problem (time, cost, errors, experience)

---

## 4. Scope of the solution

Clearly define what this initiative includes and what it does not. This is
essential to avoid misunderstandings and scope creep.

What to include:
- In scope
- Out of scope, ideally with a justification

---

## 5. Users and roles involved

A list of every profile that will interact with the system. For each one, detail
what they can do and what information they can see.

**Example**
- **Salesperson**: can view their own commissions and export a monthly summary
- **Supervisor**: can view the team's commissions and approve adjustments
- **Administrator**: full system configuration

---

## 6. Functional flow

Describe how the solution will work end to end. This is a general view of the
system's behavior before going into requirement-level detail.

What to include:
- Sequence of steps
- Interaction between user and system
- References to business rules where applicable

**Example**

1. The user (salesperson or supervisor) presses the "Confirm sale" button on the
   sale detail screen.
2. The system validates that every product in the sale has a commission category
   assigned.
3. The system checks whether any item has a discount applied and, if so,
   evaluates the percentage against the list price.
4. The system determines the calculation base per item according to the business
   rules (see BR-001 and BR-002).
5. The system calculates the commission for each item and sums the total
   commission for the sale.
6. The system records the calculated commission in the salesperson's history and
   changes the sale status to "confirmed".
7. The system displays the confirmed-sale summary on screen, including the
   commission amount generated.

---

## 7. Business rules

Define the conditions and logic that govern the system. They must be clear,
explicit, and unambiguous.

Recommended format:
- A clear description
- The rule in conditional form (when applicable)

**Example**

**BR-001 — Standard calculation base**
The commission is calculated on the final sale price (with discount):
`commission = final price × commission % by product category`. The
percentage-by-category table is managed by the Finance team and must be in effect
at the moment of confirmation.

**BR-002 — Exception for discounts greater than 15%**
If the discount applied to an item exceeds 15% of the list price, the commission
calculation base becomes the list price (without discount) instead of the final
price. The commission percentage applied is the same as in BR-001.

**BR-003 — Sales with multiple items**
In sales with more than one product, the commission is calculated per item
independently and then summed. Each item may have a different category and a
different discount.

**BR-004 — Commission table validity**
The percentage table in effect on the sale's confirmation date is applied, not
the one in effect on the quote creation date.

---

## 8. Functional requirements

Describe the specific functionality the system must implement. They must be
clear, complete, and testable.

For each requirement include:
- ID (e.g. FR-001)
- Name
- Step-by-step description
- Rules and/or preconditions
- Acceptance criteria
- Alternative flows
- Edge cases

**Example**

### FR-004 — Commission calculation per sale

When a sale is confirmed, the system must automatically calculate the
salesperson's commission.

Calculation rule: `commission = amount × percentage by category`

**Edge cases**
- *Discount of exactly 15%* — the threshold is "greater than 15%", so a discount
  of exactly 15% does not trigger BR-002. The calculation base is the final price
  (with discount).
- *Sale with a 100% discount* — the final price would be $0, but since it exceeds
  15%, BR-002 applies and the commission is calculated on the list price.
- *Salesperson with no commission percentage assigned* — if the salesperson's
  profile has no active commission category, the system cannot determine the
  percentage. Apply the same behavior as AF-01, showing the affected
  salesperson's name in the error message.
- *Sale with a single $0 item (complimentary product)* — the resulting commission
  is $0. The system must allow confirmation and record a $0 commission without
  error. It must not be treated as an exception.

**Alternative flows**
- **AF-01 — Product with no category assigned.** At step 2, if at least one
  product has no commission category assigned, the system blocks confirmation and
  shows: "The sale cannot be confirmed. Product [product name] has no commission
  category defined. Contact the administrator." The sale remains in "pending"
  status. No commission is recorded.
- **AF-02 — Commission table with no valid data.** At step 4, if no percentage
  table is in effect for the current date, the system blocks confirmation and
  shows: "The commission cannot be calculated. There is no commission table in
  effect. Contact Finance." The sale remains in "pending" status.
- **AF-03 — Error recording the commission.** At step 6, if the system cannot
  record the commission due to a technical error, the sale confirmation is rolled
  back, the status returns to "pending", and the system shows: "An error occurred
  while recording the commission. Try again or contact support." An internal
  error log is generated.

**Acceptance criteria**
- When a valid sale is confirmed, the system calculates and records the
  commission with no manual intervention. *(covers BR-001)*
- The percentage applied corresponds to the commission table in effect on the
  confirmation date. *(covers BR-004)*
- If an item has a discount greater than 15%, the base is the list price, not the
  final price. If the discount is exactly 15%, the base is the final price.
  *(covers BR-002 and edge case)*
- In sales with multiple items, the total commission is the sum of the individual
  per-item commissions. *(covers BR-003)*
- If a product has no category assigned, the sale is not confirmed and the error
  message shows the product name. *(covers AF-01)*
- If there is no commission table in effect, the sale is not confirmed and the
  corresponding message is shown. *(covers AF-02)*
- If a technical error occurs while recording, the confirmation is rolled back
  and the sale returns to "pending" status. *(covers AF-03)*
- A sale with a $0 commission (complimentary item) is confirmed without error and
  records a $0 commission. *(covers edge case)*

> Every acceptance criterion should trace back to a business rule, alternative
> flow, or edge case — annotate the coverage as shown above.

---

## 9. Non-functional requirements

*Conditional section.* Everything the system must satisfy beyond its functional
behavior. Not mandatory, but clients sometimes have specific requirements of this
kind.

- **Performance**: expected response times, maximum user load
- **Availability**: required uptime, maintenance windows
- **Security**: authentication, authorization (least privilege), encryption,
  auditing/logging, data classification, and applicable legal compliance.
  Security requirements are specified and approved as part of the analysis — they
  are not optional when the initiative handles data or access. See the
  "Information security" tab.
- **Compatibility**: browsers, operating systems, devices
- **Usability**: accessibility, language, internationalization
- **Scalability**: expected data volume at 1, 3, and 5 years

---

## 10. Integrations with other systems

*Conditional section.* Describe how the new system connects to existing systems:
what data it receives, what it sends, how often, and over which protocol.

- **Source / target system**: name of the system being integrated with
- **Integration type**: REST API, flat file, database, message queue, etc.
- **Data exchanged**: what information flows in each direction
- **Frequency**: real time, daily batch, on demand, etc.

---

## 11. Assumptions and dependencies

List everything taken for granted while writing this document, and what external
factors can affect the project. Key for managing risk and expectations.

- **Assumptions**: conditions assumed to be true (e.g. system A already exposes
  that data)
- **Dependencies**: other projects, teams, or deliverables this project needs
- **Constraints**: known limitations such as technology or regulations
- **Identified risks**: what could go wrong and how to mitigate it

---

## 12. Success metrics

*Conditional section.* Define how the real impact of the solution will be
measured once implemented.

**Examples**
- % of the process that is automated
- Average execution time
- Error rate
