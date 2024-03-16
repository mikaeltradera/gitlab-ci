
# Section 5: Practical Examples and Templates

In GitLab CI/CD, the `.gitlab-ci.yml` file is central to your CI/CD pipeline. It defines how your project is built, tested, and deployed. Here, we'll present some practical examples and templates to enhance your understanding and help you craft effective pipelines.

## 5.1 Basic .gitlab-ci.yml Structure

Start with the basic structure of a `.gitlab-ci.yml` file:

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project..."
    - build_command

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - test_command

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - deploy_command
```

This template outlines three primary stages: `build`, `test`, and `deploy`, each containing distinct jobs with specific scripts.

## 5.2 Building a Docker Image

Using Docker? Configure your `.gitlab-ci.yml` to build images:

```yaml
stages:
  - build

build_docker:
  stage: build
  image: docker:19.03.12
  services:
    - docker:19.03.12-dind
  script:
    - docker login -u $DOCKER_USER -p $DOCKER_PASSWORD
    - docker build -t my-image:$CI_COMMIT_SHA .
    - docker push my-image:$CI_COMMIT_SHA
  only:
    - master
```

This setup builds and pushes a Docker image using Docker-in-Docker service, tagging the image with the commit SHA for immutability.

## 5.3 Running Tests in Parallel

Accelerate your testing phase by running tests in parallel:

```yaml
stages:
  - test

variables:
  POSTGRES_DB: test_db
  POSTGRES_USER: user
  POSTGRES_PASSWORD: password

test:
  stage: test
  image: ruby:2.7
  services:
    - postgres:latest
  parallel:
    matrix:
      - TEST_SUITE: "unit"
      - TEST_SUITE: "integration"
      - TEST_SUITE: "functional"
  script:
    - setup_test_environment
    - run_tests $TEST_SUITE
```

In this configuration, three parallel jobs are created, each running different test suites.

## 5.4 Deploying to a Staging Environment

Deploy to a staging environment with the following configuration:

```yaml
stages:
  - test
  - deploy

test_job:
  stage: test
  script:
    - run_tests

deploy_staging:
  stage: deploy
  script:
    - deploy_to_staging
  only:
    - develop
```

Here, `deploy_staging` runs post-testing and only for the `develop` branch.

## 5.5 Multi-environment Deployment

Manage multi-environment deployments effectively:

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - build_command

test_job:
  stage: test
  script:
    - test_command

deploy_to_dev:
  stage: deploy
  script:
    - deploy_to_dev
  environment:
    name: dev
    url: http://dev.example.com
  only:
    - develop

deploy_to_prod:
  stage: deploy
  script:
    - deploy_to_prod
  environment:
    name: production
    url: http://example.com
  when: manual
  only:
    - master
```

This setup incorporates jobs for development and production environments, with production deployment requiring manual approval.

---

Feel free to adapt these templates to your project's needs. Happy coding!
