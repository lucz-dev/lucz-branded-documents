# Contract types and fact checklist

Use this catalog to select `legal.contract_type`. It is a drafting aid, not a statement that a named form is correct in every jurisdiction.

## Common types

| Request | `contract_type` | Minimum facts to confirm |
| --- | --- | --- |
| Collaboration / independent professional | `collaboration` | Parties and roles, autonomous relationship, activity and deliverables, fee/expenses/tax treatment, schedule, acceptance, IP, confidentiality, term/termination, notices, law/forum, signatures |
| Professional services / work | `professional-services` | Work or service, deliverables, acceptance, fee, changes, dependencies, term, IP, warranties, liability, termination |
| Consulting | `consulting` | Scope and advisory limits, availability, deliverables, fee, expenses, confidentiality, IP, conflicts, term/termination |
| NDA / confidentiality | `nda` | Discloser/recipient or mutual roles, protected information, purpose, exclusions, permitted disclosure, care standard, duration, return/destruction, remedies, law/forum |
| Supply | `supplier` | Goods/services, specifications, quantities, orders, delivery/risk, inspection/acceptance, price, warranties, recalls, liability, term |
| IP licence | `license-ip` | Rights/assets, ownership, scope, territory, term, exclusivity, sublicensing, fees/royalties, attribution, enforcement, termination effects |
| Data processing agreement | `data-processing` | Controller/processor roles, processing subject/duration, nature/purpose, data and subjects, instructions, security, subprocessors, assistance/audits, return/deletion, transfers |
| Employment | `employment` | Jurisdiction and applicable collective rules, role, level, location, hours, compensation/benefits, probation, leave, policies, IP/confidentiality, termination; professional review required |
| Partnership / joint initiative | `partnership` | Purpose, contributions, governance, decision rights, economics, IP/data, exclusivity, term/exit, deadlock, liability |
| Maintenance / SLA | `maintenance-sla` | Covered systems, hours, severity levels, response/restore targets, exclusions, maintenance windows, credits/remedies, security, term |
| Sponsorship | `sponsorship` | Event/activity, rights and placements, exclusivity/category, approvals, fee/in-kind value, reporting, cancellation, brand use |
| Assignment | `assignment` | Assigned right/contract, consideration, consents, effective date, warranties, notices, retained obligations |
| Settlement / release | `settlement-release` | Dispute/claims, payment or performance, release scope, no-admission treatment, confidentiality, enforcement, tax, signatures; professional review required |
| Other | `custom` | Purpose, parties, obligations, consideration, risk allocation, dates, termination, law/forum and signatures |

## Mode checklist

### Generic template

- Keep the organization profile only when the template is organization-specific.
- Represent every unknown with a declared `{{UPPER_SNAKE_CASE}}` token.
- Include a field register and annex status.
- Do not include fictional tax IDs, addresses, dates or signatories.

### Filled contract

- Confirm the exact legal names and roles of at least two parties.
- Confirm the deal facts and all values used in sensitive modules.
- Remove every placeholder.
- Keep signature lines blank only as visual signing areas, not as unresolved data tokens.

## Official-law routing

For Italian-law drafting, review the current official Civil Code text on [Normattiva](https://www.normattiva.it/eli/stato/REGIO_DECRETO/1942/03/16/262/CONSOLIDATED) before relying on contract-form, work, employment, non-compete or standard-terms requirements.

For processor contracts, review Article 28 GDPR and applicable official standard clauses on [EUR-Lex](https://eur-lex.europa.eu/eli/reg/2016/679/oj). The generated DPA module must capture the processing-specific facts; it must not claim compliance merely because a heading exists.
