# Step-by-Step Guide to Create a CDK PING/PONG

### Initialize a New CDK Project:
* Create a new directory and initialize a CDK project in TypeScript.
```
mkdir cdk-ping-pong
cd cdk-ping-pong
cdk init app --language typescript
```
* Install Dependencies:
```
npm install @aws-cdk/aws-lambda @aws-cdk/aws-apigateway
```

### Write the Lambda Function:
* Inside the lib directory, you'll find a file named cdk-ping-pong-stack.ts.
  Open this file and modify it to define the Lambda function and API Gateway.
```
import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';
import * as lambda from 'aws-cdk-lib/aws-lambda';
import * as apigateway from 'aws-cdk-lib/aws-apigateway';

export class CdkPingPongStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // Create the Lambda function
    const pingFunction = new lambda.Function(this, 'PingFunction', {
      runtime: lambda.Runtime.NODEJS_18_X,  // Node.js 18 runtime
      code: lambda.Code.fromInline(`
        exports.handler = async function(event) {
          return {
            statusCode: 200,
            body: JSON.stringify('pong')
          };
        };
      `),
      handler: 'index.handler',
    });

    // Create the API Gateway REST API
    const api = new apigateway.LambdaRestApi(this, 'PingPongApi', {
      handler: pingFunction,
      proxy: false
    });

    // Define the /ping endpoint
    const ping = api.root.addResource('ping');
    ping.addMethod('GET');  // GET /ping
  }
}
```

### Bootstrap And Build Your AWS Environment:
* If your environment hasn’t been bootstrapped yet, run:
```
cdk bootstrap
```
* Build and Deploy Your CDK Stack:
```
npm run build
```
* Deploy your CDK stack to AWS:
```
cdk deploy
```
* Monitor the Deployment Output:
Note the API Gateway endpoint URL provided in the deployment output.

#### Test the API Using Docker
* Run CURL Command
Replace <API_GATEWAY_URL> with the actual URL provided during the CDK deployment.
```
curl https://<api-gateway-url>/ping
```
