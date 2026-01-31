# Postgres to Snowflake Sync Mission

## Objective
Create a data synchronization pipeline from Postgres to Snowflake using Sling, as requested in GitHub issue #13.

## Current State
✅ **Completed Steps:**
1. Installed the `dagster-sling` package
2. Scaffolded a `SlingReplicationCollectionComponent` for Postgres to Snowflake sync
3. Configured connection definitions in `defs.yaml` with proper environment variable placeholders
4. Created basic replication configuration structure

✅ **Files Created:**
- `defs.yaml`: Contains the Dagster component configuration with Postgres and Snowflake connection definitions
- `replication.yaml`: Placeholder for Sling replication configuration (needs manual update)

## Configuration Details

### Connection Configuration (in defs.yaml)
- **POSTGRES_SOURCE**: Postgres source connection using environment variables
- **SNOWFLAKE_TARGET**: Snowflake target connection using environment variables

### Required Environment Variables
All database credentials and connection details are parameterized using environment variables:

**Postgres Source:**
- `POSTGRES_HOST`
- `POSTGRES_PORT`
- `POSTGRES_USER`
- `POSTGRES_PASSWORD`
- `POSTGRES_DATABASE`

**Snowflake Target:**
- `SNOWFLAKE_ACCOUNT`
- `SNOWFLAKE_USER`
- `SNOWFLAKE_PASSWORD`
- `SNOWFLAKE_DATABASE`
- `SNOWFLAKE_WAREHOUSE`
- `SNOWFLAKE_ROLE`

## Next Steps Required

⚠️ **Critical Manual Tasks:**

1. **Update replication.yaml**: The replication configuration file needs to be manually updated with:
   - Source and target connection references
   - Stream definitions (which tables/schemas to sync)
   - Sync mode (full-refresh, incremental, etc.)
   - Target object naming conventions
   - Example configuration:
   ```yaml
   source: POSTGRES_SOURCE
   target: SNOWFLAKE_TARGET

   defaults:
     mode: full-refresh
     object: "{stream_schema}.{stream_table}"

   streams:
     "public.*":
       sql: "select * from {stream_schema}.{stream_table}"
       object: "PUBLIC.{stream_table}"
   ```

2. **Set Environment Variables**: Configure all required environment variables in your deployment environment

3. **Test Connections**: Verify that both Postgres source and Snowflake target connections work correctly

4. **Customize Sync Logic**: Modify the replication.yaml to match your specific:
   - Source schemas and tables
   - Data transformation requirements
   - Incremental sync strategies (if needed)
   - Target schema structure in Snowflake

5. **Schedule Configuration**: Consider adding schedules or sensors to trigger the sync on a regular basis

## Technical Notes
- Uses Sling's powerful ELT capabilities for efficient data movement
- Supports both full-refresh and incremental sync modes
- Connection credentials are secure through environment variables
- Component is configured to work with Dagster's asset-based architecture

## References
- Sling Documentation: https://docs.slingdata.io/
- Dagster Sling Integration: https://pypi.org/project/dagster-sling/
- Original GitHub Issue: https://github.com/benpankow/july/issues/13