---
title: "Continuous Integration on Bitrise for Android"
date: 2018-01-14 17:56:00
tags: android continous-integration android-app-development bitrise testing
---

At the company that I am working we recently started working on an Android app. One of the joys that comes with starting a new app is setting up the CI pipeline to your liking. For the iOS app we were using Bitrise. A not-so-known CI perhaps. It is gaining a lot more traction however and I wanted to highlight a very simple, yet powerful CI setup on Bitrise for Android.

> *This post assumes that a project has already been created and a workflow called PR has already been made.*

A newly-created project on Bitrise will, by default, have three steps already defined called ‘Activate SSH key’, ‘Git Clone Repository’ and ‘Deploy to Bitrise.io’. Steps on Bitrise represent a particular part of functionality. For example ‘Git Clone Repository’, as the name suggests, clones the repository based on the Git URL that was configured when setting up the project.

![Default setup of project on Bitrise](https://cdn-images-1.medium.com/max/2000/1*IR9gaK0Ozm_VUk9v-Ggrkg.png)*Default setup of project on Bitrise*

### Building the app and running the unit tests

Since this is the workflow for the PR build I would advise to build the app using the *assembleDebug *Gradle task. This will create a debug build of your app. To setup this Gradle task, press the + icon between the ‘Git Clone Repository’ and ‘Deploy to Bitrise.io’.

In the dialog that appears, search for Gradle Runner. Afterwards, configure it like this.

![Config of Gradle Runner step](https://cdn-images-1.medium.com/max/3372/1*ulvzzRFSZzxqyZKvAP7Lbw.png)*Config of Gradle Runner step*

At this point the app will be built, however the unit tests could still be failing without us noticing.

Bitrise makes running unit tests of your app super, super simple. As with any step, press the + icon again. This time after the Gradle runner task that we just added, drag in the step called ‘Gradle Unit test’.

This is everything that you have to do for the unit tests to run. This will automatically run the gradle task called test which will run all of the unit tests.

### Deploying the built APK to Bitrise.io so users can download it

For pull requests you might want to skip this step since the final version of the APK can still change quite heavily and developers can just as easy check out the pull request and use that to run the app. Nonetheless I would still like to explain how this process works on Bitrise. The ‘Deploy to Bitrise.io’ step automatically takes all of the files that were generated in a specific folder and deploys these to Bitrise.io so these can be distributed. The first step that build the app put the APK in this specific folder so this APK will automatically be deployed.

### Running UI tests on every build

It is not uncommon that including in your builds will be UI tests. UI tests in this context being tests that require an Android emulator to be running. Bitrise offers something called [Virtual Device Testing](https://blog.bitrise.io/introducing-solid-and-snappy-virtual-device-testing-for-android), that service is still in beta. Once setting up the workflow the emulator took forever to run so for now I’ve found another option that works better at this moment.

The first step to get this to work is to add a step called ‘AVD Manager’ at the start of the workflow. This step basically boots up an emulator with a specific device profile and API level. This can be configured to your liking, however the defaults are perfectly fine. After adding this step you should add the ‘Wait for Android Emulator’ right before the moment that you would execute the UI tests (This has not been configured yet.). It can take quite some time for the emulator to boot and this will ensure that the workflow halts until the emulator has finished booting.

The last and final step to add is very similar to the unit testing step. After the ‘Wait for Android Emulator’ step put in the step called ‘Gradle Unit test’. Now configure it to run the ‘connectedAndroidTest’ Gradle task. This task will use the emulator that was booted to run all of the UI tests.

![Configuration for the Gradle unit test task](https://cdn-images-1.medium.com/max/3280/1*ibM57_Zi1lp-Znr0aIrUhg.png)*Configuration for the Gradle unit test task*

The end result is a workflow that will build the app, run the unit & UI tests and if anything fails will fail the build. Once the app builds successfully it will even deploy the APK to Bitrise so users can build it.

