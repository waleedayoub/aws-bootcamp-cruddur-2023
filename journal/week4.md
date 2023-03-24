# Week 4 — Postgres and RDS


## Homework checklist

### Watched Ashish's Week 4 - Security Considerations	
- Done and security quiz complete!
- A lot of good info on securing RDS databases
- Main things: don't make publicly available and whitelist IPs

### Create RDS Postgres Instance	
- See the aws cli create command below for this

### Bash scripting for common database actions	
- Created a whole bunch of database actions
- The way this is organized is like so:
- backend-flask/db has all the sql snippets
  - These snippets are things like schema.sql, seed.sql, sql/activities/create.sql, etc.
  - The point of these snippets is to create the various tables and in some cases, seed them with data
- backend-flast/bin contains the various psql commands used to instantiate our database
  - in some cases (like db-schema-load or db-seed), they are actually calling sql commands from backend-flask/db

### Install Postgres Driver in Backend Application	
- This one was a HUGE pain in the ass to troublshoot
- Mainly because I didn't realize docker wants the container name in the postgres connection string and NOT localhost or 127.0.0.1
- I FINALLY MANAGED TO GET THIS TO WORK

### Connect Gitpod to RDS Instance	
- Add GITPOD_IP as an env var in GITPOD like this:
```shell
export GITPOD_IP=$(curl ifconfig.me)
gp env GITPOD_IP=$(curl ifconfig.me)
```

```shell
export DB_SG_ID="sg-0ec494ab10dc821bb"
gp env DB_SG_ID="sg-0ec494ab10dc821bb"
export DB_SG_RULE_ID="sgr-09d6436bccca0ab47"
gp env DB_SG_RULE_ID="sgr-09d6436bccca0ab47"
```

```
aws ec2 modify-security-group-rules \
    --group-id $DB_SG_ID \
    --security-group-rules "SecurityGroupRuleId=$DB_SG_RULE_ID,SecurityGroupRule={Description=GITPOD,IpProtocol=tcp,FromPort=5432,ToPort=5432,CidrIpv4=$GITPOD_IP/32}"
```

### Create Congito Trigger to insert user into database	
- In this step, what we're doing is adding an entry to our cruddur.users table with the new user that was created
- Since we're using Cognito for decentralized user authentication, we would need to tell cognito to do this after a user signs up on the main site
- I assume this user will be added to our original cognito user pool called `cruddur-user-pool`
- When creating the lambda in the AWS console, a few things to consider:
  - you need to create an environment variable since the code in the lambda will refer to CONNECTION_URL
  - in this case, it should be the production connection url

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

NOT THIS:
```bash
export CONNECTION_URL="postgresql://postgres:password@localhost:5432cruddur"
```
BUT THIS:
```bash
export CONNECTION_URL="postgresql://postgres:password@db:5432cruddur"

gp env CONNECTION_URL="postgresql://postgres:password@db:5432/cruddur"

export PROD_CONNECTION_URL='postgresql://cruddurroot:Test1234!@cruddur-db-instance.cwfc17rhrksf.ca-central-1.rds.amazonaws.com:5432/cruddur'

gp env PROD_CONNECTION_URL='postgresql://cruddurroot:Test1234!@cruddur-db-instance.cwfc17rhrksf.ca-central-1.rds.amazonaws.com:5432/cruddur'
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