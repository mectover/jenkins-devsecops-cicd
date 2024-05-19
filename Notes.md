https://thoughtworks.github.io/talisman/docs/installation/single-repository/



KUBE BENCH CIS :  https://github.com/aquasecurity/kube-bench/releases

https://github.com/aquasecurity/kube-bench/blob/main/docs/installation.md


Create SonarQube Webhook:

Login into the SonarQube UI and click on the Administration on the top bar.

Under Configuration click on Webhooks.

Click on Create button and enter a name for the webhook, something like jenkins

In the URL box enter the URL of the Jenkins server like this <jenkins-url>/sonarqube-webhook/


Install Sonar Quality Gates Plugin in Jenkins:

Login into the Jenkinsserver and go to the Manage Jenkins.

Click on Manage Plugins then go to the Available tab.

Search for Sonar Quality Gates and install the required plugin.


Add SonarQube Environment and Secret in Jenkins:

Login into the Jenkinsserver and go to the Manage Jenkins.

Click on Manage Credentials then (global) then Add Credentials.

Select Secret text in kind and enter the token you created earlier under Secret. Finally click on OK to save your changes.

Now go to Configure System under Manage Jenkins.

Search for SonarQube servers.

Enter a name for the server like sonarqube

Under Server URL enter the SonarQube server URL.

Under Server authentication token select the secret you added above.

Finally click on Save


Modify the SonarQube stage:
    stage('SonarQube - SAST') {
      steps {
        withSonarQubeEnv('sonarqube') {
          sh "mvn sonar:sonar \
              -Dsonar.projectKey=devsecops-numeric-application \
              -Dsonar.host.url=<sonarqube-server-url>"
      }
        timeout(time: 2, unit: 'MINUTES') {
          script {
            waitForQualityGate abortPipeline: true
          }
        }
      }
    }



Push your changes:


cd kubernetes-devops-security/
git add .
git commit -m "<your commit message>"
git push origin master



Wait for the Jenkins job to complete before hitting the check button.









cd /root/
wget https://github.com/controlplaneio/kubesec/releases/download/v2.11.2/kubesec_linux_amd64.tar.gz
tar -xvf  kubesec_linux_amd64.tar.gz
mv kubesec /usr/bin/