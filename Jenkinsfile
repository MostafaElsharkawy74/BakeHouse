pipeline {
    agent {label "first-slave"}
    parameters {
    choice(name: 'ENV_ITI', choices: ['dev','test','prod','release'])
    }

    stages {
        stage('Build') {
            steps {
                script{ 
                    echo 'Build'
                    if (params.ENV_ITI=="release"){
                        withCredentials([usernamePassword(credentialsId: 'mostafa-cred', usernameVariable:'username' ,passwordVariable:'password')]) {    // Use the file within the block    echo "File path: ${MY_FILE}"
                        sh '''
                        docker login -u ${username} -p ${password}
                        docker build -t mostafaelsharkawy74/tt:v${BUILD_NUMBER} .
                        docker push mostafaelsharkawy74/tt:v${BUILD_NUMBER}
                        echo ${BUILD_NUMBER} > ../build_num.txt
                        echo ${ENV_ITI}
                            '''
                        }
                    }
                    else{
                        echo "user chose ${params.ENV_ITI}"
                    }
            }
        }
        }
      
        stage('Deployment') {
            steps {
                echo 'Deployment'
                script{
                    if (params.ENV_ITI== "dev" || params.ENV_ITI== "prod" || params.ENV_ITI== "test"){
                        withCredentials([file(credentialsId: 'secret_file_test', variable: 'test')]) {
                        sh '''
                           export BUILD_NUMBER=$(cat ../build_num.txt)
                           mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                           cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                           rm -rf Deployment/deploy.yaml.tmp
                           kubectl apply -f Deployment/service.yaml --kubeconfig ${test} -n ${ENV_ITI}
                           kubectl apply -f Deployment/deploy.yaml --kubeconfig ${test} -n ${ENV_ITI}
                           
                            '''
                             }
                    
                    }
                    else{
                         echo "user chose ${params.ENV_ITI}"
                    }
                    
                }
                
        }
        }       
    }
    
}
