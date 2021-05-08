# naps-ds-notebook-image-test
testing custom notebook image based on thoth-station s2i-minimal-notebook as builder

## Goals
[x] ubi8
[x] python38
[ ] pip 21.1.1
[x] jupyter
[x] jupyterlab
[ ] cuda
[ ] cudNN
[x] accessible via ODH spawner

# Procedures
1. create the BuildConfig
1. create the ImageStream

## BuildConfig
```
apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: naps-ds-notebook-image-test
  namespace: opendatahub
spec:
  output:
    to:
      kind: ImageStreamTag
      name: 's2i-build:latest'
  source:
    git:
      ref: master
      uri: 'https://github.com/dmarcus-wire/naps-ds-notebook-image-test'
    type: Git
  strategy:
    type: Source
    sourceStrategy:
      from:
        kind: ImageStreamTag
        name: 's2i-minimal-py38-notebook:latest'
        namespace: openshift
      env: []
  triggers:
    - type: ImageChange
      imageChange: {}
    - type: ConfigChange
```

## ImageStream
```
apiVersion: image.openshift.io/v1
kind: ImageStream
metadata:
  labels:
    opendatahub.io/notebook-image: "true"
  annotations:
    opendatahub.io/notebook-image-url: "https://github.com/thoth-station/s2i-minimal-notebook-py38"
    opendatahub.io/notebook-image-name: "naps-ds-notebook-image-test"
    opendatahub.io/notebook-image-desc: "Jupyter notebook image with minimal dependency set to start experimenting with Jupyter environment."
  name: naps-ds-notebook-image-test
spec:
  lookupPolicy:
    local: true
  tags:
  - annotations:
      openshift.io/imported-from: quay.io/thoth-station/s2i-minimal-py38-notebook
    from:
      kind: DockerImage
      name: quay.io/thoth-station/s2i-minimal-py38-notebook:latest
    name: "latest"
    referencePolicy:
      type: Source
```
