# Gramba Example Lambda

This is an example lambda for the Gramba project that can be found here.

- Gramba (and GraalVM) is at this point still experimental and shouldn't be used in a production environment without
knowing the risks.

## Instructions

You can get a detailed list of instructions by reading the blogpost that goes with this example -coming soon-

### Links

- Gramba core
    - https://github.com/becloudway/gramba
- Gramba addons
    - https://github.com/becloudway/gramba-addons
- Maven central artifacts
    - https://search.maven.org/search?q=be.cloudway.gramba

## Before, you continue

These instructions expect that you have the following perquisites:

- AWS Account with enough access rights
    - S3 bucket for the build artifacts
    - Programmatic access
- Maven installed
- Docker installed (used to create the native image)
- AWS Serverless (SAM) installed
- Java 8 (JDK)
    - Which is the only supported java version for now

> The current version has been tested on Linux and MacOS systems, we didn't test any Windows system as of now. If you experience issues, then please provide an issue the [Gramba repository](https://github.com/becloudway/gramba)

### Getting the source

You have two options you can either pull this repository and use that as your starting point or scroll down and use the archetype that we provided.

> We recommend the archetype as it should be up-to-date with the latest version of gramba.


### Step 1. Build the native image

This should be as simple as running the following command:

```bash
mvn clean package
```

This creates the required artifacts in your target folder, and starts compiling the native-image which can take some time.

This would also run a test against your native image witch if successful looks something like this:

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

### Step 2. Package and deploy the lambda

Now that our native function is available and configured we can deploy it to AWS.
There is a template `template.yaml` provided which can be used with AWS SAM.

Package the build and upload the artifacts to your bucket.

```bash
sam package \
    --template-file template.yaml \
    --output-template-file packaged.yaml \
    --s3-bucket <YOUR_BUCKET>
```

Deploy!
```bash
sam deploy \
    --template-file packaged.yaml \
    --stack-name gramba-test \
    --capabilities CAPABILITY_IAM
```

### Step 3. Test

Now you should have everything deployed and ready to go, the only thing left to do is obtaining the API
url.

This can be found using the following command:

```bash
aws cloudformation describe-stacks \
       --stack-name gramba-test \
       --query 'Stacks[].Outputs'
```

It should look something like:
https://01234567890.execute-api.eu-west-1.amazonaws.com/Prod/test/10

## Using the artifact

There is also a maven archetype that can be used with the following command: 

```bash
mvn archetype:generate \
 -DgroupId=my.gramba.lambda \
 -DartifactId=mygramba \
 -DarchetypeGroupId=be.cloudway \
 -DarchetypeArtifactId=gramba-aws-lambda-archetype \
 -DarchetypeVersion=0.0.5
```

This will create a folder for you with the name that you provided as the artifactId.

From that point just repeat from step 1 to create the image.

## Roadmap

This example is to be extended with more AWS services and more advanced examples of
using GraalVM and the gramba toolchain/runtime.
