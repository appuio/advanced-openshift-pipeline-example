# Advanced OpenShift Build Pipeline Example

This Project is an example OpenShift Jenkins Pipeline and it's based on the following example https://github.com/openshift/origin/blob/master/examples/jenkins/pipeline/samplepipeline.yaml

instead of injecting the jenkinsfile directly as part of the template, this example links a Jenkinsfile located in the Git Repository next to the source code of your application.

As addition the jenkins file contains also the following features:

* scm Polling
* Job Logrotation
* Notification if an error occured

## Setup

In this example we use multiple different OpenShift projects:

* builder-project (Jenkins and resulting Images)
* prod-project

The Images are built in the builder-project and then promoted from stage to stage, dev to prod.

## Builder Project

Instantiate Pipeline and Example Project 

```
oc new-app -f https://raw.githubusercontent.com/appuio/advanced-openshift-pipeline-example/master/ocp-template.yaml
```

the Jenkins and Jenkinsslave is instantiated automatically, the pipeline can now be triggered via Bulds --> Pipeline


## Promote to Production

To be able to read images from the builder project, the prod project needs puller rigts

```
oc policy add-role-to-group system:image-puller system:serviceaccounts:prod-project -n builder-project
```

update the tag on the prod project, to promote the new image

```
oc tag builder-project/image:latest prod-project/image:latest
```


## TODO

* add custom jenkins buildnodes, if additional buildtools are needed
