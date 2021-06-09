# Brief

Create a solution to store;
- API service account details (username, password)
- Database connection details (username, password, host, port, db name)
- SSH key
- GPG key

Execute it in a manner that adheres to IAC recommendations and justify AWS service(s) choices.

# Solution
To test and run the solution, you need access to an AWS environment or utilise LocalStack to mock AWS services. [Details on how to set up LocalStack](https://hub.docker.com/r/localstack/localstack/).
To deploy it, use the AWS Console or utilise a cli call as below
```bash
# With LocalStack deployment
aws cloudformation create-stack --endpoint-url http://localhost:4566 --stack-name secrets-stack --region ap-southeast-2 --template-body file://secrets-stack.yaml
# With a Real AWS connection/credentials
ws cloudformation create-stack --stack-name secrets-stack --region ap-southeast-2 --template-body file://secrets-stack.yaml --profile personal
```
Points of consideration;
- Parameters were created so that they can be passed to the stack-create call as required
- While not scoped for this example, Resource permissions can be utilised to limit access

### Choice of IaC Tool
**AWS Cloudformation** was used as it is the leanest option. Spec did not state anything about pre-existing technologies hence I picked the one that will be minimally required (aws cloudformation is part of aws-cli) in any CICD environment for AWS deployments.


### Choice of AWS Service
AWS Secrets Manager is the best choice for providing an environment for configuring, and retrieving secrets on a needs basis. Reasons it was used;

- Automated keystore rotation (if required) and password generation
- Alike KMS, RBAC can be implemented per-secret for fine-grained access controls for IAM users/roles
- API calls to retrieve secrets are logged in CloudTrail, useful for alerting and monitoring purposes
- If required, Secrets can be shared Cross Account as well

Other tools that could be possibly considered were listed below for completions sake

- SM Parameter Store -> used to store unencrypted K/V pairs such as configuration values, offers no features for security
- KMS -> used mainly to store encryption keys for AWS services, does not offer tooling for Secrets Management or Rotation
- S3 -> used to store files, not helpful services to maintain and manage secrets. Please don't use this to store secrets.