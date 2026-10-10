# AI-Powered Lead Qualification & Follow-Up Automation

An AI-powered lead qualification workflow built with n8n, Retrieval-Augmented Generation (RAG), and Google Sheets. The system extracts lead information, evaluates lead quality using configurable business rules, assigns a qualification category, and generates personalized follow-up messages.

## Features

- **RAG-based knowledge retrieval:** Retrieves relevant information from a company knowledge base.
- **AI-powered lead extraction:** Extracts structured lead details from unstructured input.
- **Automated lead scoring:** Scores leads based on budget, requirement clarity, timeline, business fit, service fit, and decision-maker involvement.
- **Four-level classification:** Categorizes leads as HOT, WARM, COLD, or UNQUALIFIED.
- **Automated workflow routing:** Routes leads according to their classification.
- **Google Sheets integration:** Stores lead information, scores, classifications, and follow-up messages.
- **Personalized follow-ups:** Generates professional messages based on available lead information.
- **Missing-information handling:** Tests whether unavailable budget and timeline information remain unknown rather than being invented.

## Technology Stack

- n8n — Workflow automation
- Google Gemini — AI-powered lead extraction and message generation
- Ollama — Local embedding generation
- `nomic-embed-text` — Embedding model
- Supabase with pgvector — Vector storage and similarity search
- Google Sheets — Lead records and follow-up storage
- JavaScript — Lead scoring and classification logic

## Workflow Architecture

1. Receive lead information.
2. Retrieve relevant company knowledge through RAG.
3. Extract structured lead attributes using an AI agent.
4. Validate and structure the extracted data.
5. Calculate the lead score using JavaScript.
6. Classify the lead as HOT, WARM, COLD, or UNQUALIFIED.
7. Route the lead through the corresponding branch.
8. Merge the branches.
9. Store lead information in Google Sheets.
10. Generate a personalized follow-up message and save it to the sheet.

## Lead Scoring

The scoring model considers:

| Criterion | Maximum Points |
|---|---:|
| Budget | 30 |
| Requirement clarity | 25 |
| Timeline | 20 |
| Business fit | 10 |
| Service fit | 10 |
| Decision-maker involvement | 5 |
| **Total** | **100** |

The classification logic also applies qualification rules. For example, a lead must meet the required budget category and score threshold to qualify as HOT. Low-budget leads and leads with a confirmed business or service mismatch are rejected by the current rules.

Classification thresholds and qualification rules are configurable in the JavaScript node.

## Testing

The workflow has been tested with:

- Strong and weak leads
- All four classification categories
- Missing budget information
- Missing timeline information
- Vague requirements
- Unsupported services
- End-to-end execution through Google Sheets
- Follow-up message generation and storage

## Setup Overview

1. Install and configure n8n.
2. Configure the required Gemini and Google Sheets credentials.
3. Run Ollama and download the `nomic-embed-text` embedding model.
4. Set up Supabase and enable pgvector.
5. Add the company knowledge-base files.
6. Configure embeddings, document loading, chunking, and vector storage.
7. Import or recreate the n8n workflow.
8. Configure credentials and environment-specific settings.
9. Test the workflow with sample leads.

Exact node configuration and credentials may vary by environment.

## Workflow Overview

![AI Lead Qualification n8n Workflow](Project-Screenshots/workflow-overview.png)

## Security

- Never commit API keys, credentials, access tokens, or private lead information.
- Use environment-specific credentials and appropriate access controls.
- Use synthetic lead data for public demonstrations.

## Limitations

- Classification depends on the quality of extracted information and configured scoring rules.
- Follow-up messages are generated drafts; generation does not itself send messages to leads.
- Actual outreach requires valid contact information and a separately configured communication integration.
- Human review is recommended before acting on important qualification decisions.

## Future Improvements

- Email or WhatsApp integration with appropriate consent.
- Lead deduplication and record updates.
- A dashboard for lead analytics.
- More comprehensive monitoring and error notifications.
- Human review and feedback to improve qualification accuracy.

## Author

Built as a practical AI automation project to explore RAG, vector search, structured AI outputs, business-rule scoring, and workflow orchestration.
