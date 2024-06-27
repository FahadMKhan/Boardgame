### Steps for Our Pipeline to Receive Email Notifications

Jenkins pipeline script with the necessary steps to configure the email notification correctly. 
**Ensure the `post` section starts at the same indentation level as the `pipeline` declaration to correctly configure the email notifications.**
                      
```groovy
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
```

### Explanation:

1. **Post Actions:**
   - **Always Block:** Ensures the email notification script executes regardless of the build result.
   - **Script Block:** Gathers environment variables, determines the pipeline status, sets the banner color, and constructs the HTML body for the email.
   - **Email Notification (`emailext`):** Configures the email to be sent with the build details, HTML body, and attaches the specified report file.
