# Week 3 — Decentralized Authentication

## Notes from lecture

### Step 1
- Create a user pool in AWS Cognito
- We did this through the AWS console UI

### Step 2
- Install AWS Amplify:
    - Amplify is a low-code/no-code platform that allows you to build serverless applications
    - It's an SDK that installs a bunch of libraries
    - It's also a way for provisioning back-end resources (including auth)
        - We don't need this since we're using Cognito manually / separately for authentication
    - Amplify is like Firebase in GCP but don't work the same way

- Install amplify in the front-end directory since we're only going to be using amplify for our front end app:
```npm i aws-amplify --save```
- The ```--save``` puts it in our ```frontend-react-js/package.json``` directory as a dependency

### Step 3: 
- Next step is to configure amplify
- This is done by putting a bunch of code in the ```frontend-react-js/src/App.js```
- This is the list of env vars we need to populate in our docker-compose:
```shell
REACT_APP_AWS_PROJECT_REGION
REACT_APP_AWS_COGNITO_IDENTITY_POOL_ID: We don't need this
REACT_APP_AWS_COGNITO_REGION
REACT_APP_AWS_USER_POOLS_ID: We get this one from our AWS Cognito portal and is created when we create a user pool
REACT_APP_CLIENT_ID: Similar to the above, we get this when we create an app in AWS Cognito
```

### Step 4:
- Conditionally show stuff when user logged in vs not logged in