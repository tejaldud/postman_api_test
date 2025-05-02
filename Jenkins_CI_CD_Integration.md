# Integrating Postman with Jenkins CI/CD Pipeline

This guide explains how to integrate the Postman collection with Jenkins for automated API testing within a CI/CD pipeline.

## Prerequisites

- Jenkins server up and running
- Node.js installed on the Jenkins server
- Newman (Postman's command-line runner) installed globally
- Git repository with your Postman collection files

## Steps to Integrate Postman with Jenkins

### 1. Install Required Jenkins Plugins

- Log in to your Jenkins dashboard
- Go to "Manage Jenkins" > "Manage Plugins"
- Install the following plugins:
  - NodeJS Plugin
  - HTML Publisher Plugin
  - Git Integration Plugin

### 2. Configure NodeJS in Jenkins

- Go to "Manage Jenkins" > "Global Tool Configuration"
- Scroll to the NodeJS section and click "Add NodeJS"
- Name it (e.g., "Node 16")
- Select the installation option (usually "Install from nodejs.org")
- Select the version (recommended: LTS version)
- Save the configuration

### 3. Prepare Your Repository

- Create a new Git repository or use an existing one
- Add your Postman collection files:
  - `API_Test_Collection.json`
  - `Technical_Interview_Environment.json`
- Create a `package.json` file with the following content:

```json
{
  "name": "postman-api-tests",
  "version": "1.0.0",
  "description": "API tests using Postman and Newman",
  "scripts": {
    "test": "newman run API_Test_Collection.json -e API_Test_Environmentjson -r cli,htmlextra --reporter-htmlextra-export newman-results/report.html"
  },
  "devDependencies": {
    "newman": "^5.3.2",
    "newman-reporter-htmlextra": "^1.22.11"
  }
}
```

### 4. Create a Jenkinsfile

- Add a `Jenkinsfile` to your repository with the following content:

```groovy
pipeline {
    agent any

    tools {
        nodejs 'Node 16'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run API Tests') {
            steps {
                sh 'mkdir -p newman-results'
                sh 'npm test'
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'newman-results',
                        reportFiles: 'report.html',
                        reportName: 'API Test Report'
                    ])
                }
            }
        }
    }

    post {
        success {
            echo 'API Tests passed! Pipeline can continue to deployment.'
        }
        failure {
            echo 'API Tests failed! Stopping deployment pipeline.'
        }
    }
}
```

### 5. Create a Jenkins Pipeline Job

1. On your Jenkins dashboard, click "New Item"
2. Enter a name (e.g., "Postman-API-Tests")
3. Select "Pipeline" and click "OK"
4. In the configuration page:
   - Under "Pipeline", select "Pipeline script from SCM"
   - Select "Git" as the SCM
   - Enter your repository URL
   - Specify the branch (e.g., "\*/main")
   - Keep the "Script Path" as "Jenkinsfile"
5. Click "Save"

### 6. Configure Webhooks (Optional)

For automatic trigger when changes are pushed:

1. Go to your Git repository settings
2. Add a webhook pointing to your Jenkins server:
   - URL: `http://your-jenkins-server/github-webhook/`
   - Content type: `application/json`
   - Select "Just the push event"

### 7. Running the Pipeline

- Manually: Click "Build Now" on your Jenkins pipeline job
- Automatically: Push changes to your repository (if webhook is configured)

## Ensuring Tests Run During the Build Process

1. **Integration with Build Stages**: The Jenkinsfile is configured to run API tests as part of the build process, making them a required step before deployment.

2. **Failure Handling**: If API tests fail, the Jenkins pipeline will mark the build as failed and prevent further deployment stages.

3. **Reporting**: The HTML Publisher plugin publishes test reports so you can easily view the results of each run.

4. **Notifications**: Configure Jenkins to send notifications (email, Slack) when tests fail so the team can respond quickly.

## Handling Environment Variables

For security and flexibility across environments:

1. Create environment-specific files (dev, staging, production)
2. Update the npm test script to accept an environment parameter:

```json
"scripts": {
  "test": "newman run API_Test_Collection.json -e Technical_Interview_Environment_${ENV:-dev}.json -r cli,htmlextra --reporter-htmlextra-export newman-results/report.html"
}
```

3. In Jenkinsfile, set the environment variable:

```groovy
environment {
    ENV = 'staging'  // or get from Jenkins parameters
}

// Then in the Run API Tests stage:
sh 'ENV=${ENV} npm test'
```

## Benefits of this Integration

1. **Automated Testing**: API tests run automatically with every build, ensuring continuous quality validation
2. **Early Issue Detection**: Problems with APIs are caught early in the development cycle
3. **Consistent Execution**: Tests run in the same way every time, eliminating human error
4. **Historical Reports**: Test results are archived for each build, allowing trend analysis
5. **Gated Deployments**: Prevent broken code from reaching production by making successful API tests a requirement for deployment
