**Note**: If you want to use the solution without building from source, navigate to Solution Landing Page.

# Solution Overview
IoT is a sprawling set of technologies and use cases that has no clear, single definition. Despite enormous advances, we’ve only seen a fraction of what the Internet revolution has yet to deliver. That’s because many powerful technological forces are now converging — poised to magnify, multiply, and exponentially increase the opportunities that software and the Internet can deliver by connecting the devices, or “things”, in the physical world around us. Each of these devices is able to convert valuable information from the real world into digital data that provides increased visibility to businesses of how users interact their products or services. The backend services required to process and uncover these valuable insights can be expensive to prove without a large pool of physical devices for full end to end integration setup or time-consuming development of scripts.

Often times, teams constantly have the need to quickly replicate the behavior of their devices interacting with AWS IoT to assess their backend services. The IoT Device Simulator solution is a Graphical User Interface (GUI) based engine designed to enable customers to get started quickly assessing AWS IoT services without an existing pool of devices. The IoT Device Simulator leverages managed, highly available, highly scalable AWS-native services to effortlessly create and simulate thousands of connected devices that are defined by the customer.

**Note**: This solution is designed to simulate device data for testing. It is not recommended for use in production environments.

# AWS CDK and Solutions Constructs
[AWS Cloud Development Kit (AWS CDK)](https://aws.amazon.com/cdk/) and [AWS Solutions Constructs](https://aws.amazon.com/solutions/constructs/) make it easier to consistently create well-architected infrastructure applications. All AWS Solutions Constructs are reviewed by AWS and use best practices established by the AWS Well-Architected Framework. This solution uses the following AWS Solutions Constructs:
- [aws-cloudfront-s3](https://docs.aws.amazon.com/solutions/latest/constructs/aws-cloudfront-s3.html)
- [aws-lambda-stepfunctions](https://docs.aws.amazon.com/solutions/latest/constructs/aws-lambda-stepfunctions.html)

In addition to the AWS Solutions Constructs, the solution uses AWS CDK directly to create infrastructure resources.
# Customizing the Solution
## Prerequisites for Customization
- Node.js 18.x or later

### 1. Clone the repository
```bash
git clone https://github.com/TurnAirplaneModeOff/NodeJS-IoT_AWS_simulator.git
cd iot-device-simulator
export MAIN_DIRECTORY=$PWD
```

### 2. Declare environment variables
```bash
export REGION=aws-region-code
export DIST_BUCKET_PREFIX=my-bucket-name 
export SOLUTION_NAME=my-solution-name 
export VERSION=my-version 
```
_Note:_ When you define `DIST_BUCKET_PREFIX`, a randomized value is recommended. You will need to create an S3 bucket where the name is `<DIST_BUCKET_PREFIX>-<REGION>`. The solution's CloudFormation template will expect the source code to be located in a bucket matching that name

When creating and using buckets it is recommeded to:
  - Use randomized names or uuid as part of your bucket naming strategy.
  - Ensure buckets are not public.
  - Verify bucket ownership prior to uploading templates or code artifacts.

## Unit Test
After making changes, run unit tests to make sure added customization passes the tests:
```bash
cd $MAIN_DIRECTORY/deployment
chmod +x run-unit-tests.sh
./run-unit-tests.sh
```

## Build
```bash
cd $MAIN_DIRECTORY/deployment
chmod +x build-s3-dist.sh
./build-s3-dist.sh $DIST_BUCKET_PREFIX $SOLUTION_NAME $VERSION
```

## Deploy

- Deploy the distributable to the Amazon S3 bucket in your account. Make sure you are uploading all files and directories under `deployment/global-s3-assets` and `deployment/regional-s3-assets` to `<SOLUTION_NAME>/<VERSION>` folder in the `<DIST_BUCKET_PREFIX>-<REGION>` bucket (e.g. `s3://<DIST_BUCKET_PREFIX>-<REGION>/<SOLUTION_NAME>/<VERSION>/`).
  CLI based S3 command to sync the buckets is:
  ```bash
  aws s3 sync $MAIN_DIRECTORY/deployment/global-s3-assets/ s3://${DIST_BUCKET_PREFIX}-${REGION}/${SOLUTION_NAME}/${VERSION}/
  aws s3 sync $MAIN_DIRECTORY/deployment/regional-s3-assets/ s3://${DIST_BUCKET_PREFIX}-${REGION}/${SOLUTION_NAME}/${VERSION}/
  ```
- Get the link of the `iot-device-simulator.template` uploaded to your Amazon S3 bucket.
- Deploy the IoT Device Simulator solution to your account by launching a new AWS CloudFormation stack using the S3 link of the `iot-device-simulator.template`.

CLI based CloudFormation deployment:

```bash
export INITIAL_USER=name@example.com 
export CF_STACK_NAME=iot

aws cloudformation create-stack \
   --profile ${AWS_PROFILE:-default} \
   --region ${REGION} \
   --template-url https://${DIST_BUCKET_PREFIX}-${REGION}.s3.amazonaws.com/${SOLUTION_NAME}/${VERSION}/iot-device-simulator.template \
   --stack-name ${CF_STACK_NAME} \
   --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND \
   --parameters \
        ParameterKey=UserEmail,ParameterValue=${INITIAL_USER}

```


# Collection of operational metrics
This solution collects anonymous operational metrics to help AWS improve the quality and features of the solution.