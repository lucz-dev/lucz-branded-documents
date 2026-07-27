# Clause modules

Select only modules relevant to the requested relationship. Ask for the parameters below before drafting restrictive or risk-allocation language.

| Module | `clause_modules` value | Confirm before use |
| --- | --- | --- |
| Scope and deliverables | `scope-deliverables` | Activities, outputs, dependencies, milestones, change process, acceptance |
| Compensation and payment | `compensation-payment` | Amount/basis, currency, VAT/tax wording, invoicing, due dates, expenses, late-payment treatment |
| Term and renewal | `term-renewal` | Start/effective date, fixed/indefinite term, renewal mechanism, notice |
| Termination | `termination` | Convenience/cause rights, cure, notice, accrued fees, return/transition, survival |
| Confidentiality | `confidentiality` | Protected information, purpose, exclusions, permitted recipients/disclosures, safeguards, duration, return/destruction |
| Non-compete | `non-compete` | Relationship, legitimate purpose, restricted activities, territory, duration, consideration when applicable; mandatory professional review |
| Non-solicitation | `non-solicitation` | Protected people/customers, prohibited conduct, duration, territory/context, exceptions |
| Intellectual property | `intellectual-property` | Background IP, new work, assignment/licence, scope, territory, term, source/materials, moral-right and open-source treatment |
| Data protection | `data-protection` | Roles and all processing parameters; use a DPA annex where required |
| Liability | `liability` | Cap/basis, excluded losses, carve-outs, causation, insurance and mandatory-law exceptions |
| Indemnity | `indemnity` | Triggering third-party claims, covered loss, control of defence, notice, settlement consent, exclusions |
| Warranties | `warranties` | Express warranties, conformity standard, remedy, duration, disclaimers allowed by law |
| Independent relationship | `independent-contractor` | Autonomy, no subordination/agency authority, organization of work, taxes/contributions, equipment, other clients |
| Force majeure | `force-majeure` | Events, mitigation, notice, suspension, long-stop termination, payment obligations |
| Notices | `notices` | Addresses/PEC/email, deemed receipt, changes, operational versus formal notices |
| Assignment/subcontracting | `assignment-subcontracting` | Consent, permitted affiliates, continuing liability, subprocessors/subcontractors |
| Law and forum | `governing-law-forum` | Applicable law, exclusive/non-exclusive forum or arbitration, mandatory-law limits |
| Dispute resolution | `dispute-resolution` | Escalation, mediation/arbitration rules, seat, language, interim relief, costs |
| Specific written approval | `specific-written-approval` | Exact section references and labels after reviewing whether the document is a standard form |
| Signatures | `signatures` | Party roles, legal names, signatories/titles, representation powers, signing method |
| Custom | `custom` | User-approved purpose and complete parameters |

## Sensitive structured parameters

When selected, pass these fields to `legal.module_parameters`:

- `confidentiality`: `protected_information`, `exclusions`, `duration`, `permitted_disclosures`;
- `non_compete`: `relationship`, `restricted_activities`, `territory`, `duration`, and `consideration` for an employee relationship;
- `liability`: `cap`, `exclusions`, `indemnities`;
- `data_processing`: `roles`, `subject_and_duration`, `nature_and_purpose`, `personal_data_and_subjects`, `instructions_and_security`, `subprocessors`, `return_or_deletion`.

The renderer rejects a selected sensitive module whose structured parameters are absent.

## Standard annex workflow

1. Ask whether an approved organization annex already exists in Knowledge.
2. Reuse approved text without silently rewriting it; identify the version/source.
3. Otherwise offer relevant annexes and mark each as:
   - `included`: included in the generated contract;
   - `placeholder`: expected but still to be completed;
   - `external`: referenced but stored separately.
4. Common annexes: scope/SOW, fee schedule, SLA, acceptance test, DPA, technical and organizational measures, IP/assets, brand-use rules, security schedule, authorized subprocessors.
5. Keep annex codes stable (`A`, `B`, `SOW-1`, etc.) and cross-reference them from the main terms.

## Review guardrails

- Standard conditions or formularies may require specific approval of selected onerous clauses under applicable law. Generate a dedicated block only from exact section references; do not assert completeness.
- Non-compete rules differ by relationship and jurisdiction. Never reuse employee wording for a business or independent-contractor relationship without review.
- Data-processing wording must reflect the actual processing. A generic privacy sentence is not a DPA.
- Liability, indemnity, employment, consumer, settlement and forum clauses always merit professional review before signature.
