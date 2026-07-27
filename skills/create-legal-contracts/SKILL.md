---
name: create-legal-contracts
description: Create, complete, and publish legal contract drafts as equivalent DOCX and PDF files using confirmed organization legal data and optional brand identity. Use for generic contract templates with placeholders, filled contracts for named parties, collaboration and professional-services agreements, NDAs, DPAs, addenda, releases, policies, terms, legal notices, standard clause modules, annexes, signature blocks, and any request to draft or generate a contract or legal document.
---

# Create Legal Contracts

Create reviewable legal drafts through the native legal profile and document tools. Never present generated wording as legal advice, certified validity, or a substitute for review in the applicable jurisdiction.

## Load the company contract profile

Call `get_legal_contract_profile` before asking questions or drafting.

If `found` is false:

1. Search the organization Knowledge for `profilo_aziendale`, company identifiers, registered office, representative, contact channels, and existing approved legal templates. Treat these as candidate facts, not confirmation.
2. Call native `ask_user` with one form of at most four grouped questions. Show the facts found and the missing fields in the questions. Cover:
   - legal name, trading name, legal form, VAT/tax identifiers, registry and addresses;
   - country, contract language, desired governing law and forum;
   - legal representative, signing method, PEC/notice email;
   - stable payment, invoicing, privacy, insurance and standard-annex preferences.
3. Offer concise choices such as “confirm the listed data”, “update in free text”, and “leave missing for templates”. The native form always permits free text. Do not ask the same field twice.
4. Save only facts the user confirmed by calling `save_legal_contract_profile` with `confirmed_by_user: true`. List every unknown value in `missing_fields`; never invent it.
5. Verify that the tool reports both the Knowledge profile and organization memory saved. If either write fails, stop and retry idempotently before generating files.

If the profile exists, use its confirmed values and ask only for missing fields that materially affect the requested contract. Update it through `save_legal_contract_profile` when the user confirms stable corrections or additions. Do not save deal-specific counterpart data as the organization profile.

The deterministic documents are:

- Knowledge: `legal/legal_contract_profile`;
- memory: `memory/memory_org_profilo_contrattuale_aziendale`.

## Resolve brand identity

Look for the organization memory `memory_org_preferenza_brand_documenti_legali`.

- If it contains an active choice, apply it without asking again.
- Otherwise call native `ask_user` with exactly two single-select questions in one form:
  1. use the company logo, colors and fonts, or a neutral legal style;
  2. remember the choice, or use it only for this document.
- If the user chooses to remember it, call `remember` with title `Preferenza brand documenti legali`, scope `org`, and a self-contained statement of the confirmed choice.
- If the choice is for this document only, do not write that memory.
- If `ask_user` is unavailable and no preference exists, stop without generating files.

For a branded output, retrieve exact values from `brand_identity`, `profilo_aziendale`, relevant memories, and the organization File Explorer `Brand` folder. Pass only an exact Vision `{scope,path}` logo reference. For a neutral output, set `brand.apply_identity: false` and omit the logo.

Before declaring onboarding assets missing, open `brand_identity` and `profilo_aziendale` directly in the configured Knowledge space and list the organization `Brand` folder. Do not substitute one generic search for those exact reads.

## Choose template or filled mode

Determine the mode explicitly:

- `template`: create a reusable generic contract with visible `{{UPPER_SNAKE_CASE}}` placeholders and a generated field register;
- `filled`: populate the parties and contract facts with user-confirmed values and allow no unresolved placeholders.

If the request is ambiguous, use `ask_user` before drafting. A request for a “facsimile”, “generic contract”, “model”, or “contract to fill in” means `template`. A request naming actual parties and asking to prepare their agreement normally means `filled`.

For template mode:

- declare every placeholder in `legal.placeholders`;
- use each declared placeholder at least once;
- include placeholders for missing party identifiers, dates, amounts, term, notices, law/forum, signatures, and annex data when relevant;
- never disguise a missing value as a reasonable default.

For filled mode:

