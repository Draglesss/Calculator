pipeline {
    agent any
    stages {
        stage("Compilation") {
            steps {
                sh "./gradlew compileJava"
            }
        }
        stage("test unitaire") {
            steps {
                sh "./gradlew test"
            }
        }
        stage("Code coverage") {
            steps {
                sh "./gradlew jacocoTestReport"
                publishHTML(target: [
                    reportDir: 'build/reports/jacoco/test/html',
                    reportFiles: 'index.html',
                    reportName: "JaCoCo Report"
                ])
                sh "./gradlew jacocoTestCoverageVerification"
            }
        }
	stage("Analyse statistique du code") {
		steps {
			sh "./gradlew checkstyleMain"
			publishHTML (target: [
 				reportDir: 'build/reports/checkstyle/',
 				reportFiles: 'main.html',
 				reportName: "Checkstyle Report"
				])
		}
	}
	stage("Package") {
		steps {
			sh "./gradlew build"

	}
		}
	stage("Docker build") {
		steps {
		 	sh "docker build -t calculator ."

	 	}
	}
	stage("Docker push") {
		steps {
			sh "docker push localhost:5000/calculator:latest"
		}
	}
        stage("Déploiement sur staging") {
            steps {
                sh "docker run -d --rm -p 8889:8080 --name jenkins_feature_calculator localhost:5000/calculator"
            }
        }
        stage("Test d'acceptation") {
            steps {
                sleep 5
                sh "chmod +x acceptance_test.sh && ./acceptance_test.sh"
            }
        	}
	}
	post {
		always {
			mail to: 'otmanefatteh@hotmail.com',
			subject: "Cher lion Votre compilation est terminée: ${currentBuild.fullDisplayName}",
			body: " Votre build est accompli, Veuilez vérifier: ${env.BUILD_URL}"
		}
		success{
			sh "docker stop jenkins_calculator"
		}
	}
	
}

