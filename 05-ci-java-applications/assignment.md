---
slug: ci-java-applications
id: fjspwgch8lx6
type: challenge
title: Continuously Integrate Java Applications
teaser: Continuously Integrate Java Applications with Drone CI.
notes:
- type: text
  contents: |-
    ## Objectives

    In this track, this is what you'll learn:
    - Build Java Applications using Drone CI
    - Push the Java Application Container Image to our Private registry
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
difficulty: basic
timelimit: 600
---

👋 Introduction
===============

It is always a challenge to build Java applications for cloud i.e building a container image out of Java source code. Though there are lot of tools that exists to do this, but getting them to work with your CI is a challenge.

Let us break that that myth and lean how we can use Drone CI to make the CI task of Java applications to much simpler and easier.

Drone Configuration
===================

Navigate to the git repositories home directory i.e. `$GIT_REPOS_HOME` using **Terminal 1** tab,

```shell
cd $GIT_REPOS_HOME
```

Clone the `quarkus-springboot-demo(QSBD)` repository and navigate to the same,

```shell
git clone http://kubernetes-vm.${_SANDBOX_ID}.instruqt.io:30950/${QSBD_GIT_REPO}
```

Navigate to the cloned repo directory

```shell
cd quarkus-springboot-demo
```

Check out the `${QSBD_GIT_REV}` of the application,

```shell
git checkout -b ${QSBD_GIT_REV} origin/instruqt
```

Using the **Java App** tab edit the `quarkus-springboot-demo` project and  create a file `.envrc.local` with the following contents,

```shell
export DRONE_SERVER="http://kubernetes-vm.${_SANDBOX_ID}.instrqut.io:30980"
export DRONE_TOKEN="drone token from the drone account settings page"
```

> **TIP**: Copy `DRONE_SERVER`  and `DRONE_TOKEN` values from the Drone`user-01` account settings.

Refresh the local environment by running,

```shell
direnv allow .
```

Let is ensure the drone token works,

```shell
drone info
```

Activate the Repository
-----------------------

We need to activate the `quarkus-springboot-demo` in Drone, so that the git events on those repositories can trigger Drone builds.

Run the following command to activate the `quarkus-springboot-demo`,

```shell
drone repo activate "${QSBD_GIT_REPO}"
```

Add Secrets to Drone Repository
-------------------------------

The application build uses few secrets namely,

- `maven_mirror_url` : The maven mirror to used by Apache Maven builder to download the artifacts.
- `destination_image`: The container image name. Default to `${REGISTRY_NAME}/example/quarkus-springboot-demo`.
- `image_registry`: The Image registry to use. Derived from environment variable `${REGISTRY_NAME}`.
- `image_registry_user`: The Image registry username to authenticate.  Derived from environment variables `${IMAGE_REGISTRY_USER}`.
- `image_registry_password`: The Image registry user password to be used while authentication authenticate. Derived from environment variables `${IMAGE_REGISTRY_PASSWORD}`.

Run the following script to add the secrets to the Drone repo `${QUARKUS_SPRINGBOOT_DEMO_GIT_REPO}`,

```shell
./scripts/add-secrets.sh
```

Update .drone.yml
-----------------

Make sure the `mtu` value for the Docker plugin is set to right value in as per the environment,

```shell
ifconfig | grep cni
```

Update the value as per the `mtu` value shown in the output of the command.

Trigger Pipeline
----------------

Now push the local modifications to the git.

```shell
git commit -a -m "Trigger build"
git push origin "${QSBD_GIT_REV}"
```

The git **push** event will trigger a build on Drone. Open the **Drone** tab and navigate to `quarkus-springboot-demo` to see the build running. The first build will take some time during java-build step as Apache Maven need to download the artifacts from Maven Central and cache them on to the local Nexus Repository Manager.

**TIP**: To test triggers you can also use git command like `git commit --allow-empty -m "Test Trigger" -m "Test Trigger"`

🏁 Finish
=========

To complete this challenge, press **Check**.