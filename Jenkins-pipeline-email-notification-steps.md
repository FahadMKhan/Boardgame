### Steps for Our Pipeline to Receive Email Notifications

Jenkins pipeline script with the necessary steps to configure the email notification correctly. 
**Note that the `post` section is added immediately after the `pipeline` declaration, as required.**

```groovy
pipeline {
    agent any
    stages {
        stage('Deploy') {
            steps {
                echo 'Deploying the application...'
                // Add your deployment steps here, e.g., deploy to a server
                // sh 'deploy-script.sh'
            }
        }
    }
**#DO NOT COPY THE ABOVE**
**#Copy the below pipeline declaration and paste it in your Jenkins pipeline to configure the email notification correctly.**                         
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

1. **Post Actions:**
   - **Always Block:** Ensures the email notification script executes regardless of the build result.
   - **Script Block:** Gathers environment variables, determines the pipeline status, sets the banner color, and constructs the HTML body for the email.
   - **Email Notification (`emailext`):** Configures the email to be sent with the build details, HTML body, and attaches the specified report file.

This complete pipeline script is ready to use in Jenkins and covers the typical stages of a CI/CD pipeline, including sending a detailed email notification after each build.
