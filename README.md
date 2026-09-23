# B2B Lead Enrichment & Qualification

Automated B2B lead enrichment and qualification workflow built with **n8n, Google Sheets and the APICVR API**.

## Overview

The workflow takes raw B2B leads containing Danish CVR numbers and automatically enriches them with company information from the APICVR API.

The enriched company data is then evaluated using predefined qualification rules, including company status, employee count, bankruptcy/protection status and industry relevance.

The purpose is to turn a basic lead list into a structured and qualified dataset that can be used for sales prospecting, segmentation and further marketing automation.

## Workflow

The pipeline follows a simple processing flow:

**Google Sheets → Only Unprocessed → APICVR → Enrich & Qualify → Industry Match → Enriched Leads → Mark Processed**

### What it does

* Reads B2B leads from Google Sheets
* Identifies leads that have not yet been processed
* Uses the CVR number to query the APICVR API
* Retrieves company information
* Enriches each lead with structured company data
* Determines a lead priority based on predefined rules
* Checks the company's industry code against target industries
* Stores the enriched lead in a separate Google Sheets dataset
* Marks the original lead as processed
* Prevents already processed leads from being processed again

## Lead Enrichment

The workflow enriches the original lead with information including:

* Company name
* CVR number
* Company status
* Company type
* Company status date
* Bankruptcy status
* Protection status
* Industry code
* Industry description
* Number of employees
* Establishment date
* Address
* Postal code
* City
* Data source
* Processing timestamp

The enrichment is based on company data returned by the **APICVR API**.

## Lead Qualification

The workflow applies predefined qualification rules to the enriched company data.

### Priority

Priority is determined using company status and employee count:

* **Inactive – Do Not Contact** — company has an end date
* **Do Not Contact** — company is bankrupt or protected
* **High** — 50+ employees
* **Medium** — 10–49 employees
* **Low** — fewer than 10 employees
* **Unknown** — employee information is unavailable

This creates a consistent qualification layer that can be used for subsequent sales or marketing workflows.

### Industry Matching

The workflow also compares the company's industry code against a predefined list of target industry codes stored in Google Sheets.

Each lead receives an `industry_match` value indicating whether its industry belongs to the defined target segment.

This makes it possible to combine company size and company status with industry relevance when evaluating leads.

## Data Flow

The workflow uses two Google Sheets datasets:

### Leads

The input sheet contains the original leads, including:

* Lead name
* Lead email
* CVR number
* Processing status

Only leads where `processed = FALSE` are sent through the enrichment workflow.

### Enriched Leads

The enriched dataset contains the original lead information together with the company data retrieved from APICVR and the qualification results.

The enriched lead is upserted using the CVR number as the matching field.

## Processing Control

The workflow includes a simple processing-state mechanism to make the pipeline repeatable.

New leads are marked as unprocessed.

After successful enrichment and storage, the original lead is updated to:

`processed = TRUE`

This allows the workflow to be run repeatedly without unnecessarily processing the same leads again.

## Tools

* **n8n** — workflow automation and orchestration
* **Google Sheets** — lead database, industry configuration and enriched output
* **APICVR API** — Danish company data enrichment
* **JavaScript** — data transformation and qualification logic
* **HTTP Request** — API integration

## Workflow File

The sanitized n8n workflow is available here:

`workflow/b2b-lead-enrichment-qualification-apicvr.json`

The workflow is provided for demonstration and portfolio purposes.

## Security

This repository contains a sanitized version of the workflow.

Credentials, authentication details and private configuration should be removed or replaced before publishing the workflow publicly.

Before using the workflow, configure your own:

* Google Sheets credentials
* APICVR API access
* Google Sheets documents and worksheets

## Use Cases

The workflow can serve as a foundation for:

* B2B lead enrichment
* Sales prospecting
* Lead qualification
* Industry segmentation
* Marketing automation
* CRM preparation
* Account-based marketing
* Automated prospect prioritization

## Future Improvements

Possible extensions include:

* CRM integration
* Automated lead scoring
* Additional company data sources
* Website enrichment
* Contact enrichment
* Automated email outreach
* Slack or email notifications for high-priority leads
* Scheduled processing instead of manual execution
* Additional qualification criteria
* Integration with downstream sales and marketing automation
