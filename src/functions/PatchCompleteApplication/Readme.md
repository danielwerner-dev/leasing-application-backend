# PatchCompleteApplication Lambda function
The PatchCompleteApplication endpoint is used to finish user input and to send data to Yardi and other
dependent systems to begin processing

## What is this document
This Readme will give you the steps needed to run the complete application handler locally. 
This allows you to develop significantly faster compared to deploying to the cloud for every test.

## Overview of steps needed to start

1. Setup application with correct data
1. Setup environment variables
1. copy tests.ts code into a local tests.ts file
1. Build tests.ts
1. Execute tests.cjs

### Setup application with the correct data
To use this test class you will need to setup an application in "ready to submit" state.
"Ready to submit" means that you will have gone through all of the stages in the Leasing Application Dashboard
and stopped at the "pay and submit" button.

### Setup environment variables
Replace values where needed and copy into shell and execute
```shell
# required to run the function
export AWS_ACCESS_KEY_ID= # get from aws
export AWS_SECRET_ACCESS_KEY= # get from aws
export CUSTOMER_SERVICE_URL='https://api-qa.invitationhomes.com/customer/v1'
export YARDI_SERVICE_URL='https://yardi-api.qainvh.com/v1'
export ENVIRONMENT='qa'
export LEASING_APPLICATION_BASE_URL='https://www.qainvh.com'
export EMAIL_DELIVERY_SERVICE_URL='https://email-delivery-api.qainvh.com/v1'
```

### tests.ts
Copy file at the bottom of this document into a new tests.ts folder (in the same directory as this Readme)

Build and execute:

```shell
node_modules/.bin/esbuild --bundle --outfile=dist/tests.cjs --platform=node ./src/functions/PatchCompleteApplication/tests.ts
node dist/tests.cjs
# or
node_modules/.bin/esbuild --bundle --outfile=dist/tests.cjs --platform=node ./src/functions/PatchCompleteApplication/tests.ts && node dist/tests.cjs
```

```ts
// tests.ts
import { eventFixture } from '../../../tests/fixtures/index';
import { handler } from './index';

const application_id = 'your application id';
const bearerToken = 'your bearer token';
const authorization = `Bearer ${bearerToken}`;
const body = {
  amountToPay: 50,
  applicantsToPay: ['some@email.com']
};

const event = eventFixture({
  isBase64Encoded: true,
  pathParameters: { application_id },
  headers: { authorization, 'client-ip-address': '123.123.123.123' },
  body: JSON.stringify(body)
});

(async () => {
  const result = await handler(event);
  console.log('response', result);
})();

```