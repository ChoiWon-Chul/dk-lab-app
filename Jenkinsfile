pipeline {
  agent any
  options { disableConcurrentBuilds(); timestamps(); skipDefaultCheckout(true) }
  environment {
    REGISTRY_PUSH = 'dk-lab-registry:5000'
    IMAGE_NAME = 'dk-first-web'
    GITOPS_REPO = 'https://github.com/ChoiWon-Chul/dk-lab-gitops.git'
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Image Tag') {
      steps {
        script {
          env.IMAGE_TAG = sh(script: 'git rev-parse --short=12 HEAD', returnStdout: true).trim()
        }
        echo '소스 커밋의 앞 12자리를 이미지 태그로 사용합니다.'
      }
    }
    stage('Build') {
      steps {
        sh '''
          set -eu
          docker build -t "${REGISTRY_PUSH}/${IMAGE_NAME}:${IMAGE_TAG}" ./app
        '''
      }
    }
    stage('Smoke Test') {
      steps {
        sh '''
          set -eu
          docker run --rm "${REGISTRY_PUSH}/${IMAGE_NAME}:${IMAGE_TAG}" nginx -t
        '''
      }
    }
    stage('Push Image') {
      steps {
        sh '''
          set -eu
          docker push "${REGISTRY_PUSH}/${IMAGE_NAME}:${IMAGE_TAG}"
        '''
      }
    }
    stage('Update GitOps') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'github-gitops', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
          sh '''
            set -eu
            set +x
            test "$GITOPS_REPO" = "https://github.com/ChoiWon-Chul/dk-lab-gitops.git" || {
              echo '[FAIL] Unexpected GitOps repository URL' >&2
              exit 1
            }

            ASKPASS="$WORKSPACE/.dk-git-askpass.sh"
            cat > "$ASKPASS" <<'ASKPASS_EOF'
#!/bin/sh
case "$1" in
  *Username*) printf '%s\n' "$GIT_USER" ;;
  *Password*) printf '%s\n' "$GIT_TOKEN" ;;
esac
ASKPASS_EOF
            chmod 700 "$ASKPASS"
            trap 'rm -f "$ASKPASS"' EXIT
            export GIT_ASKPASS="$ASKPASS"
            export GIT_TERMINAL_PROMPT=0

            rm -rf gitops-work
            git clone --depth=1 --branch main "$GITOPS_REPO" gitops-work
            cd gitops-work
            sed -i "s#image: localhost:5001/dk-first-web:.*#image: localhost:5001/dk-first-web:${IMAGE_TAG}#" k8s/deployment.yaml
            grep -F "image: localhost:5001/dk-first-web:${IMAGE_TAG}" k8s/deployment.yaml >/dev/null
            git config user.name 'dk-lab-jenkins'
            git config user.email 'ci-bot@example.invalid'
            git add k8s/deployment.yaml
            if ! git diff --cached --quiet; then
              git commit -m "deploy: ${IMAGE_TAG}"
              git push origin HEAD:main
            else
              echo 'GitOps image tag unchanged'
            fi
          '''
        }
      }
    }
  }
  post {
    success {
      echo 'Jenkins CI complete. Verify GitOps sync and Kubernetes Pods in Argo CD.'
    }
    failure {
      echo 'Inspect the failed stage in Console Output. Never share credentials or tokens.'
    }
  }
}
