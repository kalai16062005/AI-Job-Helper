# AI Job Helper

## Project Overview

AI Job Helper is an n8n workflow template that automates an initial review of a resume against a job posting. It helps applicants prepare tailored application materials and track their results.

The workflow analyzes the job description and resume, identifies skill gaps, suggests resume improvements, calculates a match score, generates a cover letter, records results in Google Sheets, and emails the applicant.

## Technology Stack

- **n8n** for workflow automation and the applicant form.
- **Google Gemini** through n8n's Gemini Chat Model and AI Agent nodes.
- **Google Sheets** to append or update application results.
- **Gmail** to deliver the applicant's report.
- **Other n8n nodes:** Form Trigger, HTTP Request, Code, Extract from File, Merge, and Structured Output Parser.

## Application Workflow

1. The Form Trigger collects the applicant's name and email, a job posting URL, a resume file, and experience level.
2. In parallel, HTTP Request fetches the job page and Extract from File extracts resume text. The file extraction node is configured for PDF.
3. The Code node cleans the fetched page text; the JD Analyzer uses Gemini and structured output to identify job details and requirements.
4. Merge combines the job analysis with the extracted resume text. Resume Analysis uses Gemini to produce skill gaps, resume improvements, and a match score.
5. The Cover Letter agent drafts a tailored letter using the applicant details, job analysis, and resume analysis.
6. Google Sheets appends or updates a row with applicant and generated results. Gmail then sends the applicant a job-match report with the analysis and cover letter.

The form collects experience level, but the current workflow does not use it in its analysis. Although the form allows PDF and DOCX file selection, the extraction node is configured for PDF; use a PDF resume.

## How to Run This Project

**Prerequisites:** An n8n instance, a Google Gemini API credential, Google Sheets and Gmail OAuth credentials, access to a target spreadsheet and worksheet, and a PDF resume for testing.

1. Install or start n8n by following the [official n8n hosting documentation](https://docs.n8n.io/hosting/). For a local Node.js/npm setup, run `npx n8n` to start the n8n application.
2. Import `ai-job-helper.workflow.json` into n8n using its workflow import option. The repository contains a sanitized workflow template, not a standalone web application.
3. Configure credentials on the Google Gemini Chat Model nodes, the Google Sheets node, and the Gmail node in your n8n instance.
4. In the Google Sheets node, select your spreadsheet and worksheet, then confirm the sheet columns match the node's mappings.
5. Save the workflow and test it with an accessible job posting URL and a PDF resume. Activate the workflow in n8n when ready to use its form.

The template does not include credentials or spreadsheet references. Each user must configure their own credentials, spreadsheet, and worksheet after importing it.
