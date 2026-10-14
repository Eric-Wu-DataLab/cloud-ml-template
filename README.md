# Project name

Two sentences. What problem this solves and the headline result.

## Architecture

Data flow diagram, plus one line per component explaining why it is there.

## Data

Source, size, usage rights, and how to get it. Data is not stored in this repo.

## Project layout

| Path | Contents |
| --- | --- |
| src/ | Pipeline and model code |
| tests/ | Unit tests and eval checks |
| infra/ | Cloud resource setup and teardown scripts |
| COSTS.md | Cost log and credit end dates |

## Prerequisites

Accounts, tools, and versions needed before the first command.

## Configuration

Copy .env.example to .env and fill in each value. Secrets never go in git.

## Cloud conventions

### Budgets and alerts

Each cloud account has one monthly budget of $50, set at the subscription level.
Budget emails go to the account owner. A budget only sends email. It does not stop any resource.

| Alert | Threshold | Type | What I do when it fires |
| --- | --- | --- | --- |
| $10 | 20% | Actual | Open cost analysis, find the top resource, confirm it was expected. |
| $25 | 50% | Actual | Stop or scale down every resource not in active use and find what caused the spend. |
| $50 | 100% | Actual | Stop all compute today, delete any resource group I no longer need, and create nothing new until I know the cause. |
| Forgotten GPU | 100% | Forecasted | Find any running GPU compute and stop it the same day. |

### Tags

Every resource group and every resource gets three tags.

| Tag | Example | Meaning |
| --- | --- | --- |
| project | rag-assistant | The project the resource belongs to. |
| owner | eric | The person to ask before deleting it. |
| expires | 2026-11-08 | The date it should be deleted. Azure does not act on this tag. The Friday cost check does. |

Tags on a resource group are not copied to the resources inside it, so each resource is tagged too.

### Resource groups and naming

Each project has its own resource group, so deleting the group removes everything the project created.
All resources in a project use the same region.
Names follow the pattern `<type>-<project>`.

| Resource | Example name (Project A) |
| --- | --- |
| Resource group | rg-rag-assistant |
| Storage account | stragassistant |
| AI Search service | srch-rag-assistant |
| Key Vault | kv-rag-assistant |

Storage account names allow only lowercase letters and numbers, so they drop the hyphens.
Some names must be unique across all of Azure. If a name is taken, add a short suffix such as initials.

### Cost-safe defaults

- Compute clusters have a minimum of 0 nodes, so they shut down after each job.
- Compute instances have idle shutdown turned on.
- Models are served with batch endpoints. Real-time endpoints bill every hour they exist.
- Azure AI Search runs on the Free tier. The Basic tier is used only for a short test and deleted after the demo.
- BigQuery sessions have a maximum-bytes-billed cap, and queries name their columns instead of using SELECT *.

### Before creating anything billable

- Budget alerts are set up and the confirmation email has arrived.
- The project's resource group exists and has its three tags.
- The service's price is checked in the Azure pricing calculator and noted in COSTS.md.
- The free credit end date is written in COSTS.md.
- Keys and connection strings are stored in .env or Key Vault and never committed to git.

### Teardown

1. Record the demo.
2. Write the actual cost in COSTS.md.
3. Delete the project's resource group.
4. Check for soft-deleted resources. Some, like Key Vault, keep their name reserved for a while after deletion.
5. Check the cost page two days later to confirm the charges stopped.
6. Tag the final release in git.

## How to run

1. Set up the environment
2. Create cloud resources
3. Run the pipeline
4. Run the tests

## Results

Evaluation metrics, what they mean, and what a successful run looks like.

## Cost

Actual cost to build and run this project. Details are in COSTS.md.

## Teardown

Steps to delete every cloud resource so nothing keeps billing.

## What I would do next

## New project checklist

- [ ] Rename the project and write the two-sentence summary
- [ ] Turn on secret scanning push protection
