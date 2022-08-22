---
slug: gitops-java-applications
id: bqdduvi3xvrx
type: challenge
title: Using GitOps to Deploy Java Application
teaser: Using GitOps to deploy the Java Application.
notes:
- type: text
  contents: |-
    ## Objectives
    As part of the earlier challenge we,
    - Built Java Applications using Drone CI
    - Pushed the Java Application Container Image to our Private registry
    In this track, this is what you'll learn:
    - How to Continuously Deploy the Java Application using Argo CD by applying GitOps principles
    - What are are the strategies to do rolling updates using Argo CD
tabs:
- title: Terminal 1
  type: terminal
  hostname: kubernetes-vm
- title: Gitea
  type: website
  path: /
  url: http://kubernetes-vm.${_SANDBOX_ID}.instruqt.io:30950
  new_window: true
- title: Argo CD
  type: website
  path: /
  url: http://kubernetes-vm.${_SANDBOX_ID}.instruqt.io:30080
  new_window: true
- title: Drone
  type: website
  path: /
  url: http://kubernetes-vm.${_SANDBOX_ID}.instruqt.io:30980
  new_window: true
- title: Terminal 2
  type: terminal
  hostname: kubernetes-vm
- title: Nexus Repository Manager
  type: service
  hostname: kubernetes-vm
  port: 30081
- title: Java App
  type: code
  hostname: kubernetes-vm
  path: /root/repos/quarkus-springboot-demo
- title: Java App GitOps
  type: code
  hostname: kubernetes-vm
  path: /root/repos/quarkus-springboot-demo-gitops
difficulty: basic
timelimit: 600
---

👋 Introduction
===============

GitOps is a way of implementing Continuous Deployment for cloud native applications. It focuses on a developer-centric experience when operating infrastructure, by using tools developers are already familiar with, including Git and Continuous Deployment tools.

The core idea of GitOps is having a Git repository that always contains declarative descriptions of the infrastructure currently desired in the production environment and an automated process to make the production environment match the described state in the repository. If you want to deploy a new application or update an existing one, you only need to update the repository - the automated process handles everything else. It’s like having cruise control for managing your applications in production.

> Source: <https://www.gitops.tech/>

Clone Repository
----------------

Navigate to the git repositories home directory i.e. `$GIT_REPOS_HOME` using **Terminal 2** tab,

```shell
cd $GIT_REPOS_HOME
```

Clone the `quarkus-springboot-demo-gitops` repository and navigate to the same,

```shell
git clone http://kubernetes-vm.${_SANDBOX_ID}.instruqt.io:30950/${QSBD_GITOPS_GIT_REPO}
```

Navigate to the cloned repo directory

```shell
cd quarkus-springboot-demo-gitops
```

Enable the environment variables,

```shell
direnv allow .
```

Check out the `${QSBD_GITOPS_GIT_REV}` of the application,

```shell
git checkout -b ${QSBD_GITOPS_GIT_REV} origin/instruqt
```

GitOps
------

Update the App helm `$APP_HOME/helm_vars/values.yaml`

```shell
envsubst < "$APP_HOME/helm_vars/values.tpl.yaml" > "$APP_HOME/helm_vars/values.yaml"
```

Commit and push the code to git repo,

```shell
git commit -a -m "Init GitOps"
git push origin ${QSBD_GITOPS_GIT_REV}
```

Deploy Argo CD application Kubernetes cluster that will use our GitOps repo,

```shell
envsubst < $APP_HOME/app.yaml | kubectl apply -f -
```

Open the **Argo CD** tab to watch the application getting synchronized and deployed on the Kubernetes cluster.

Verify Application Deployment
-----------------------------


Updating Application
--------------------

Edit the application source `${QSBD_GIT_REPO}/src/main/java/com/example/hellospringboot/GreeterController.java` using the tab **Java App** and update the message from **"Hello from Captain Canary!!\uD83D\uDC25🚀";** to be like **"Captain Canary rocks with GitOps!!!\uD83D\uDC25🚀";**.

On **Terminal 2** navigate to `${QSBD_GIT_REPO}`,

```shell
cd "${GIT_REPOS_HOME}/quarkus-springboot-demo"
```

Commit and push the code to the `${QSBD_GIT_REPO}`,

```shell
git commit -m  "Lets do GitOps" "src/main/java/com/example/hellospringboot/GreeterController.java"
git push origin ${QSBD_GITOPS_GIT_REV}
```

The action above should trigger a drone build as we saw in the earlier challenge. Once the build is successful you should see the updated image getting pushed to the registry.

Click open the `quarkus-springboot-demo` application on the Argo CD dashboard and watch the deploy, in few seconds it should start to sync with the latest image change i.e. the image that was pushed to the image repository earlier.  It if you verify the `values.yaml` file in the `${QSBD_GITOPS_GIT_REPO}`, it should now been updated to latest image revision.

> **TIP**: Image revisions can be verified using their sha256 digests

Congratulations. You now successfully applied GitOps(Continuous Deployment) using Argo CD and integrated it with Drone CI to support with Continuous Integration.

🏁 Finish
=========

To complete this challenge, press **Check**.
