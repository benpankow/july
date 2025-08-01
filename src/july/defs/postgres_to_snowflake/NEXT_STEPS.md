# Postgres to Snowflake Sync - Next Steps

This directory contains a Dagster Sling replication component that syncs data from Postgres to Snowflake.

## What was created

1. **defs.yaml**: Defines the SlingReplicationCollectionComponent with:
   - Connection configuration for Postgres source
   - Connection configuration for Snowflake target  
   - Reference to the replication configuration file

2. **replication.yaml**: Currently contains empty placeholder configuration that needs to be updated. This is the Sling-specific configuration file that defines:
   - Source and target connection names
   - Stream definitions (which tables/schemas to sync)
   - Sync modes and target mappings

## Next steps to complete the setup

### Step 1: Configure Environment Variables
Set the following environment variables with your actual connection details:

**Postgres Source:**
- `POSTGRES_CONNECTION_STRING`: Full PostgreSQL connection string (e.g., `postgresql://user:password@host:port/database`)

**Snowflake Target:**
- `SNOWFLAKE_HOST`: Your Snowflake account hostname
- `SNOWFLAKE_USER`: Snowflake username
- `SNOWFLAKE_PASSWORD`: Snowflake password
- `SNOWFLAKE_DATABASE`: Target database name
- `SNOWFLAKE_WAREHOUSE`: Snowflake warehouse to use
- `SNOWFLAKE_ROLE`: Snowflake role to assume

### Step 2: Configure the replication.yaml file
Update `/src/july/defs/postgres_to_snowflake/replication.yaml` with your specific replication requirements:

```yaml
source: postgres_source
target: snowflake_target

streams:
  "public.*":
    mode: full-refresh
    target:
      schema: "PUBLIC"
```

Or for specific tables:
```yaml
source: postgres_source
target: snowflake_target

streams:
  "public.users":
    mode: incremental
    target:
      schema: "RAW"
      table: "USERS"
  "public.orders":
    mode: full-refresh
    target:
      schema: "RAW"
      table: "ORDERS"
```

### Step 3: Install Sling CLI (if not already installed)
The component uses Sling under the hood, so ensure Sling is installed:
```bash
# Install Sling CLI
curl -LO 'https://github.com/slingdata-io/sling-cli/releases/latest/download/sling_linux_amd64.tar.gz' \
  && tar xf sling_linux_amd64.tar.gz \
  && chmod +x sling && sudo mv sling /usr/local/bin/
```

### Step 4: Test the connection
Before running the Dagster assets, test your connections directly with Sling:
```bash
sling conns test postgres_source
sling conns test snowflake_target
```

### Step 5: Run the Dagster pipeline
Once configured, you can materialize the assets through the Dagster UI or CLI:
```bash
dagster dev
```

## Replication Modes
Consider which replication mode is best for each table:
- **full-refresh**: Truncates and reloads entire table (good for small, dimension tables)
- **incremental**: Only syncs new/changed records (requires tracking column)
- **snapshot**: Creates timestamped versions of data

## Additional Configuration Options
Refer to the [Sling documentation](https://docs.slingdata.io/concepts/replication) for advanced configuration options like:
- Custom SQL transforms
- Data type mappings
- Partitioning strategies
- Error handling policies

## Monitoring and Observability
Once running, you can monitor the sync progress through:
- Dagster UI asset lineage view
- Dagster event logs
- Sling logs (if enabled)
- Snowflake query history