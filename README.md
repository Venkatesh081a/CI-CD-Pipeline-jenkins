1. Setup Jenkins
a) Install Jenkins
You can install Jenkins either on a virtual machine or use a cloud-based Jenkins service.

sudo yum upgrade
sudo yum install java-17-amazon-corretto -y
java -version
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status Jenkins

b) Install Python and Necessary Libraries
Jenkins needs Python installed, along with any necessary libraries for running your Python application. Here's how to configure it:

2) Install Python on the Jenkins server (if not already installed):
   sudo apt install python3 python3-pip
Install Jenkins Python plugin:
Go to Jenkins dashboard → Manage Jenkins → Manage Plugins.
Search for the "Python" plugin and install it.
Once installed, Jenkins can use the python and pip commands in the pipeline scripts.
3) Install Python Dependencies for the project: Make sure you have a requirements.txt in the root of your Python project to specify all the dependencies for your Python application.
4) Fork and Clone the Repository :
    Fork the repository to your GitHub account and clone it to your Jenkins server:
     # Clone your forked repository
       git clone https://github.com/YOUR_USERNAME/flask.git

 5) Jenkins Pipeline Setup :
      a) Create a Jenkinsfile in the root of your repository
          The Jenkinsfile is a text file that defines your pipeline. It will include the stages such as building the environment, testing, and deploying the application.
           pipeline {
    agent any

    environment {
        PYTHON = 'python3'
        PIP = 'pip3'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    // Install dependencies
                    echo 'Installing dependencies...'
                    sh """
                    ${PIP} install -r requirements.txt
                    """
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run tests using pytest
                    echo 'Running tests...'
                    sh """
                    ${PYTHON} -m pytest
                    """
                }
            }
        }

        stage('Deploy') {
            when {
                // Only deploy if tests pass
                branch 'main'
            }
            steps {
                script {
                    echo 'Deploying to staging environment...'
                    sh """
                    # Example: Deploy with Flask
                    ${PYTHON} api.py &
                    """
                }
            }
        }
    }

   post {
    always {
        echo 'Cleaning up...'
    }

    success {
        echo 'Build and test successful! Deploying to staging.'
        mail to: 'vullagantivenkatesh@gmail.com@example.com',
             subject: "Build Successful: ${currentBuild.fullDisplayName}",
             body: "The build was successful and deployed to staging."
    }

    failure {
        echo 'Build or tests failed. Not deploying.'
        mail to: 'vullagantivenkatesh@gmail.com@example.com',
             subject: "Build Failed: ${currentBuild.fullDisplayName}",
             body: "The build or tests failed. Please check the logs."
    }
}

}

Explanation of the Pipeline Stages:
  Build: Installs dependencies from requirements.txt using pip.
  Test: Runs the unit tests using pytest.
  Deploy: If the tests pass, deploys the app to a staging environment (you can replace this part with your own deployment script).
  Post-actions: Sends notifications based on build results.



