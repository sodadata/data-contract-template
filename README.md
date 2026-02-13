# Data Contract Templates

A collection of ready-to-use data contract templates across industries and use cases. Clone a template, adapt it to your dataset, and run it against your data.

Browse all templates with a searchable UI at [executabledatacontrats.com](https://soda.io/templates?utm_source=github&utm_medium=organic&utm_campaign=data_contracts&utm_content=templates).

<p align="center">
  <img width="455" height="260" alt="CleanShot 2026-02-12 at 18 13 00@2x" src="https://github.com/user-attachments/assets/1b2ee06f-7bbf-4d5d-ab0f-ef2834799cc7" />
</p>

## What is a data contract?

A data contract is a YAML file that defines what a dataset **should** look like — its schema, column types, allowed values, freshness, row counts, and any other quality rule. When executed against real data, each rule is verified and produces a pass or fail result.

A minimal contract looks like this:

```yaml
dataset: my_datasource/my_database/my_schema/orders

checks:
  - schema:
      allow_extra_columns: false
  - row_count:
      threshold:
        must_be_greater_than: 0

columns:
  - name: order_id
    data_type: varchar
    checks:
      - missing:
      - duplicate:
  - name: total
    data_type: decimal
    checks:
      - invalid:
          valid_min: 0
```

## Available templates

| Template | Description |
|---|---|
| [general_data_contract.yml](general_data_contract.yml) | Starter template with common check types: schema, row count, missing/duplicate, valid values, and aggregates |
| [finance_transactions_data_contract.yml](finance_transactions_data_contract.yml) | Financial transactions — UUID validation, ISO-4217 currency, freshness, and cross-field checks (amount sign must match transaction type) |
| [finance_bcbs239_data_contract.yml](finance_bcbs239_data_contract.yml) | BCBS 239 regulatory compliance — referential integrity, LEI format validation, reconciliation between exposure and accounting datasets |
| [retail_inventory_levels_data_contract.yml](retail_inventory_levels_data_contract.yml) | Inventory levels — arithmetic consistency (`available = on_hand - reserved`), non-negative stock, freshness with configurable threshold |
| [retail_oders_data_contract.yml](retail_oders_data_contract.yml) | Retail orders — quantity validation, payment method enumeration, country code format, freshness |
| [tech_subscriptions_data_contract.yml](tech_subscriptions_data_contract.yml) | SaaS subscriptions — lifecycle consistency (cancelled subs must have end dates, active subs can't have past end dates), billing status enumeration |

## Running a contract

### 1. Install Soda

```bash
# Install the package for your data source
pip install soda-postgres    # or soda-snowflake, soda-bigquery, soda-databricks, soda-duckdb, etc.
```

### 2. Configure your data source

Create a `ds_config.yml` with your connection details:

```yaml
type: postgres
name: my_datasource
connection:
  host: localhost
  port: 5432
  user: my_user
  password: ${env.POSTGRES_PASSWORD}
  database: my_database
```

The `name` field must match the first segment of the `dataset` path in your contract. Environment variables are supported via `${env.VARIABLE_NAME}`.

### 3. Adapt and verify

Copy a template, update the `dataset` path and column definitions to match your table, then run:

```bash
# Validate contract syntax (no database connection needed)
soda contract test --contract my_contract.yml

# Run the contract against live data
soda contract verify -ds ds_config.yml -c my_contract.yml
```

See the [Soda CLI reference](https://docs.soda.io/soda-v4/reference/cli-reference) and [contract language reference](https://docs.soda.io/soda-v4/reference/contract-language-reference) for the full set of check types and options.

## Contributing a template

1. Fork this repo and create a branch.
2. Add your contract as `<industry>_<use_case>_data_contract.yml` (e.g., `healthcare_patient_records_data_contract.yml`).
3. Use `datasource/database/schema/<table_name>` as the dataset path so it stays generic.
4. Include a variety of check types that are relevant to the use case — the goal is to teach people the syntax through realistic examples.
5. Open a pull request with a short description of the use case and why it's useful.
