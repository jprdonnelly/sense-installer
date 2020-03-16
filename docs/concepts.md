# How qliksense cli works

At the initialization `qliksense` cli create few files in the director `~/.qliksene` and it contains following files

```console
.qliksense
├── config.yaml
├── contexts
│   └── qlik-default
│       └── qlik-default.yaml
└── ejson
    └── keys
```

`qlik-default.yaml` is a default CR has been created with some default values like this

```yaml
apiVersion: qlik.com/v1
kind: Qliksense
metadata:
  name: qlik-default
spec:
  profile: docker-desktop
  secrets:
    qliksense:
    - name: mongoDbUri
      value: mongodb://qlik-default-mongodb:27017/qliksense?ssl=false
  rotateKeys: "yes"
  releaseName: qlik-default
```

The `qliksense` cli creates a default qliksense context (different from kubectl context) named `qlik-default` which will be the prefix for all kubernetes resources created by the cli under this context latter on. New context and configuration can be created by the cli.

```console
$ qliksense config -h
do operations on/around CR

Usage:
  qliksense config [command]

Available Commands:
  apply         generate the patchs and apply manifests to k8s
  list-contexts retrieves the contexts and lists them
  set           configure a key value pair into the current context
  set-configs   set configurations into the qliksense context as key-value pairs
  set-context   Sets the context in which the Kubernetes cluster and resources live in
  set-secrets   set secrets configurations into the qliksense context as key-value pairs
  view          view the qliksense operator CR

Flags:
  -h, --help   help for config

Use "qliksense config [command] --help" for more information about a command.
```

`qliksense` cli works in two modes

- with a git repo fork/clone of [qliksense-k8s](https://github.com/qlik-oss/qliksense-k8s)
- without git repo

## Without git repo

In this mode `qliksense` CLI download the specified version from [qliksense-k8s](https://github.com/qlik-oss/qliksense-k8s) and put it into folder `~/.qliksense/contexts/<context-name>/qlik-k8s`.

The qliksense cli create a CR for the qliksense operator and all the config operations are peformed to edit the CR. So when `qliksense install` or `qliksense config apply` both generate patches in local file system (i.e `~/.qliksense/contexts/<context-name>/qlik-k8s`) and install those manifests into the cluster and create a custom resoruce (CR) for the `qliksene operator` then the operator make association to the isntalled resoruces  so that when `qliksenes uninstall` is performed the operator can delete all those kubernetes resources related to QSEoK for the current context.

## With a git repo

User has to create fork or clone of [qliksense-k8s](https://github.com/qlik-oss/qliksense-k8s) and push it to their own git server. When user perform `qliksense install` or `qliksene config apply` the qliksense operator do these tasks

- downloads the corresponding version of manifests from the user's git repo.
- generate kustomize patches
- install kubernetes resoruces 
- push those generated patches into a new branch in the provided git repo. so that user user can merge those patches into their master branch. 
- spinup a cornjob to monitor master branch. If user modifies anything in the master branch those changes will be applied into the cluster. This is a light weight `git-ops` model

This is how repo info is provided into the CR

```console
qliksense config set git.repository="https://github.com/my-org/qliksense-k8s"

qliksense config set git.accessToken=blablalaala
```