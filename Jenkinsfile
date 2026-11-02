// Build the Grafana image from this repo and push it to staging ECR only.
// The image is whatever grafana/dashboards contains on the built commit
// (Homepage, Engineering Overview, DORA, Jenkins, Jira, GitHub, Deployments,
// Issue Releases).
//
// gotickets/maverick-admin was not readable from the environment that added
// this file, so the staging conventions are the ones that repo's deploy uses
// in k8s-stage:
//   agent    java-server-builds  (ubuntu-java-docker-builder AMI)
//   auth     the agent instance profile; no credentials in this file
//   registry 319908685555.dkr.ecr.us-east-1.amazonaws.com  (stage account)
//   repo     devlake-dashboards  (git repo name, same as maverick-admin)
// There is no production registry and no production stage.

pipeline {
  agent { label 'java-server-builds' }

  options {
    disableConcurrentBuilds()
    timestamps()
  }

  environment {
    AWS_REGION = 'us-east-1'
    AWS_DEFAULT_REGION = 'us-east-1'
    ECR_REGISTRY = '319908685555.dkr.ecr.us-east-1.amazonaws.com'
    ECR_REPO = 'devlake-dashboards'
  }

  stages {
    stage('Build image') {
      steps {
        sh '''
          set -eu
          test -f grafana/Dockerfile
          IMAGE_TAG="$(git rev-parse HEAD)"
          docker build \
            --file grafana/Dockerfile \
            --tag "${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}" \
            grafana
        '''
      }
    }

    stage('Push to ECR staging') {
      when {
        anyOf {
          branch 'main'
          branch 'cursor/trim-dashboards-a1d1'
        }
      }
      steps {
        sh '''
          set -eu
          IMAGE_TAG="$(git rev-parse HEAD)"
          BRANCH="${BRANCH_NAME:-}"
          if [ -z "${BRANCH}" ]; then
            BRANCH="${GIT_BRANCH#origin/}"
          fi

          aws ecr describe-repositories \
            --repository-names "${ECR_REPO}" \
            --region "${AWS_REGION}" \
            || aws ecr create-repository \
              --repository-name "${ECR_REPO}" \
              --region "${AWS_REGION}"

          aws ecr get-login-password --region "${AWS_REGION}" \
            | docker login --username AWS --password-stdin "${ECR_REGISTRY}"

          docker push "${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}"

          if [ "${BRANCH}" = "main" ]; then
            docker tag \
              "${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}" \
              "${ECR_REGISTRY}/${ECR_REPO}:staging"
            docker push "${ECR_REGISTRY}/${ECR_REPO}:staging"
          fi
        '''
      }
    }
  }
}
