# PostgreSQL to Snowflake Sync Mission

## Overview
This directory contains a Dagster Sling replication setup to sync data from PostgreSQL to Snowflake, addressing GitHub issue #13.

## Current Setup

### Components Configured
- **Sling Replication Component**: Using `dagster_sling.SlingReplicationCollectionComponent`
- **Source Connection**: PostgreSQL database connection configured with environment variables
- **Target Connection**: Snowflake warehouse connection configured with environment variables
- **Replication Configuration**: Basic full-refresh sync setup for public schema tables

### Files
- `defs.yaml`: Main Dagster component definition with connection configurations
- `replication.yaml`: Sling-specific replication configuration (needs manual completion)

## Environment Variables Required
The following environment variables must be set:

### PostgreSQL Source
- `POSTGRES_HOST`: PostgreSQL server hostname
- `POSTGRES_PORT`: PostgreSQL server port (typically 5432)
- `POSTGRES_USER`: PostgreSQL username
- `POSTGRES_PASSWORD`: PostgreSQL password
- `POSTGRES_DATABASE`: PostgreSQL database name
- `POSTGRES_SCHEMA`: PostgreSQL schema name (typically 'public')

### Snowflake Target
- `SNOWFLAKE_ACCOUNT`: Snowflake account identifier
- `SNOWFLAKE_USER`: Snowflake username
- `SNOWFLAKE_PASSWORD`: Snowflake password
- `SNOWFLAKE_DATABASE`: Snowflake database name
- `SNOWFLAKE_WAREHOUSE`: Snowflake warehouse name
- `SNOWFLAKE_ROLE`: Snowflake role name

## Next Steps to Complete the Setup

### 1. Complete replication.yaml Configuration
The `replication.yaml` file needs to be manually updated with the following content:

```yaml
source: postgres_source
target: snowflake_target

# Default configuration for all streams
defaults:
  mode: full-refresh
  object: "{stream_schema}.{stream_table}"

streams:
  # Example: sync all tables from public schema
  "public.*":
    mode: full-refresh
    target_options:
      # Create schema if it doesn't exist
      pre_sql: "CREATE SCHEMA IF NOT EXISTS {target_schema}"
```

### 2. Customize Stream Configuration
- Modify the `streams` section to specify which tables/schemas to sync
- Consider changing from `full-refresh` to `incremental` mode for large tables if supported
- Add table-specific configurations as needed

### 3. Set Environment Variables
Create a `.env` file or set environment variables in your deployment environment with all the required connection parameters.

### 4. Test the Connection
- Use `dg check defs` to validate the full configuration loads correctly
- Test the connections manually using Sling CLI if needed

### 5. Schedule the Sync
Consider adding a schedule or sensor to automate the sync process:
- Add a `dagster.ScheduleDefinition` to run the sync on a regular basis
- Or create a sensor that triggers based on data changes

### 6. Monitor and Optimize
- Set up monitoring for sync failures
- Add asset checks for data quality validation
- Optimize performance for large datasets

## Integration Points
This sync can be integrated with other Dagster assets by:
- Making Snowflake tables assets that depend on this sync
- Creating downstream transformations that use the synced data
- Adding data quality checks on the synced tables

## Documentation References
- [Sling Documentation](https://docs.slingdata.io/)
- [Dagster Sling Integration](https://docs.dagster.io/integrations/embedded-elt/sling)
- [Sling Replication Configuration](https://docs.slingdata.io/concepts/replication)