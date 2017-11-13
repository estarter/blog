---
layout: post
title: How to manage jenkins jobs
date: 2016-02-09 19:51:50
categories: CI
tags: []
---

[Jenkins CLI](https://icejenkins.cern.ch/cli/) allows to create a new job and update existing one using xml file description file.
Existing jobs can be also stored to the local file. That makes it possible to generate many alike jobs from the template using simple generation script.
From another side one may want to make periodical backups and controlling the modifications precisely  by exporting the jobs from jenkins and keeping xml files in git.

Here's the import and export script that I use: [https://github.com/estarter/jenkins-job-manager](https://github.com/estarter/jenkins-job-manager)
