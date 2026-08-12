# Legal renderer payload

Use the native tool schema as authoritative. This reference highlights the legal fields that must not be renamed.

## Required top-level values

```json
{
  "layout": "document",
  "formats": ["docx", "pdf"],
  "document_kind": "legal",
  "base_name": "safe-file-name",
  "language": "it",
  "title": "Contract title",
  "brand": {
    "apply_identity": true,
    "company_name": "Confirmed company legal or trading name",
    "colors": {
      "primary": "#112233",
      "secondary": "#445566"
    },
    "fonts": {
      "heading": "Confirmed heading font",
      "body": "Confirmed body font"
    },
    "company_details": [],
    "contact_lines": []
  },
  "v": 2,
  "blocks": [
    { "kind": "heading", "text": "1. Parties", "level": 2 },
    { "kind": "text", "body": "Clause text" }
  ],
  "legal": {}
}
```

Include the documented colors, fonts, company details and exact `{scope,path}` logo reference when `apply_identity` is true. Never pass JavaScript `undefined`; omit optional keys instead.

The body is the same block catalog every other document uses; `legal` travels beside it and is what the renderer turns into the contract sheet, the parties table, the register of fields to fill, the annexes and the signature blocks. Never write those as blocks: they would be printed twice.

The older flat `sections` body still renders, so a contract you generated before the change stays reproducible. New contracts use blocks.

## Legal object

Use exact enum values:

- `mode`: `template` or `filled`;
- `contract_type`: `collaboration`, `professional-services`, `consulting`, `nda`, `supplier`, `license-ip`, `data-processing`, `employment`, `partnership`, `maintenance-sla`, `sponsorship`, `assignment`, `settlement-release`, or `custom`;
- `review_status`: `draft` or `review-required`.

Use these object shapes:

```json
{
  "mode": "template",
  "contract_type": "collaboration",
  "jurisdiction": "Italia",
  "review_status": "review-required",
  "parties": [
    {
      "role": "Committente",
      "name": "{{RAGIONE_SOCIALE_COMMITTENTE}}",
      "details": []
    },
    {
      "role": "Collaboratore",
      "name": "{{NOME_COLLABORATORE}}",
      "details": []
    }
  ],
  "clause_modules": ["scope-deliverables", "confidentiality", "signatures"],
  "placeholders": [
    {
      "key": "RAGIONE_SOCIALE_COMMITTENTE",
      "label": "Ragione sociale del committente",
      "required": true
    },
    {
      "key": "NOME_COLLABORATORE",
      "label": "Nome o ragione sociale del collaboratore",
      "required": true
    }
  ],
  "signature_blocks": [
    {
      "role": "Committente",
      "name": "{{RAGIONE_SOCIALE_COMMITTENTE}}"
    },
    {
      "role": "Collaboratore",
      "name": "{{NOME_COLLABORATORE}}"
    }
  ],
  "annexes": [
    {
      "code": "A",
      "title": "Scope of Work",
      "status": "placeholder"
    }
  ],
  "specific_approvals": [
    {
      "section_reference": "Art. 8",
      "label": "Limitazione di responsabilità"
    }
  ],
  "module_parameters": {
    "confidentiality": {
      "protected_information": "Defined protected information",
      "exclusions": "Defined exclusions",
      "permitted_disclosures": "Defined permitted disclosures",
      "duration": "Defined duration"
    }
  }
}
```

`parties` and `signature_blocks` require at least two entries. `annexes[].status` must be `included`, `placeholder`, or `external`. Every template placeholder key must be `UPPER_SNAKE_CASE`, must appear as `{{KEY}}` in rendered text, and must be declared exactly once. A filled contract must use `placeholders: []` and contain no `{{...}}`.

For sensitive modules, use the exact parameter keys required by the tool:

- `non_compete`: `relationship`, `restricted_activities`, `territory`, `duration`, and employee `consideration` when applicable;
- `liability`: `cap`, `exclusions`, `indemnities`;
- `data_processing`: `roles`, `subject_and_duration`, `nature_and_purpose`, `personal_data_and_subjects`, `instructions_and_security`, `subprocessors`, `return_or_deletion`.

When validation fails, read the returned field path, correct the payload and retry the renderer. Do not stop after a correctable schema error and do not save partial output.
