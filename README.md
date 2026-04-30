# swarmbench-bank-churn-consensus
Synthetic adversarial dataset for SwarmBench multi-agent evaluation. 10M rows across 20 CSV shards with cross-shard duplicates, inconsistent Geography labels, and dirty currency formatting. Designed to test coverage pressure and consensus aggregation.

## Dataset Overview

- **10 customer shards** + **10 account shards** = 20 CSV files
- **500,000 rows per file** (10M total rows)
- **100,000 unique customers** with heavy intra-shard duplication (~5x)
- Adversarial formatting: inconsistent Geography labels, dirty currency strings

## Schema

### Customer Shards (`customer_shard_{1-10}.csv`)
| Column | Type | Notes |
|--------|------|-------|
| CustomerId | int | Unique identifier |
| Surname | str | Generated username |
| Geography | str | Dirty: `France`, `FRA`, `French`, `DE`, `Germany`, `Spain` |
| Gender | str | `Male`, `Female`, `F`, `M` |
| Age | int | 18-90 |
| Tenure | int | 0-10 |
| EstimatedSalary_Dirty | str | Currency-formatted: `€105,701.65` |

### Account Shards (`account_shard_{1-10}.csv`)
| Column | Type | Notes |
|--------|------|-------|
| CustomerId | int | Links to customer shard |
| CreditScore | int | 300-850 |
| Balance | str | Currency-formatted: `€195482.49` |
| NumOfProducts | int | 1-4 |
| HasCrCard | mixed | `Yes`/`No`/1/0 |
| IsActiveMember | mixed | `Yes`/`No`/1/0 |
| EstimatedSalary | float | Clean numeric |
| Exited | int | 0/1 |

## Adversarial Design

1. **Cross-shard duplicates**: Same CustomerId appears in multiple shards with potentially different Geography
2. **Geography inconsistency**: `France` vs `fra` vs `french` vs `DE` vs `Germany`
3. **Currency noise**: `€` symbol and comma separators in Balance/EstimatedSalary
4. **Intra-shard duplication**: ~5x duplicates within each shard (500K rows → ~100K unique)
5. **Schema mismatch**: Customer shards have `EstimatedSalary_Dirty`, account shards have clean `EstimatedSalary`

## Generation

```bash
pip install -r requirements.txt
python generate_shards.py
