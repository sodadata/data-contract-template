# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a collection of **Soda data contract YAML templates** — declarative data quality definitions used with [Soda](https://www.soda.io/). There is no application code, build system, or test suite; the repo is purely YAML contract definitions.

## Contract Structure

Each `.yml` file defines a data contract for a specific dataset. The standard structure is:

- **`dataset`**: Target table path (`datasource/database/schema/table`)
- **`variables`**: Optional parameterized values (e.g., `FRESHNESS_HOURS`) referenced via `${var.NAME}`
- **`checks`**: Dataset-level checks (schema validation, row_count, freshness, failed_rows, reconciliation)
- **`columns`**: Per-column definitions with `name`, `data_type`, and column-level `checks`
- **`reconciliation`**: Cross-dataset validation (see `finance_bcbs239_data_contract.yml`)

## Contract Variants

Two formats coexist:

1. **Main format** (most files): Uses Soda-native check types (`missing`, `duplicate`, `invalid`, `failed_rows`, `freshness`, `aggregate`, `schema`). Thresholds use `must_be`, `must_be_greater_than`, `must_be_less_than`, etc. Checks can have `qualifier`, `name`, and `attributes` metadata.

2. **v4 format** (`v4_contract_test.yml`): Same Soda-native syntax but adds `qualifier` identifiers and explicit `level: fail` thresholds on every check.

3. **AI-generated format** (`v4_from_ai.yml`): Uses a simplified `type`-based syntax (`no_missing_values`, `no_duplicate_values`, `custom_sql`, `row_count`) that differs from native Soda syntax.

## Domain Templates

- `finance_transactions_data_contract.yml` — Financial transactions with UUID validation, currency (ISO-4217), cross-field checks (amount sign vs transaction type)
- `finance_bcbs239_data_contract.yml` — BCBS 239 regulatory compliance with reference data validation, reconciliation, and `attributes.bcbs239` tags
- `retail_oders_data_contract.yml` — Retail orders (note: filename has typo "oders")
- `retail_inventory_levels_data_contract.yml` — Inventory with arithmetic consistency checks (available = on_hand - reserved)
- `tech_subscriptions_data_contract.yml` — SaaS subscriptions with lifecycle consistency (status vs date logic)
- `general_data_contract.yml` — Minimal starter template

## Key Soda Conventions

- `${soda.NOW}` — built-in variable for current timestamp
- `${var.NAME}` — references to user-defined variables
- `valid_format.regex` — regex-based format validation
- `valid_values` — enumerated allowed values
- `valid_reference_data` — referential integrity against another dataset
- `failed_rows.expression` — SQL expression that identifies bad rows (rows matching the expression fail)
