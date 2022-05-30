pipeline {
  agent any
  stages {
    stage('Pulling Code') {
      parallel {
        stage('Pulling Code') {
          steps {
            git(url: 'https://github.com/kevinstudy2021/cloud-native.git', branch: '"${BRANCH}"', changelog: true, credentialsId: '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')
          }
        }

        stage('Pull Code By Trigger') {
          steps {
            git(url: 'https://github.com/kevinstudy2021/cloud-native.git', branch: 'env.githubBranch', changelog: true, credentialsId: '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')
            script {
              BRANCH = env.githubBranch
            }

          }
        }

      }
    }

    stage('initConfiguration') {
      steps {
        script {
          println "init var"
          CommitMessage = sh(returnStdout: true, script: "git log -1 --pretty=format:'%h: %an %s'").strm()
          CommitID = sh(returnStdout: true, script: "git log -1 --pretty=format:'%h'").strm()
          def curDate = sh(returnStdout: true, script: "date '+%Y%m%d-%H%M%S'").strm()
          TAG = curlDate[0..14] + "-" + CommitID + "-" + BRANCH
        }

      }
    }

    stage('build and test') {
      parallel {
        stage('build and test') {
          steps {
            sh '''sh """
    echo \'start build...\'
    ${BUILD_COMMAND}
"""'''
          }
        }

        stage('Scan code') {
          steps {
            sh '''sh """
    echo \'code scan\'
    ls
"""'''
          }
        }

      }
    }

    stage('Build docker image') {
      steps {
        sh '''sh """
    docker build -t ${HARBOR_ADDRESS}/${REGISTRY_DIR}/${IMAGE_NAME}:${TAG} .
    docker login -u ${Username} -p ${Password} ${HARBOR_ADDRESS}
    docker push ${REGISTRY_DIR}/${IMAGE_NAME}:${TAG}
"""
'''
      }
    }

    stage('Deploy') {
      steps {
        sh '''sh """
    kubectl config use-context ${CLUSTER} --kubeconfig=${KUBECONFIG_PATH}
    kubectl set image ${DEPLOY_TYPE} -l ${RESOURCE_LABEL} ${IMAGE_NAME}=${REGISTRY_DIR}/${IMAGE_NAME}:${TAG} -n ${NAMESPACE}
"""
'''
      }
    }

  }
  environment {
    CommitMessage = ''
    CommitID = ''
    TAG = ''
  }
}