- require at least two parties with their roles and confirmed legal names;
- obtain identifiers, addresses, signatories and deal terms when material;
- reject any remaining `{{PLACEHOLDER}}`;
- do not fill missing personal or company data from guesses or unrelated CRM notes.

Read [contract-types.md](references/contract-types.md) when selecting the contract type and required facts.

## Select and parameterize clause modules

Read [clause-modules.md](references/clause-modules.md) whenever the user asks for clauses, the contract type implies a sensitive module, or an existing template must be adapted.

Build the contract in this order:

1. Select the contract type and core modules.
2. Ask for deal-specific facts: purpose, scope/deliverables, consideration, dates, duration/renewal, termination, notices, law/forum and signatures.
3. Offer optional relevant modules without silently adding restrictive terms.
4. Parameterize each selected sensitive module:
   - confidentiality: protected information, exclusions, permitted disclosures and duration;
   - non-compete: relationship, restricted activity, territory, duration and any required consideration;
   - liability: cap, exclusions and indemnity allocation;
   - data processing: roles, subject/duration, purpose, data/categories, instructions/security, subprocessors and return/deletion.
5. Add standard annexes or placeholders for them. Typical candidates are scope/SOW, price schedule, SLA, privacy/DPA, security measures, IP schedule, acceptance criteria and authorized subprocessors.
6. Identify clauses that may require specific written approval in a standard form and list exact section references in `legal.specific_approvals`. Do not claim the list is legally exhaustive.

Never infer that non-compete, employment, consumer, privacy, liability or forum wording is enforceable. Require professional review for jurisdiction-sensitive or high-impact terms.

## Draft the contract

Use numbered, cross-referenced sections. Include, as applicable:

- title, date/effective-date treatment and party definitions;
- recitals and purpose;
- scope, deliverables, responsibilities and acceptance;
- consideration, invoicing, taxes and expenses;
- term, renewal, suspension, termination and effects;
- selected clause modules;
- notices, governing law, forum or dispute resolution;
- entire agreement, amendments, severability and counterparts/e-signature;
- annex index, specific-approval block where applicable, and signatures.

Make the relationship accurate. For collaboration or professional-services contracts, do not imply employment or subordination unless the user explicitly requests an employment agreement and provides the appropriate facts.

Use `review_status: review-required` unless the user supplies a professionally approved template and explicitly asks only for mechanical completion; even then, never certify review yourself.

## Render and save

Every legal deliverable must use:

- `document_kind: legal`;
- `layout: document`;
- `formats: [docx,pdf]`;
- structured `legal` metadata matching the selected mode, contract type, parties, modules, placeholders, annexes, approvals and signatures.

Call `render_branded_documents` once per coherent contract. The server must return one DOCX and one PDF with matching content, size, MIME type and SHA-256 metadata. If validation or either format fails, do not publish a partial set.

Save both outputs with `save_file`, `destination: files`:

- filled contract: `Legale/<counterparty-name>/<current-year>`;
- generic template: `Legale/Modelli/<current-year>`.

The `path` argument is mandatory on every legal `save_file` call. Never omit it and never accept the fallback `agent-outputs` folder.

Sanitize folder segments without changing the displayed legal names inside the document. Preserve the authoritative File Explorer paths returned by `save_file`.

Do not update the CRM for a legal document unless the user explicitly asks. If requested, write only after both files are saved; reuse an exact contact and make the action idempotent with the renderer run ID and both saved paths.

## Verify before responding

Confirm:

- the profile was loaded or onboarding was saved to both Knowledge and memory;
- mode is correct and filled documents contain no unresolved placeholders;
- every selected sensitive module has explicit parameters;
- PDF and DOCX contain equivalent party names, clauses, terms, annexes and signature blocks;
- both files exist under the required name/year folder and are downloadable;
- the output says it is a draft or requires review;
- no legal CRM action occurred unless explicitly requested.

Return both saved paths, the mode, contract type, selected modules, unresolved fields for templates, and any brand/font warning. Do not expose local workspace paths, private download URLs, credentials, or internal source material.
