# Week 4 — Postgres and RDS


## Homework checklist

### Watched Ashish's Week 4 - Security Considerations	
- Done and security quiz complete!
- A lot of good info on securing RDS databases
- Main things: don't make publicly available and whitelist IPs

### Create RDS Postgres Instance	
- 
### Bash scripting for common database actions	
### Install Postgres Driver in Backend Application	
### Connect Gitpod to RDS Instance	
### Create Congito Trigger to insert user into database	
### Create new activities with a database insert	

## Lecture Notes
- AWS Aurora vs AWS RDS Postgrs, which is better?
    - Depends on use case, RDS Posgres is way cheaper and I assume less fully featured?

- Used the aws-cli to deploy an RDS Postgres instance
- Important to note that RDS Postgres can only be stopped temporarily for 7 days!!!
    - After that, it turns back on and starts to incur spend
- The main things we accomplished this week are:
    1. Creating the production postgres database in RDS
    2. Populating the development postres database running in our dev environment

## Creating production postgres db using RDS on amazon:
```shell
aws rds create-db-instance \
  --db-instance-identifier cruddur-db-instance \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --engine-version  14.6 \
  --master-username XXX \
  --master-user-password XXX \
  --allocated-storage 20 \
  --availability-zone ca-central-1a \
  --backup-retention-period 0 \
  --port 5432 \
  --no-multi-az \
  --db-name cruddur \
  --storage-type gp2 \
  --publicly-accessible \
  --storage-encrypted \
  --enable-performance-insights \
  --performance-insights-retention-period 7 \
  --no-deletion-protection
```

## Setting up environment variables for the database connections

```bash
export CONNECTION_URL="postgresql://postgres:password@localhost:5432/cruddur"

gp env CONNECTION_URL="postgresql://postgres:password@localhost:5432/cruddur"

export PROD_CONNECTION_URL="postgresql://cruddurroot:Test1234!@cruddur-db-instance.cwfc17rhrksf.ca-central-1.rds.amazonaws.com:5432/cruddur"

gp env PROD_CONNECTION_URL="postgresql://cruddurroot:Test1234!@cruddur-db-instance.cwfc17rhrksf.ca-central-1.rds.amazonaws.com:5432/cruddur"
```

## Creating tables in the dev postgres database
```sql
DROP TABLE IF EXISTS public.users;

CREATE TABLE public.users (
  uuid UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  display_name text,
  handle text,
  cognito_user_id text,
  created_at TIMESTAMP default current_timestamp NOT NULL
);

DROP TABLE IF EXISTS public.activities;

CREATE TABLE public.activities (
  uuid UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  message text NOT NULL,
  replies_count integer DEFAULT 0,
  reposts_count integer DEFAULT 0,
  likes_count integer DEFAULT 0,
  reply_to_activity_uuid integer,
  expires_at TIMESTAMP,
  created_at TIMESTAMP default current_timestamp NOT NULL
);
```