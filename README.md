# Serverless: Auto update security groups with Cloudfront IPs

![servrless](https://github.com/abiydv/ref-docs/blob/master/images/logos/serverless_small.png)
![py](https://github.com/abiydv/ref-docs/blob/master/images/logos/python_small.png)
![cli](https://github.com/abiydv/ref-docs/blob/master/images/logos/aws-cli_small.png)
![aws-lambda](https://github.com/abiydv/ref-docs/blob/master/images/logos/aws-lambda_small.png)

Serverless framework example to deploy AWS Lambda, which auto updates security groups with Cloudfront IPs. Lambda runtime used - python 3.7

:warning: This stack should only be created in `us-east-1`. In any other region, the lambda will not be able to subscribe to the AWS public topic.

## A FEW WORDS
The AWS example lambda [here](https://github.com/aws-samples/aws-cloudfront-samples/tree/master/update_security_groups_lambda) will work only if you create the stack in us-east-1 as well as have security groups which are in us-east-1.  
I have modified their function to work with security groups in other regions as well.  
The entire stack will exist in us-east-1 only while it will operate on security groups in other regions as well.  
Be careful about the region selection. Specify the security group region in the [config-dev](./config-dev.json) file

## BEFORE YOU BEGIN
#### 1. Serverless. 
Install serverless, follow this [guide](https://serverless.com/framework/docs/providers/aws/guide/installation/)
  
#### 2. AWS Cli
Setup aws cli with profiles matching environments/stages. A sample `~/.aws/credentials` file - 
```
[dev]
aws_access_key_id = DEV_ACCESS_KEY
aws_secret_access_key = DEV_SECRET_KEY
[qa]
aws_access_key_id = QA_ACCESS_KEY
aws_secret_access_key = QA_SECRET_KEY
[prod]
aws_access_key_id = PROD_ACCESS_KEY
aws_secret_access_key = PROD_SECRET_KEY
```

## USAGE
#### 1. Deploy the service 
Deploy the service with `--stage dev` argument to create the **dev** stack. Use **qa** or **prod** to launch function in other environments. <br><br>
***NOTE**: Create `config-qa.json` and `config-prod.json` with the respective AWS Account Id info.*
```
serverless deploy -v --stage dev --aws-region us-east-1 
```
#### 2. Invoking the lambda function
First, create the lambda test event using the sample [event](./event.json) file. Next, execute the function. On first execution you will receive an error, like - 
```
[ERROR] Exception: MD5 Mismatch: got 2182bff9048fe44d4c1d9b37903fc632 expected 45be1ba64fe83acb7ef247bccbc45704
```
Now, replace the MD5 in the test event and execute again, to populate the security groups.
```
serverless invoke -f updatesg -l --stage dev
```
After the stack is created, this lambda will be auto-triggered everytime there is a change in any IP as reported by AWS, it need not be executed manually later.

#### 3. Deploy only the lambda function after any change in lambda code. (Skip if no change)
In case you need to deploy a change in the function code, you do not need to deploy the entire stack again. Use the following command to deploy only the lambda function
 
```
serverless deploy -f updatesg --stage dev
```

#### 4. Cleanup
:rocket: Nuke the setup after you are done testing/looking.
```
serverless remove -v --stage dev
```

## CONTACT

Drop me a note or open an issue if something doesn't work out.

Cheers! :thumbsup:
