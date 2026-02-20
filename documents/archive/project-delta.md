# Project Delta (Archived)

**Status:** Completed & Archived
**Owner:** Engineering Team
**Period:** 2022-04-01 — 2022-10-31

## Overview

Project Delta built the first version of the internal analytics pipeline, processing daily sales and support data. It was decommissioned in late 2022 when Project Gamma replaced it with a more scalable solution.

## What Was Built

- Python-based batch ETL scripts (ran nightly via cron)
- PostgreSQL data warehouse (single node)
- Basic Metabase dashboards for Sales and Support teams

## Reason for Archiving

- Single-node database could not handle growing data volume
- No monitoring or alerting; failures were discovered manually
- Codebase had no test coverage, making changes risky
- Replaced by Project Gamma (delivered Jan 2024)

## Key Files (decommissioned)

All code was archived to the `archive/project-delta-code` S3 bucket on 2022-11-30.

## Contacts

- Original project lead: Pat Wong (left company 2023-03)
