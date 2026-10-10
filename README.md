# AI-Powered Lead Qualification & Follow-Up Automation

An AI-powered lead qualification and follow-up automation project built with **n8n, Retrieval-Augmented Generation (RAG), Google Gemini, Ollama, Supabase, and Google Sheets**.

The system retrieves company-specific knowledge, extracts structured lead information, evaluates lead quality using configurable business rules, assigns a qualification category, and generates personalized follow-up messages.

The project contains two complementary workflows: a **Knowledge Base Ingestion Workflow** that prepares and stores company knowledge as vectors, and a **Lead Qualification Workflow** that retrieves this knowledge to evaluate incoming leads.

## Features

- **RAG-based knowledge retrieval:** Retrieves relevant company information from a vector database.
- **Knowledge base ingestion:** Loads company knowledge files, extracts and chunks text, generates embeddings, and stores vectors for retrieval.
- **AI-powered lead extraction:** Extracts structured lead details from unstructured input.
- **Automated lead scoring:** Scores leads based on budget, requirement clarity, timeline, business fit, service fit, and decision-maker involvement.
- **Four-level classification:** Categorizes leads as HOT, WARM, COLD, or UNQUALIFIED.
- **Automated workflow routing:** Routes leads according to their qualification category.
- **Google Sheets integration:** Stores lead details, scores, classifications, and follow-up messages.
- **Personalized follow-ups:** Generates professional follow-up message drafts based on available lead information.
- **Missing-information handling:** Keeps unavailable information unknown instead of inventing values.
- **Modular architecture:** Separates knowledge preparation from lead processing.

## Technology Stack

- **n8n** — Workflow automation and orchestration
- **Google Gemini** — AI-powered lead extraction and follow-up message generation
- **Ollama** — Local embedding generation
- **nomic-embed-text** — Embedding model
- **Supabase with pgvector** — Vector storage and similarity search
- **Google Sheets** — Lead records and follow-up message storage
- **JavaScript** — Lead scoring and classification logic

## Workflow Architecture

This project contains two workflows that work together.

### Workflow 1: Knowledge Base Ingestion

**File:** `workflow/knowledge-base-ingestion-workflow.json`

This workflow prepares company knowledge so the lead qualification agent can retrieve relevant information.

Processing steps:

1. Read the company knowledge-base files.
2. Extract text from the source documents or file data.
3. Split the content into manageable text chunks.
4. Generate embeddings using Ollama and `nomic-embed-text`.
5. Store the embedded documents in the configured Supabase pgvector database.

The vector database contains searchable representations of the company knowledge. This workflow should be run before the lead qualification workflow is used for the first time and whenever the knowledge base needs to be updated.

**Important:** Repeated ingestion should be handled carefully to avoid duplicate chunks. Configure the appropriate replacement, cleanup, or upsert strategy for your vector store.

### Workflow 2: Lead Qualification and Follow-Up

**File:** `workflow/lead-qualification-workflow.json`

This workflow uses the prepared knowledge base to evaluate incoming leads.

Processing steps:

1. Receive lead information.
2. Retrieve relevant company knowledge through RAG.
3. Extract structured lead attributes using an AI agent.
4. Validate and structure the extracted data.
5. Calculate the lead score using JavaScript.
6. Classify the lead as HOT, WARM, COLD, or UNQUALIFIED.
7. Route the lead through the corresponding branch.
8. Merge the classification branches.
9. Store lead information, score, and classification in Google Sheets.
10. Generate a personalized follow-up message and save it to the sheet.

### Execution Order

1. Configure the required services and credentials.
2. Run the Knowledge Base Ingestion Workflow to populate the vector database.
3. Verify that the knowledge has been stored successfully.
4. Execute the Lead Qualification Workflow with a sample lead.
5. Review the qualification result and generated follow-up message in Google Sheets.

The lead qualification workflow depends on the vector database containing the required company knowledge.

## Lead Scoring

The scoring model considers the following criteria:

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

Classification thresholds and qualification rules can be adjusted in the JavaScript scoring node.

The scoring model is a configurable business-rule system, not a guarantee that a lead will convert into a customer.

## Knowledge Base

The example knowledge base contains the following files:

- `companyinformation.txt` — Company information
- `faq.txt` — Frequently asked questions
- `ideal-customer-profile.txt` — Target customer characteristics
- `pricing.txt` — Budget and pricing guidance
- `qualification-rules.txt` — Lead qualification rules
- `services.txt` — Available services

These documents provide company-specific context for retrieval and qualification decisions. Replace the demonstration content with your own appropriate business information when adapting the project.

## Repository Structure

```text
ai-lead-qualification-automation/
├── README.md
├── workflow/
│   ├── knowledge-base-ingestion-workflow.json
│   └── lead-qualification-workflow.json
├── knowledge-base/
│   ├── companyinformation.txt
│   ├── faq.txt
│   ├── ideal-customer-profile.txt
│   ├── pricing.txt
│   ├── qualification-rules.txt
│   └── services.txt
├── sample-data/
│   └── test-leads.csv
└── Project-Screenshots/
    └── workflow-overview.png
```

The structure above is illustrative. File and folder names should match the files actually committed to the repository.

## Testing

The workflows have been tested with scenarios including:

- Strong and weak leads
- All four classification categories
- Missing budget information
- Missing timeline information
- Vague requirements
- Unsupported services
- End-to-end execution through Google Sheets
- Follow-up message generation and storage

Sample lead data is provided for demonstration. Actual outcomes depend on the configured workflow, input data, knowledge base, and scoring rules.

## Setup Overview

### Prerequisites

- n8n, running locally or on a hosted instance
- Google Gemini API access, if required by the exported workflow
- Ollama with the `nomic-embed-text` model
- A Supabase project with pgvector enabled
- A Google account and Google Sheets access
- The project knowledge-base files

### Installation and Configuration

1. Clone or download this repository.
2. Install and configure n8n.
3. Import both workflow JSON files into n8n.
4. Configure your own Google Gemini and Google Sheets credentials.
5. Install and run Ollama, then download the embedding model:

   ```bash
   ollama pull nomic-embed-text
   ```

6. Configure Supabase and enable pgvector.
7. Set up the required vector table, embedding dimensions, and vector-store configuration to match the workflow.
8. Make the knowledge-base files accessible to n8n. If using Docker, configure an appropriate volume mount and update file paths as needed.
9. Connect the document loader, text splitter, embedding model, and vector store in the ingestion workflow.
10. Run the ingestion workflow and verify that the documents have been stored.
11. Configure the retriever or vector-store tool in the lead qualification workflow to use the same vector database and collection.
12. Execute the main workflow with a sample lead and inspect the results in Google Sheets.

Exact node settings, table schemas, credential types, and file paths may vary by n8n version and environment. Follow the imported workflow's actual configuration when completing the setup.

## Workflow Overview

![AI Lead Qualification n8n Workflow](Project-Screenshots/workflow-overview.png)

## Security

- Never commit API keys, OAuth client secrets, access tokens, passwords, or other credentials.
- Use your own credentials when importing the workflows.
- Review workflow exports for sensitive values before publishing them.
- Do not publish private lead information or confidential business documents.
- Use synthetic lead data for public demonstrations.
- Restrict access to your n8n instance, database, and Google Sheets as appropriate.

## Limitations

- Classification depends on the quality of extracted information and configured scoring rules.
- RAG quality depends on the content, chunking, embeddings, and retrieval configuration of the knowledge base.
- Follow-up messages are generated drafts; generation does not itself send messages to leads.
- Actual outreach requires valid contact information and a separately configured communication integration.
- Human review is recommended before acting on important qualification decisions.
- The project requires correct credentials and configuration for all connected services.

## Future Improvements

- Email or WhatsApp integration with appropriate consent
- Lead deduplication and record updates
- A dashboard for lead analytics
- More comprehensive monitoring and error notifications
- Human review and feedback to improve qualification accuracy
- Automated knowledge-base refresh and duplicate-safe ingestion

## Author

Built as a practical AI automation project to explore RAG, vector search, structured AI outputs, business-rule scoring, and workflow orchestration.
