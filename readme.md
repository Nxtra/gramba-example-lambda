# Gramba Example Lambda

This is an example lambda for the gramba project that can be found here.

- Gramba is at this point still experimental and shouldn't be used in a production environment without
knowing the risks.

## Instructions

You can get a detailed list of instruction by reading the blogpost that goes with this example -coming soon-

## Before you continue

These instructions expect that you have the following perquisites:

- AWS Account
- Access to your AWS account
- Maven installed
- Docker installed (used to create the native image)
- AWS Serverless (SAM) installed 
- S3 bucket for the build artifacts


### Step 1. Build the native image

This should be as simple as running the following command:

```bash
mvn clean package
```

This creates the required artifacts in your target folder, it can take some time to compile 
a native image.

This would also run a test against your native image wich if successfull looks something like this:

```bash
[INFO] [gramba-test-lambda-0.0.1:14]    classlist:   8,682.33 ms
[gramba-test-lambda-0.0.1:14]        (cap):   1,224.05 ms
[gramba-test-lambda-0.0.1:14]        setup:   3,223.59 ms
[gramba-test-lambda-0.0.1:14]   (typeflow):  20,525.45 ms
[gramba-test-lambda-0.0.1:14]    (objects):  19,932.76 ms
[gramba-test-lambda-0.0.1:14]   (features):   1,011.63 ms
[gramba-test-lambda-0.0.1:14]     analysis:  42,534.88 ms
[gramba-test-lambda-0.0.1:14]     universe:   1,054.58 ms
[gramba-test-lambda-0.0.1:14]      (parse):   4,490.73 ms
[gramba-test-lambda-0.0.1:14]     (inline):   2,800.16 ms
[gramba-test-lambda-0.0.1:14]    (compile):  39,786.54 ms
[gramba-test-lambda-0.0.1:14]      compile:  48,571.90 ms
[gramba-test-lambda-0.0.1:14]        image:   2,920.00 ms
[gramba-test-lambda-0.0.1:14]        write:     493.90 ms
[gramba-test-lambda-0.0.1:14]      [total]: 108,054.23 ms
-- BUILD SUCCESSFUL --
{"headers":null,"body":"{\"response\":\"The ID is: 1\"}","isBase64Encoded":null,"statusCode":200}
-- TESTS SUCCESSFUL --

```

### Step 3. Package and deploy the lambda

Now that our native function is available and configured we can deploy it to AWS.
There is a template `template.yaml` provided which can be packaged and deployed using AWS SAM.

Package the build and upload the artifacts to your bucket.
```
sam package \
    --template-file template.yaml \
    --output-template-file packaged.yaml \
    --s3-bucket <YOUR_BUCKET>
```

Deploy!
```
sam deploy \
    --template-file packaged.yaml \
    --stack-name gramba-test \
    --capabilities CAPABILITY_IAM
```

### Step 4. Test

Now you should have everything deployed and ready to go, the only thing left to do is obtaining the API
url. 

This can be found using the following command: 

```
aws cloudformation describe-stacks \
       --stack-name gramba-test \
       --query 'Stacks[].Outputs'
```

It should look something like:
https://01234567890.execute-api.eu-west-1.amazonaws.com/Prod/test/10

## Roadmap

This example is to be extended with more AWS services and more advanced examples of 
using GraalVM and the gramba toolchain/runtime.