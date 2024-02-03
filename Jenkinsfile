pipeline {
    agent any

    parameters {
        string(name: 'ARTIFACTORY_URL', defaultValue: 'http://192.168.56.150:8082/artifactory/ansible-code1', description: 'JFrog Artifactory URL')
        string(name: 'ARTIFACTORY_CREDENTIALS', defaultValue: 'admin:cmVmdGtuOjAxOjE3Mzg0NzA5MzM6VHZQdUtGRWhpMm1MY1pKNzdoTjNHM1YyZEQ1', description: 'JFrog Artifactory Credentials')
    }

    stages {
        stage('Zip and Push to JFrog Artifactory') {
            steps {
                script {
                    // Create a zip file containing all files except Jenkinsfile
                    sh 'zip -r ansible-code1.zip * -x Jenkinsfile'
                    
                    // Upload the zip file to JFrog Artifactory
                    sh "curl -u${params.ARTIFACTORY_CREDENTIALS} -T ansible-code1.zip ${params.ARTIFACTORY_URL}"
                }
            }
        }
        
        stage('Download Artifact from Jfrog') {
            agent {
                label 'ansible2'
            }
            steps {
                script {
                    // Download the zip file from JFrog Artifactory
                    sh "curl -u${params.ARTIFACTORY_CREDENTIALS} -O ${params.ARTIFACTORY_URL}/ansible-code1.zip"
                    // Now you can use the downloaded files on the agent
                }
            }
        }
        
        stage('Run playbook') {
            agent {
                label 'ansible2'
            }
            steps {
                script {
                    // Unzip ansible-code1.zip
                    sh 'unzip -o ansible-code1.zip'
                    // Run ansible-playbook from the correct directory
                    dir('ansible-code1') {
                        sh 'ansible-vault decrypt /home/vagrant/inventory.yml --vault-password-file vault_password.txt'
                        sh 'ansible-playbook -i /home/vagrant/ansible-dev/inventory.yml play1.yml'
                    }
                }
            }
        }
    }
}

