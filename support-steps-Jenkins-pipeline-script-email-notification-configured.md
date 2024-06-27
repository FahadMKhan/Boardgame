Certainly! Here is the complete Jenkins pipeline script with the necessary stages, build steps, and the email notification configured correctly:

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out the code...'
                // Add your SCM checkout step here, e.g., git checkout
                // git url: 'https://your-repo-url.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                echo 'Building the project...'
                // Add your build steps here, e.g., maven build
                // sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
                // Add your test steps here, e.g., run unit tests
                // sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying the application...'
                // Add your deployment steps here, e.g., deploy to a server
                // sh 'deploy-script.sh'
            }
        }
    }

    post {
        always {
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
                def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'
                def buildUrl = env.BUILD_URL

                def body = """
                    <html>
                        <body>
                            <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                                <h2>${jobName} - Build ${buildNumber}</h2>
                                <div style="background-color: ${bannerColor}; padding: 10px;">
                                    <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                                </div>
                                <p>Check the <a href="${buildUrl}">console output</a></p>
                            </div>
                        </body>
                    </html>
                """

                emailext (
                    to: 'echix@example.com',
                    from: 'jenkins@example.com',
                    replyTo: 'jenkins@example.com',
                    subject: "Build ${buildNumber} - ${jobName} - ${pipelineStatus.toUpperCase()}",
                    body: body,
                    mimeType: 'text/html',
                    attachmentsPattern: 'trivy-report.html'
                )
            }
        }
    }
}
```

### Explanation:

1. **Stages:**
   - **Checkout:** Placeholder for SCM checkout steps.
   - **Build:** Placeholder for build steps, e.g., using Maven.
   - **Test:** Placeholder for testing steps, e.g., running unit tests.
   - **Deploy:** Placeholder for deployment steps.

2. **Post Actions:**
   - **Always Block:** Executes the email notification script regardless of the build result.
   - **Script Block:** Gathers environment variables, determines the pipeline status, sets the banner color, and constructs the HTML body for the email.
   - **Email Notification (`emailext`):** Sends the email with the build details, HTML body, and attaches the specified report file.

This complete pipeline script is ready to use in Jenkins and covers the typical stages of a CI/CD pipeline, including sending a detailed email notification after each build.
