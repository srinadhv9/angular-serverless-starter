# AngularServerlessStarter


This project demonstrates how to setup a an Angular application and deploy it on AWS via serverless, is based on [this serverless example single palge app example](https://github.com/serverless/examples/tree/master/aws-node-single-page-app-via-cloudfront).

Our goals here are to build deploy and serve and Angular application on AWS with low latency using Serverless framework.

To achieve these goals we use S3 in combination with CloudFront. S3 is used to store our static HTML file while CloudFront is responsible for making it available via Amazon's Content Delivery Network.


## Prerequisite

[Nodejs](https://nodejs.org/en/) (at least version 8)

The `serverless-single-page-app-plugin` in this example requires the Serverless Framework version 1.2.0 or higher and the AWS Command Line Interface. Learn more [here](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) on how to install the AWS Command Line Interface.

## Setup

Replace the bucket name in `serverless.yaml` which you can find inside the `custom` section. There is a placeholder text `angular-serverless-starter`. This is due the fact that bucket names must be globally unique across all AWS S3 buckets. 

Since this plugin uses a custom Serverless plugin you need to setup the `node_modules` by running:

```bash
npm install
```

The `serverless-single-page-app-plugin` plugin in this example is there to simplify the experience using this example. It's not necessary to understand the plugin to deploy your Single Page Application.


## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.




# Build & deploy

## Build 

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `--prod` flag for a production build.

## Deploy

Warning: Whenever you making changes to CloudFront resource in `serverless.yml` the deployment might take a while e.g 20 minutes.

In order to deploy the Single Page Application you need to setup the infrastructure first by running

```bash
serverless deploy
```

The expected result should be similar to:

```bash
Serverless: Packaging service…
Serverless: Uploading CloudFormation file to S3…
Serverless: Uploading service .zip file to S3…
Serverless: Updating Stack…
Serverless: Checking Stack update progress…
...........................
Serverless: Stack update finished…

Service Information
service: serverless-simple-http-endpoint
stage: dev
region: us-east-1
api keys:
  None
endpoints:
  None
functions:
  None
```

After this step your S3 bucket and CloudFront distribution is setup. Now you need to upload your static file e.g. `index.html` and `app.js` to S3. You can do this by running

```bash
serverless syncToS3
```

The expected result should be similar to

```bash
Serverless: upload: app/index.html to s3://yourBucketName123/index.html
Serverless: upload: app/app.js to s3://yourBucketName123/app.js
Serverless: Successfully synced to the S3 bucket
```

Hint: The plugin is simply running the AWS CLI command: `aws S3 sync app/ s3://yourBucketName123/`

Now you just need to figure out the deployed URL. You can use the AWS Console UI or run

```bash
sls domainInfo
```

The expected result should be similar to

```bash
Serverless: Web App Domain: dyj5gf0t6nqke.cloudfront.net
```

Visit the printed domain domain and navigate on the web site. It should automatically redirect you to HTTPS and visiting <yourURL>/about will not result in an error with the status code 404, but rather serves the `index.html` and renders the about page.

This is how it should look like: ![Screenshot](https://cloud.githubusercontent.com/assets/223045/20391786/287cb3acd5-11e6-9eaf-89f641ed9e14.png)

## Re-deploying

If you make changes to your Single Page Application you might need to invalidate CloudFront's cache to make sure new files are served.
Meaning, run:

```bash
serverless syncToS3
```

To sync your files and then:

```bash
serverless invalidateCloudFrontCache
```

# Test

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).


