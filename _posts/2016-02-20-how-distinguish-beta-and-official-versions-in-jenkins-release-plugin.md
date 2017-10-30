---
layout: post
title: How distinguish beta and official versions in Jenkins release plugin
date: 2016-02-20 19:20:23
categories: CI maven
---

We are using jenkins release plugin jobs to perform beta and official releases. The difference between releases are hidden in the defined maven profiles. Thus following [Release Plugin documentation](https://wiki.jenkins-ci.org/display/JENKINS/Release+Plugin) it should be RELEASE_VERSION and DEVELOPMENT_VERSION variable plus RELEASE_TYPE drop-down selection to be filled by user.

![Release Plugin screenshot]({{ "/assets/screen-shot-2016-02-20-at-19-17-08.png" | absolute_url }})

To make it prone-save we need to add a unit test that would check the correspondence of RELEASE_TYPE and RELEASE_VERSION. Because we can't make an official release of version 1.0.2-beta-02 neither beta release of 1.3.0.

Isn't it too many things to care about? You can make it simpler and automatically detect the release type from the version number. For that, add the pre-build step  of groovy script that would set ReleaseType variable:

```java
import hudson.model.*;
import hudson.util.*;

def build = Thread.currentThread().executable

def releaseVersion = build.buildVariableResolver.resolve("RELEASE_VERSION")
def releaseType = releaseVersion =~ /^[\d\.]+$/ ? "official-release" : "beta-release"

def pa = new ParametersAction([
  new StringParameterValue("ReleaseType", releaseType)
])
build.addAction(pa)
```

Then the complete release parameterization looks like this:

![Release configuration screenshot]({{ "/assets/screen-shot-2016-02-20-at-19-18-55.png" | absolute_url }})

And for the user it would be like this (we also skip DEVELOPMENT_VERSION parameter and make the make automatically assign one):

![Release action screenshot]({{ "/assets/screen-shot-2016-02-20-at-19-13-21.png" | absolute_url }})
