# Exercise-6-CI

## 1. Build the IOT Manager Project in a pipeline

1. fork https://github.com/hsro-inf-mis/play2-iot-manager as a new project
2. create an https://circleci.com/ account with github access to your forked repo
3. add a folder .circleci with a config.yml
4. Use https://circleci.com/docs/2.0/configuration-reference/ for the next steps
5. try to add a job which just prints an echo shell output when you commit something
6. commit your changes and go to https://circleci.com/dashboard and see if the process starts.
7. Try to build a workflow based on the sample file bellow
8. Optional: Try to add a valid kubernetes deployment file for the app. it should expose the docker port 9000 to an ingress on port 80. 

## 2. Reimplement it with Jenkins (optional)

1. Create a docker instance with jenkins and install the blue origin template
2. Try to reimplement the build with a JENKINSFILE

## Template for Workflow in Step 7

```yaml
# Sample
version: 2
jobs:
  test:
    docker:
    - image: innfactory/gcloud-scala-ci:latest
    - image: circleci/postgres:9.6-alpine
      environment:
        POSTGRES_USER: user
        POSTGRES_DB: build
        POSTGRES_PASSWORD: password
    working_directory: ~/repo
    environment:
      JVM_OPTS: -Xmx3200m
      TERM: dumb
      DATABASE_PORT: 5432
      DATABASE_USER: user
      DATABASE_PASSWORD: password
    steps:
    - ## do a checkout here
    - run:
        name: "test with sbt"
        command: |
           (
           for i in `seq 1 10`;
                       do
                         nc -z localhost $DATABASE_PORT && echo Success && exit 0
                         echo -n .
                         sleep 1
                       done
                       echo Failed waiting for Postgres && exit 1
           )
           ## run your test with sbt ciTest
  build:
    docker:
    - image: innfactory/gcloud-scala-ci:latest
    - image: circleci/postgres:9.6-alpine
      environment:
        POSTGRES_USER: user
        POSTGRES_DB: build
        POSTGRES_PASSWORD: password
    working_directory: ~/repo
    environment:
      JVM_OPTS: -Xmx3200m
      TERM: dumb
      DATABASE_PORT: 5432
      DATABASE_USER: user
      DATABASE_PASSWORD: password
    steps:
    - ## checkout
    - ## setup a remote docker env with version: 17.09.0-ce
    - ## build your app with sbt flyway/flywayMigrate and sbt -Dbranch=$CIRCLE_BRANCH -Dversion=$CIRCLE_SHA1 docker:publish
  deploy:
  	# write a deployment file for kubernetes and store it under .circle. Just echo it here, because you have no kubernetes available here. 
workflows:
  version: 2
  # build a workflow - test - build - deploy 
  
  # try to optimize the build with caching.
```



