# The CITAS Protocol 

[![Build Status](https://app.travis-ci.com/Montana/citas-protocol.svg?branch=master)](https://app.travis-ci.com/Montana/citas-protocol)

The **CITAS** Protocol is a Protocol invented by me using Travis CI, it's a lot like SCRUM, but using Travis CI. **CITAS** stands for **Controlling Integrations Through Application Services**. Here's an example of CITAS: 

## Purpose of the CITAS Protocol? 

It helps diagnose a problem with a `.travis.yml` configuration, or maybe a 3rd party configuraiton, like Quay, OpenShift or Docker. It's a simple "trace your tracks" method in diagnosing errors, builds that fail, queues, etc. 

Let's start with your `deploy.sh` file, this will tell Travis where and what to deploy your application to, here's mine - your `deploy.sh` file will be different: 

```bash
#!/bin/bash

eval "$(ssh-agent -s)" # start ssh-agent cache
chmod 600 .travis/id_rsa # allow read access to the private key
ssh-add .travis/id_rsa # add the private key to SSH (Crucial in the CITAS Protocol) 

git config --global push.default matching
git remote add deploy ssh://git@$IP:$PORT$DEPLOY_DIR
git push deploy master

# you can skip this command if you don't need to execute any additional commands after deploying.
ssh apps@$IP -p $PORT <<EOF
  cd $DEPLOY_DIR
  crystal build --release --no-debug index.cr # Change to whatever commands you need!
EOF
```
Okay, so it seems like we now have all of our pieces to start with the CITAS protocol, we know what we are going to deploy with, our `deploy.sh` file, and hopefully a provider to complete the **CITAS** acronym. Here's a flowchart below I've created showing the CITAS protocol method flow:

![Untitled drawio (1)](https://user-images.githubusercontent.com/20936398/142917731-c446cba0-17ba-4215-9201-4fa920616312.png)

An example of this in action would be this `.travis.yml` file I created, in the example we use the CITAS protocol to build an Android application: 

```yaml
language: android
jdk: oraclejdk8

services: 
    - docker 
    
deploy:
  provider: openshift
  user: "YOU USER NAME"
  password: "YOUR PASSWORD" # Can be encrypted (recommended in the CITAS protocol)
  domain: "YOUR OPENSHIFT DOMAIN"

android:
  licenses:
    - 'android-sdk-preview-license-.+'
    - 'android-sdk-license-.+'
    - 'google-gdk-license-.+'
 
  components:
    - tools
    - build-tools-30.0.2
    - tools
    - android-30
    - android-22
    - extra-google-google_play_services
    - extra-google-m2repository
    - extra-android-m2repository
    - sys-img-armeabi-v7a-android-22
 
before_install:
  - chmod +x gradlew
  - yes | sdkmanager "platforms;android-30"

before_script:
  - echo no | android create avd --force -n test -t android-22 --abi armeabi-v7a
  - emulator -avd test -no-audio -no-window &
  - bash android-wait-for-emulator
  - adb shell input keyevent 82 &
  
script:
  - ./gradlew clean build
  - ./gradlew test
  - ./gradlew build check

before_cache:
  - rm -f  $HOME/.gradle/caches/modules-2/modules-2.lock
  - rm -fr $HOME/.gradle/caches/*/plugin-resolution/

cache:
  directories:
  - $HOME/.gradle/caches/
  - $HOME/.gradle/wrapper/
  - $HOME/.android/build-cache
```
This has done exactly what we wanted when it comes to the CITAS protocol. We controlled the integration (using Travis), we used OpenShift as the Application, and Docker as the Service to deploy. Hence the CITAS acronym. 
