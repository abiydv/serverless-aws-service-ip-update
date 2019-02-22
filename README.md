# Serverless: Auto update security groups with Cloudfront IPs

![servrless](https://github.com/abiydv/ref-docs/blob/master/images/logos/serverless_small.png)
![py](https://github.com/abiydv/ref-docs/blob/master/images/logos/python_small.png)
![cli](https://github.com/abiydv/ref-docs/blob/master/images/logos/aws-cli_small.png)
![aws-lambda](https://github.com/abiydv/ref-docs/blob/master/images/logos/aws-lambda_small.png)

Serverless framework example to deploy AWS Lambda, which auto updates security groups with Cloudfront IPs. Lambda runtime used - python 3.7

:warning: *This stack should only be created in `us-east-1`. In any other region, the lambda will not be able to subscribe to the AWS public topic.*

## A FEW WORDS
The AWS example lambda [here](https://github.com/aws-samples/aws-cloudfront-samples/tree/master/update_security_groups_lambda) will work only if you create the stack in `us-east-1` as well as have security groups which are in `us-east-1`.  
I have modified their function to work with security groups in other regions.  
The entire stack will exist in `us-east-1` only while it will operate on security groups in other regions as well.  
Be careful about the region selection. Specify the security group region in the [config-dev](./config-dev.json) file

## BEFORE YOU BEGIN
#### 1. Serverless. 
Install serverless, follow this [guide](https://serverless.com/framework/docs/providers/aws/guide/installation/)
  
#### 2. AWS Cli
Setup aws cli with profiles matching environments/stages. A sample `~/.aws/credentials` file entry for `dev` profile - 
```
[dev]
aws_access_key_id = DEV_ACCESS_KEY
aws_secret_access_key = DEV_SECRET_KEY
```

## USAGE
#### 1. Deploy the app 
Deploy the app with `-s dev` argument to create the **dev** stack. Change `-s dev` to `-s qa` or `-s prod` to launch in Use **qa** or **prod** environments.  
***NOTE**: Create `config-qa.json` and `config-prod.json` with the respective AWS Account Id info.*
```
sls deploy -v -s dev 
```
#### 2. Invoke the Lambda

Once the stack is deployed, execute your lambda with `event.json` as payload.
```
sls invoke -f updatesg -p event.json -s dev
```

If you get an error like - 
```
[ERROR] Exception: MD5 Mismatch: got 2182bff9048fe44d4c1d9b37903fc632 expected 45be1ba64fe83acb7ef247bccbc45704
```
Replace the MD5 after got (2182....) in the `event.json` file and execute again. It should succeed now.
```
sls invoke -f updatesg -p event.json -s dev
```

From this point on, Lambda will be auto-triggered everytime there is a change in any IP as reported by AWS, it need not be executed manually later.

#### 3. Deploy a code change to Lambda (Skip if no change)
To deploy a new function code, you do not need to deploy the entire stack. Use the following command to deploy only the lambda function
 
```
sls deploy -f updatesg -s dev
```

#### 4. Cleanup
:rocket: Nuke the setup after you are done testing/looking.
```
sls remove -v -s dev
```

## CONTACT

Drop me a note or open an issue if something doesn't work out.

Cheers! :thumbsup:
