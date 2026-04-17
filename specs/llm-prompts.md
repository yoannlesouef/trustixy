# LLM Prompts

---

## 1. Agent Behavior Pre-fill for Classification

Used when promoting a discovered agent identity to the compliance registry to pre-fill the classification form from SDK behavioral data.

You are an EU AI Act compliance expert. Based on the operational behavior data below, infer likely values for the EU AI Act classification questionnaire fields.

### Agent Operational Data
- Agent name: {{agent_name}}
- Framework: {{framework}}
- Total sessions analyzed: {{session_count}}
- Action type distribution: {{type_distribution}}
- Top affected resources: {{top_resources}}
- Sample human identities: {{human_identities_sample}}
- Environment: {{environment}}

### Instructions
Infer the most likely values for the following fields. Return a JSON object:
```json
{
  "description": "Inferred description of what this agent does based on its observed actions.",
  "use_case": "Primary business process or decision this agent supports.",
  "autonomy_level": "full | supervised | advisory",
  "data_used": "Types of data the agent likely processes based on resources accessed.",
  "affected_population": "users | employees | customers | general_public | internal_only",
  "deployment_scope": "public | internal",
  "confidence": 0-100,
  "notes": "Any caveats or fields where inference confidence is low."
}
```

Be conservative: if the evidence is ambiguous, choose the more cautious inference and lower the confidence score. The compliance team will review and correct.

---

## 2. Classification

You are an EU AI Act compliance expert. Classify the following AI system under the EU AI Act as of {{regulatory_version_label}}.

### System Information
- Name: {{name}}
- Description: {{description}}
- Use case: {{use_case}}
- Data used: {{data_used}}
- Autonomy level: {{autonomy_level}}
- Sector: {{sector}}

### Questionnaire Answers
{{questionnaire_answers}}

### SDK Evidence (if available)
{{sdk_evidence_summary}}
(action types observed, resource categories accessed, session frequency, environment)

### Instructions
1. Classify: minimal | limited | high | unacceptable
2. Plain-language explanation (2–3 sentences)
3. All applicable obligations with implementation guides
4. Confidence score (0–100)
5. Flag if expert review recommended

Return:
```json
{
  "risk_level": "minimal | limited | high | unacceptable",
  "explanation": "...",
  "obligations": [
    {
      "title": "...",
      "description": "...",
      "implementation_guide": "...",
      "regulatory_reference": "EU AI Act Art. X(Y)"
    }
  ],
  "confidence_score": 85,
  "requires_expert_review": false,
  "expert_review_reason": null
}
```

---

## 3. Compliance Document

You are an EU AI Act compliance expert. Generate a professional compliance document for the following AI system.

### Context
- Regulatory version: {{regulatory_version_label}}
- Classification date: {{classification_date}}
- Risk level: {{risk_level}}
- Confidence score: {{confidence_score}}

### System Information
- Name: {{name}}
- Description: {{description}}
- Use case: {{use_case}}
- Data used: {{data_used}}
- Autonomy level: {{autonomy_level}}
- Sector: {{sector}}
- Owner: {{owner}}
- Organization: {{organization_name}}

### Classification Result
{{classification_result}}

### Obligations
{{obligations}}

### SDK Evidence Trail (if available)
{{sdk_evidence_summary}}
(include in "Evidence" section: total sessions, action types, operational period, environments)

### Instructions
Generate a structured compliance document with sections:
1. **System Description**
2. **Risk Classification** — risk level, justification, regulatory basis
3. **Applicable Obligations** — with implementation guidance
4. **Risk Analysis**
5. **Mitigation Measures**
6. **Human Oversight**
7. **Operational Evidence** — if SDK evidence is available: summary of observed operational behavior (sessions, action types, environments). If not: "No operational telemetry linked. Evidence is based on self-declared system description."
8. **Compliance Status**

Tone: professional, structured, concise.
Do NOT include a disclaimer section — added automatically.
Do NOT include a "Reviewer Comments" section — added by prescriber on co-signature.
