# Serverless: AWS Lambda to rollback AWS ECS deployment
[![CodeFactor](https://www.codefactor.io/repository/github/abiydv/serverless-aws-lambda-ecs-rollback/badge)](https://www.codefactor.io/repository/github/abiydv/serverless-aws-lambda-ecs-rollback)

![servrless](https://github.com/abiydv/ref-docs/blob/master/images/logos/serverless_small.png)
![py](https://github.com/abiydv/ref-docs/blob/master/images/logos/python_small.png)
![cli](https://github.com/abiydv/ref-docs/blob/master/images/logos/aws-cli_small.png)
![aws-lambda](https://github.com/abiydv/ref-docs/blob/master/images/logos/aws-lambda_small.png)

Serverless framework example to deploy AWS Lambda, which auto updates security groups with Cloudfront IPs. Lambda runtime used - python 3.7

This stack should only be created in US-EAST-1. In any other region, the lambda will not be able to subscribe to the AWS topic. To modify security groups in other regions, modify the region in the [config-dev](./config-dev.json) file

## Prerequisites
   **1.** Install serverless. Follow this [guide](https://serverless.com/framework/docs/providers/aws/guide/installation/)
   
   **2.** Setup aws cli with profiles matching environments/stages. A sample `~/.aws/credentials` file - 
   
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

## How to use

  **1.** Deploy the service with `--stage dev` argument to create the **dev** stack. Use **qa** or **prod** to launch function in other environments. <br><br>
  ***NOTE**: Create `config-qa.json` and `config-prod.json` with the respective AWS Account Id info.*

  ```
  serverless deploy -v --stage dev --aws-region us-east-1 
  ```

  **2.** Invoke the lambda function

  Create the lambda event using the sample [event](./event.json) file

  On first execution you will receive an error, like - 
  ```
  [ERROR] Exception: MD5 Mismatch: got 2182bff9048fe44d4c1d9b37903fc632 expected 45be1ba64fe83acb7ef247bccbc45704
  ```
  Replace the MD5 in the test event and execute again, to populate the security groups.

  ```
  serverless invoke -f updatesg -l --stage dev
  ```


  **3.** Deploy only the lambda function after any change in lambda code. (Skip if no change)
  ```
  serverless deploy -f updatesg --stage dev
  ```

  **4.** Cleanup everything
  ```
  serverless remove -v --stage dev
  ```

## Contact

Drop me a note or open an issue if something doesn't work out.

Cheers! :thumbsup:
