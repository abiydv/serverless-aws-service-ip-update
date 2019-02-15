# Serverless: Auto update security groups with Cloudfront IPs

![servrless](https://github.com/abiydv/ref-docs/blob/master/images/logos/serverless_small.png)
![py](https://github.com/abiydv/ref-docs/blob/master/images/logos/python_small.png)
![cli](https://github.com/abiydv/ref-docs/blob/master/images/logos/aws-cli_small.png)
![aws-lambda](https://github.com/abiydv/ref-docs/blob/master/images/logos/aws-lambda_small.png)

Serverless framework example to deploy AWS Lambda, which auto updates security groups with Cloudfront IPs. Lambda runtime used - python 3.7

## A FEW WORDS :warning:
The AWS example lambda [here](https://github.com/aws-samples/aws-cloudfront-samples/tree/master/update_security_groups_lambda) will work only if you create the stack in `us-east-1` as well as have security groups in `us-east-1`.  
I have modified the function to work with security groups in other regions too.  
The entire stack will still exist in `us-east-1` only but it will update security groups in other regions as well.  
Be careful about the region selection. Specify the security group region in the environment specific config file. In this case, modify the [config-dev](./config-dev.json) file

## BEFORE YOU BEGIN
#### 1. Serverless. 
Install serverless, follow this [guide](https://serverless.com/framework/docs/providers/aws/guide/installation/)
  
#### 2. AWS Cli
Setup aws cli with profiles matching environments/stages. A sample `~/.aws/credentials` file with dev profile setup - 
```
[dev]
aws_access_key_id = DEV_ACCESS_KEY
aws_secret_access_key = DEV_SECRET_KEY
```

## USAGE
#### 1. Deploy the service 
Deploy the service with `--stage dev` argument to create the **dev** stack. Use **qa** or **prod** to launch function in other environments. <br><br>
***NOTE**: Create `config-qa.json` and `config-prod.json` with the respective AWS Account Id info.*
```
sls deploy -v --stage dev
```
#### 2. Invoking the lambda function
First, create the lambda test event using the sample [event](./event.json) file. Next, execute the function. 
```
sls invoke -f updatesg -l --stage dev
```

If you get an MD5 mismatch error, update the MD5 in the event and rerun the function. 
```
[ERROR] Exception: MD5 Mismatch: got 2182bff9048fe44d4c1d9b37903fc632 expected 45be1ba64fe83acb7ef247bccbc45704
```

After the stack is created, this lambda will be auto-triggered everytime there is a change in any IP as reported by AWS, it need not be executed manually later.

#### 3. Deploy only the lambda function
In case you need to deploy a change in the function code, you do not need to deploy the entire stack again. Use the following command to deploy only the lambda function
```
sls deploy -f updatesg --stage dev
```

#### 4. Cleanup
:rocket: Nuke the setup after you are done testing/looking.
```
sls remove -v --stage dev
```

## CONTACT
Drop me a note or open an issue if something doesn't work out.

Cheers! :thumbsup:
