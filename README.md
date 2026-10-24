# AI Job Helper

An n8n workflow that accepts an applicant's details, a job posting URL, and a PDF resume. It uses Google Gemini to analyze the job and resume, draft a tailored cover letter, save the results to Google Sheets, and email the applicant.

## Problem and objectives

Reviewing a resume against each job posting and preparing an application can be repetitive. This workflow automates an initial comparison and produces reusable application material. Its results are AI-generated and should be reviewed before use.

## Features

- Collects name, email, job link, PDF resume, and experience level through an n8n form.
- Fetches the submitted job page and extracts job text.
- Extracts text from the uploaded PDF.
- Uses Gemini agents and structured output to analyze the job and resume, including match score, skill gaps, and resume improvements.
- Generates a cover letter.
- Appends or updates a Google Sheets row and emails the applicant.

**Current limitations:** The experience-level field is present on the form but is not used downstream. Job text extraction expects the page to contain `Job Summary: ... Similar jobs`; otherwise it falls back to the first 8,000 characters of the fetched text. The resume extraction node is configured for PDF files only. The submitted job URL is fetched directly, so restrict acceptable destinations before exposing the form publicly.

## Technologies

- [n8n](https://n8n.io/) workflow automation and form trigger
- Google Gemini through n8n's LangChain AI nodes
- Google Sheets and Gmail integrations
- n8n's HTTP Request, Code, Extract from File, Merge, and Structured Output Parser nodes

## Workflow

1. The form trigger receives the applicant details and PDF.
2. The job link is fetched over HTTP while the PDF text is extracted.
3. A Code node cleans the job page text; the job and resume analyses are combined.
4. Gemini generates the resume analysis and cover letter.
5. The workflow writes results to Google Sheets and sends an email.

## Project structure

| Path | Purpose |
| --- | --- |
| `ai-job-helper.workflow.json` | Sanitized, inactive n8n workflow template |
| `README.md` | Setup and workflow documentation |
| `.env.example` | Comment-only example; this workflow defines no environment variables |
| `.gitignore` | Excludes the original local export and local secrets/runtime files |
| `Ai job helper (1).json` | Original local export; intentionally excluded from Git because it contains account-specific references |

This repository contains a workflow export, not a standalone frontend or backend application. It has no package manifest, custom API service, project-owned database schema, or migration scripts. n8n manages its own runtime and persistence.

## Prerequisites

- An n8n instance with the node types and versions referenced by the workflow.
- A Google Gemini API credential configured in n8n.
- Google Sheets OAuth and Gmail OAuth credentials configured in n8n, with access to the target spreadsheet and permission to send mail.
- A Google Sheet with headers matching the workflow's mapped columns: `Name`, `Email`, `Company`, `Job Title`, `Skils gap`, `Resume Improvement`, `Cover letter`, `Match Score`, and `Date`. The existing `Skils gap` spelling is preserved from the workflow export.
- Job postings accessible to the n8n instance and resumes in PDF format.

## Installation and setup

1. Start or create an n8n instance using the [official installation documentation](https://docs.n8n.io/hosting/). For a local Node.js/npm installation, `npx n8n` can start n8n; this starts the automation platform, not an application bundled in this repository.
2. In n8n, import `ai-job-helper.workflow.json` using the workflow import-from-file option.
3. Configure the Google Gemini credentials on the three Gemini Chat Model nodes. Choose or verify the model supported by your n8n installation.
4. Configure the Google Sheets node with your spreadsheet and worksheet, then verify the column mappings against the headers above.
5. Configure the Gmail credential and verify the generated message and recipient mappings.
6. Save the workflow, test it with a job page you are authorized to access and a non-sensitive PDF, and activate it only when the integrations and form are ready.

The published template intentionally has no credential bindings, spreadsheet ID, cached spreadsheet URL, or active form webhook ID. Those account-specific settings must be selected in your n8n instance after import.

## Environment variables

This workflow does not read project-defined environment variables. Configure provider credentials in n8n's credential manager instead. `.env.example` contains comments only; do not add real API keys, OAuth secrets, or passwords to the workflow export or repository.

## Running and production

There are no application-specific install, test, or build scripts in this repository. Run the n8n instance using your chosen installation/deployment method, import the workflow, configure credentials and the sheet, and activate the workflow in n8n. For production, follow n8n's hosting guidance for persistent storage, backups, encryption, access control, and secure credential management. No separate production build is required.

## Form and data flow

The n8n Form Trigger exposes the application form when the workflow is configured and active. It is the only user-facing entry point defined here; this repository does not define a separate REST API.

The workflow writes applicant and generated-result fields to the configured spreadsheet, then sends an email to the submitted address. Resume and job-posting content is sent to the configured Gemini integration for processing. Review your providers' data handling and retention terms before using personal applicant data.

## Database and migrations

The workflow has no application-owned database or migrations. n8n's own database and persistence are configured on the n8n instance and are outside this repository.

## Production build

There is no production build command: the deliverable is an n8n workflow JSON export. Deploy n8n, import the template, set up provider credentials and spreadsheet mappings, test, and activate it.

## Troubleshooting

- **A node type is unavailable:** use an n8n installation that includes compatible Form, Gemini/LangChain, Google Sheets, Gmail, and file-extraction nodes.
- **The workflow cannot access Google services:** configure or reauthorize the relevant Gemini, Sheets, and Gmail credentials in n8n; credentials are not included in this repository.
- **No sheet rows are written:** select the spreadsheet and worksheet after import and check that its headers match the configured columns.
- **Job analysis is incomplete:** the current text extraction relies on a specific page phrase and truncates its fallback input. Job pages with different layouts may need a more robust extraction step.
- **Resume processing fails:** confirm the upload is a readable PDF and that the n8n instance supports the configured Extract from File operation.
- **Email delivery fails:** verify Gmail authorization, recipient mapping, and the sending account's permissions.

## Future improvements

- Use the experience-level form value in analysis.
- Replace the page-specific job-text heuristic with more robust extraction and explicit failure handling.
- Restrict and validate job URL destinations before enabling a public form.
- Add workflow-level error handling and tests using representative job pages and PDFs.

## Screenshots

No screenshots are included in the project.
