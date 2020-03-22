## Jenkins in docker to build android apps.

A tested variant is [image based on jenkins:lts](https://github.com/mgolokhov/dockerized_jenkins_for_android/tree/master/jenkins_lts)

[Jenkinsfile (pipeline)](https://github.com/mgolokhov/dockerized_jenkins_for_android/blob/master/jenkinsfiles/Jenkinsfile) is intended for [dodroid project](https://github.com/mgolokhov/dodroid).

Prepare image run `sh ./create.sh`

Start jenkins on `localhost:8080` run `sh ./run.sh`

Default user/password: admin/admin

The first run will take more time, because android build tools and libraries are not preinstalled.

<img src=https://user-images.githubusercontent.com/294512/77258780-e700cf80-6c8d-11ea-821b-f788810158fd.png>
