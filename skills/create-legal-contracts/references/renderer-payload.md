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
  "sections": [
    {
      "title": "1. Parties",
      "body": "Section text",
      "bullets": []
    }
  ],
  "legal": {}
}
```

Include the documented colors, fonts, company details and exact `{scope,path}` logo reference when `apply_identity` is true. Never pass JavaScript `undefined`; omit optional keys instead.

There is no `v` key in a legal payload, and no `blocks`. The typed content blocks the tool schema also documents (`v: 2`) are for commercial and general documents; the renderer rejects `document_kind: legal` on a block body, because the contract sheet, the parties table, the placeholder register and the signature blocks are built from `legal` paired with `sections`.

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
