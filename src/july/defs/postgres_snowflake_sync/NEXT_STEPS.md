# Postgres to Snowflake Sync - Next Steps

## Overview
This component addresses GitHub issue #13: "Create a sync from Postgres to Snowflake" using Sling for data replication.

## What Was Accomplished
1. **Sling Integration Setup**: Installed `dagster-sling` package and scaffolded a `SlingReplicationCollectionComponent`
2. **Configuration Structure**: Created basic configuration with:
   - Postgres source connection
   - Snowflake target connection
   - Sample table replications (users, orders, products)

## Current Configuration
- **Location**: `src/july/defs/postgres_snowflake_sync/`
- **Component Type**: `dagster_sling.SlingReplicationCollectionComponent`
- **Sample Tables**: 
  - `users` (full-refresh mode)
  - `orders` (incremental mode with update_key)
  - `products` (full-refresh mode)

## Required Next Steps

### 1. Environment Configuration
Set up the following environment variables:
- `POSTGRES_HOST` - Postgres server hostname
- `POSTGRES_PORT` - Postgres server port (typically 5432)
- `POSTGRES_DATABASE` - Source database name
- `POSTGRES_USER` - Postgres username
- `POSTGRES_PASSWORD` - Postgres password
- `SNOWFLAKE_HOST` - Snowflake account URL
- `SNOWFLAKE_USER` - Snowflake username
- `SNOWFLAKE_PASSWORD` - Snowflake password
- `SNOWFLAKE_DATABASE` - Target database name
- `SNOWFLAKE_WAREHOUSE` - Snowflake warehouse name
- `SNOWFLAKE_ROLE` - Snowflake role name
- `SNOWFLAKE_SCHEMA` - Target schema (currently set to use env var)

### 2. Customize replication.yaml
Edit `replication.yaml` to:
- Update table names to match your actual Postgres schema
- Configure appropriate sync modes (full-refresh, incremental, etc.)
- Set correct primary keys and update keys for incremental tables
- Add/remove tables as needed for your use case

### 3. Test Connections
- Verify Postgres connection parameters
- Verify Snowflake connection parameters
- Test connectivity to both databases

### 4. Sling Binary Installation
Ensure Sling CLI is installed on the system where Dagster will run:
```bash
# Install Sling binary (https://docs.slingdata.io/sling-cli/getting-started)
curl -LO https://github.com/slingdata-io/sling-cli/releases/latest/download/sling_linux_amd64.tar.gz
tar -xf sling_linux_amd64.tar.gz
sudo mv sling /usr/local/bin/
```

### 5. Schema Creation
Create target schemas in Snowflake:
```sql
USE DATABASE <your_database>;
CREATE SCHEMA IF NOT EXISTS raw;
```

### 6. Data Validation
After initial sync:
- Verify row counts match between source and target
- Check data types and transformations
- Validate incremental updates work correctly

### 7. Production Considerations
- Set up monitoring and alerting for failed syncs
- Configure appropriate retry policies
- Consider partitioning strategies for large tables
- Set up access controls and permissions
- Plan for schema evolution handling

## Useful Commands
- `dg check yaml` - Validate configuration
- `dg check defs` - Validate definitions by loading them
- `dg list defs` - Show project definitions
- `dg list env` - List required environment variables

## References
- [Sling Documentation](https://docs.slingdata.io/)
- [Sling Replication Config](https://docs.slingdata.io/concepts/replication#overview)
- [Dagster Sling Integration](https://docs.dagster.io/integrations/embedded-elt/sling)