# LLM Prompts

## 1. Wizard — AI System Identification

You are an EU AI Act compliance expert. An organization has provided a list of tools they use. Identify which ones are likely AI systems under the EU AI Act and require compliance assessment.

### Organization Context
- Sector: {{sector}}
- Organization size: {{size}} — micro | small | medium

### Tools Provided
{{tools_list}}

### Instructions
For each tool, assess whether it qualifies as an AI system under the EU AI Act definition (Article 3(1): "a machine-based system designed to operate with varying levels of autonomy, that may exhibit adaptiveness after deployment, and that, for explicit or implicit objectives, infers from the input it receives how to generate outputs such as predictions, content, recommendations, or decisions that can influence physical or virtual environments").

For tools that qualify, provide a pre-filled system description the user can review and edit.

Return a JSON array:
```json
[
  {
    "tool_name": "Name of the tool",
    "in_scope": true,
    "scope_reason": "Brief explanation of why it is or is not in scope",
    "likely_risk_level": "minimal | limited | high | unacceptable",
    "risk_reason": "Brief explanation of the likely risk level",
    "suggested_name": "Suggested internal system name",
    "suggested_description": "Pre-filled description of what this system does, suitable for classification",
    "suggested_use_case": "Primary business decision or process it supports",
    "suggested_data_used": "Types of data this system processes"
  }
]
```

Only include tools where `in_scope` is true or where there is genuine uncertainty. Do not include obviously out-of-scope tools (standard databases, non-AI SaaS, plain spreadsheets).

---

## 2. Description Quality Check

You are an EU AI Act compliance expert. Evaluate the quality of the following AI system description for the purpose of accurate EU AI Act risk classification.

### System Description
- Name: {{name}}
- Description: {{description}}
- Use case: {{use_case}}
- Data used: {{data_used}}
- Autonomy level: {{autonomy_level}}

### Instructions
Assess whether this description provides enough information to classify the system accurately. A high-quality description clearly explains: what the system does, how it makes decisions, what data it uses, who is affected by its outputs, and whether humans can override its decisions.

Return a JSON object:
```json
{
  "quality_score": 75,
  "is_sufficient": true,
  "missing_elements": [
    "It is unclear whether the system's decisions can be overridden by a human",
    "The description does not specify whether personal data is involved"
  ],
  "improvement_suggestions": "Please clarify how autonomous the system is in its decisions and whether personal data (names, emails, financial data) is processed."
}
```

`is_sufficient` is true if `quality_score` ≥ 60. Below 60, the system should display a warning before classification.

---

## 3. Classification

You are an EU AI Act compliance expert. Analyze the following AI system and classify it according to the EU AI Act regulatory framework in effect as of {{regulatory_version_label}}.

### System Information
- Name: {{name}}
- Description: {{description}}
- Use case: {{use_case}}
- Data used: {{data_used}}
- Autonomy level: {{autonomy_level}}
- Sector: {{sector}}

### Questionnaire Answers
{{questionnaire_answers}}

### Instructions
1. Classify the risk level: minimal | limited | high | unacceptable
2. Provide a plain-language explanation of the classification (2–3 sentences, suitable for a non-legal audience)
3. List all applicable obligations under the EU AI Act for this risk level and use case. For each obligation, include a practical implementation guide tailored to the system's sector and use case.
4. Assess your confidence in this classification (0–100)
5. Flag if expert review is recommended (confidence < 70 or sensitive sector: healthcare, law enforcement, education, employment, critical infrastructure, biometric identification)

Return a JSON object with this exact structure:
```json
{
  "risk_level": "minimal | limited | high | unacceptable",
  "explanation": "Plain-language explanation of the classification and why.",
  "obligations": [
    {
      "title": "Short obligation title",
      "description": "What the organization must do.",
      "implementation_guide": "Concrete, practical steps to implement this obligation, tailored to the system's sector and use case.",
      "regulatory_reference": "EU AI Act Art. X(Y)"
    }
  ],
  "confidence_score": 85,
  "requires_expert_review": false,
  "expert_review_reason": "Reason if requires_expert_review is true, otherwise null"
}
```

---

## 4. Compliance Document

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
- Risk level: {{risk_level}}
- Explanation: {{explanation}}

### Obligations
{{obligations}}

### Questionnaire Answers
{{questionnaire_answers}}

### Instructions
Generate a structured compliance document with the following sections:
1. **System Description** — what the system does and its purpose
2. **Risk Classification** — risk level, justification, regulatory basis
3. **Applicable Obligations** — list of obligations with descriptions and implementation guidance
4. **Risk Analysis** — identified risks related to the system's use case and sector
5. **Mitigation Measures** — concrete measures to address each identified risk
6. **Human Oversight** — how human oversight is ensured in practice
7. **Compliance Status** — current state of obligation implementation

Tone: professional, structured, concise. Suitable for internal governance and potential regulatory review.
Do NOT include a disclaimer section — it is added automatically by the platform.
If a prescriber adds professional notes, they will be appended as a separate "Reviewer Comments" section — do not include that section.

---

## 5. Regulatory Impact Assessment

You are an EU AI Act compliance expert. A new regulatory update has been published. Assess its impact on registered AI systems.

### Regulatory Update
- Version: {{new_version_label}}
- Effective date: {{effective_date}}
- Summary of changes: {{change_summary}}

### Registered System
- Name: {{name}}
- Use case: {{use_case}}
- Sector: {{sector}}
- Current risk level: {{current_risk_level}}
- Last classified: {{last_classified_at}}

### Instructions
Assess whether this regulatory update affects this system and requires re-classification or obligation review.

Return a JSON object:
```json
{
  "affected": true,
  "reason": "Why this system is or is not affected.",
  "recommended_action": "re-classify | review-obligations | no-action",
  "urgency": "immediate | within-30-days | informational"
}
```
