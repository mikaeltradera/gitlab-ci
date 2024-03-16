
# Advanced Scripting in GitLab CI/CD

This document provides detailed examples of advanced scripting techniques in GitLab CI/CD pipelines to automate and enhance your DevOps workflow, along with explanations for each example.

## 4. Advanced Scripting in GitLab CI

### 4.1 Utilizing Script Blocks for Complex Operations

Script blocks allow for the execution of complex operations within CI jobs. They can execute multiple commands, use conditional logic, and perform operations based on environment variables.

#### Example 1: Conditional Script Execution

```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - |
      if [ "$CI_COMMIT_BRANCH" == "main" ]; then
        echo "This is a build step for the main branch";
        # Add main branch-specific build commands here
      else
        echo "This is a build step for a non-main branch";
        # Add other branch-specific build commands here
      fi
```

**Explanation:** This example demonstrates using conditional logic within a script block to execute different commands based on the branch being built. This is useful for projects that require different build steps for different branches.

#### Example 2: Dynamically Creating Artifact Names

```yaml
build_artifacts:
  stage: build
  script:
    - export ARTIFACT_NAME="build_${CI_COMMIT_REF_NAME}_${CI_PIPELINE_ID}.zip"
    - zip -r ${ARTIFACT_NAME} .
  artifacts:
    paths:
      - ${ARTIFACT_NAME}
```

**Explanation:** This script dynamically generates the name of the build artifact using environment variables provided by GitLab CI. This allows for unique artifact names based on the branch name and pipeline ID, which helps in identifying and organizing artifacts.

### 4.2 Security Best Practices in Scripts

Ensuring the security of your CI/CD pipelines is crucial. This involves avoiding the hardcoding of sensitive data and using environment variables to pass secrets.

#### Example: Integrating Slack Notifications

```yaml
notify_slack:
  stage: notify
  image: curlimages/curl:latest
  script:
    - >
      curl -X POST -H 'Content-type: application/json'
      --data '{"text":"Deployment completed successfully"}'
      $SLACK_WEBHOOK_URL
  only:
    - main
```

**Explanation:** This example uses the `curl` command within a Docker container to send a notification to a Slack channel after a successful deployment. It demonstrates how to integrate third-party services into your CI/CD pipeline while keeping sensitive information like the Slack webhook URL secure by using environment variables.

### 4.3 Integrating Third-Party Tools

Integrating third-party tools into your CI/CD pipeline can enhance functionality, such as code quality checks, monitoring, or notifications.

#### Example: Dynamic Environment Deployment

```yaml
deploy:
  stage: deploy
  script:
    - |
      if [ "$CI_COMMIT_BRANCH" == "main" ]; then
        echo "Deploying to production environment";
        deploy_production.sh
      elif [ "$CI_COMMIT_BRANCH" == "develop" ]; then
        echo "Deploying to staging environment";
        deploy_staging.sh
      else
        echo "Deploying to testing environment";
        deploy_testing.sh
      fi
```

**Explanation:** This script deploys your application to different environments based on the branch. This is an example of how to structure a deployment job to handle multiple environments within the same pipeline.

#### Example: Automated Version Bumping and Tagging

```yaml
bump_version_and_tag:
  stage: release
  only:
    - main
  script:
    - |
      VERSION=`python version_bump.py`
      git config --global user.email "ci@example.com"
      git config --global user.name "CI"
      git tag $VERSION
      git push origin $VERSION
```

**Explanation:** In this example, a Python script is used to automatically bump the project's version. Afterward, the CI pipeline commits the new version and tags the commit. This automates the versioning and release process, reducing manual errors and streamlining releases.

#### Example: Using Docker for Dependency Management

```yaml
image_quality_check:
  stage: test
  image: docker:19.03.12
  services:
    - docker:19.03.12-dind
  script:
    - docker run --rm -v $(pwd):/app some/docker-image-quality-check tool --option /app
```

**Explanation:** This job utilizes Docker to run a quality check tool against your codebase without the need to install the tool on the runner. This is an example of how to use Docker for dependency management and to ensure a clean, reproducible environment for CI jobs.

#### Example: Multi-Stage Docker Build and Push

```yaml
build_and_push:
  stage: build
  image: docker:19.03.12
  services:
    - docker:19.03.12-dind
  script:
    - echo $CI_JOB_TOKEN | docker login -u $CI_REGISTRY_USER --password-stdin $CI_REGISTRY
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
```

**Explanation:** This example demonstrates a CI job that builds a Docker image from the project and pushes it to GitLab's container registry. This is a common pattern for building and distributing Docker images as part of a CI/CD pipeline.
