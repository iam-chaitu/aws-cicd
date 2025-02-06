# Node.js AWS CI/CD Project

This is a simple Node.js application deployed using AWS CI/CD services, including AWS CodePipeline, CodeBuild, and Elastic Beanstalk. The application is containerized using Docker and pushed to Amazon Elastic Container Registry (ECR).

## Table of Contents

- [Project Overview](#project-overview)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Running the Application Locally](#running-the-application-locally)
- [Testing](#testing)
- [AWS CI/CD Pipeline](#aws-cicd-pipeline)
- [Deployment](#deployment)

## Project Overview

This project demonstrates how to set up a CI/CD pipeline for a Node.js application using AWS services. The application is a simple Express server that responds with "Hello World!" when accessed. The pipeline automates the build, test, and deployment process using AWS CodePipeline, CodeBuild, and ECR.

## Prerequisites

Before you begin, ensure you have the following installed:

- [Node.js](https://nodejs.org/) (v14 or higher)
- [npm](https://www.npmjs.com/) (usually comes with Node.js)
- [Docker](https://www.docker.com/) (for containerization)
- [Git](https://git-scm.com/)
- An AWS account with access to CodePipeline, CodeBuild, and ECR

## Setup

1. **Clone the repository:**

   ```bash
   git clone https://github.com/your-username/your-repo-name.git
   cd your-repo-name
   ```

2. **Install dependencies:**

   ```bash
   npm install
   ```

3. **Build the Docker image:**

   ```bash
   docker build -t my-node-app .
   ```

4. **Run the Docker container:**

   ```bash
   docker run -p 3000:3000 my-node-app
   ```

   The application will be available at `http://localhost:3000`.

## Running the Application Locally

To run the application locally without Docker, use the following command:

```bash
npm start
```

The application will be available at `http://localhost:3000`.

## Testing

To run the tests, use the following command:

```bash
npm test
```

This will execute the Mocha tests located in the `tests/` directory.

## AWS CI/CD Pipeline

This project uses AWS CodePipeline, CodeBuild, and Elastic Beanstalk to automate the build, test, and deployment process.

### Pipeline Steps

1. **Source**: The pipeline is triggered by changes to the `main` branch of the GitHub repository.
2. **Build**: AWS CodeBuild runs the build and test commands specified in the `buildspec.yml` file.
3. **Deploy**: The application is deployed to an Elastic Beanstalk environment.

### `buildspec.yml`

The `buildspec.yml` file contains the build and test commands:

```yaml
version: 0.2

phases:
  install:
    commands:
      - echo Installing dependencies...
      - npm install

  pre_build:
    commands:
      - echo Logging into AWS ECR...
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com
      - docker rmi -f $ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/nodeapp:latest || true
  build:
    commands:
      - echo Building the Docker image...
      - docker build -t $ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/nodeapp:latest .

  post_build:
    commands:
      - echo Pushing Docker image to ECR...
      - docker push $ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/nodeapp:latest
      - echo Build completed on `date`
```
