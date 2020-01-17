# Qlik Sense installation and operations CLI

The Qlik Sense installations and operations CLI provides capabilities for installing the Qlik Sense on Kubernetes packaging and performing operations on qliksense.

## Getting started

Download the appropriate executable for your platform from the [releases page](https://github.com/qlik-oss/sense-installer/releases). When used, the CLI will check to see if porter is installed, if not, will download and install it. Once done, you can find porter through `echo $HOME/.porter` on Linux and MacOS and in `$Env:USERPROFILE\.porter` on Windows. You can also install it in advance, release > 0.22.1-beta.1 is required.

To make sure everything is order, you can fetch the Qlik Sense bundle version and corresponding image list from:
 - `qliksense about --tag qlik/qliksense-cnab-bundle:latest `

## Running Preflight checks
You can run preflight checks to ensure that the cluster is in a healthy state before installing Qliksense. 
- `qliksense preflight -c <credential_name> `

The above command runs the checks in the default namespace. If you want to specify the namespace to run preflight checks on:
- `qliksense preflight --param namespace=<value> -c <credential_name> `

## Qliksense Packaging
Packaging of Qlik Sense on Kubernetes is done through a [Porter](https://porter.sh/) definition in the [Qlik Sense on Kubernetes configuration repository](https://github.com/qlik-oss/qliksense-k8s/blob/master/porter.yaml), the resulting bundle published on DockerHub as a [Cloud Natvie Application Bundle](https://cnab.io/) called [qliksense-cnab-bundle](https://hub.docker.com/r/qlik/qliksense-cnab-bundle).
### Versioning
A version of [qliksense-cnab-bundle](https://hub.docker.com/r/qlik/qliksense-cnab-bundle) is published corresponding to an edge release. To get the latest edge release simply specify `qliksense-cnab-bundle:latest`

## Known Issues

if you see the error message like this

```console
invoking custom action about on Qliksense...
Unable to find image 'qlik/qliksense-cnab-bundle@sha256:b41a3be13914daee9fb5b9b2d39e68aa99526600d9a99448b346992d6de48171' locally
Error: 1 error occurred:
	* failed to invoke the bundle: Error response from daemon: manifest for qlik/qliksense-cnab-bundle@sha256:b41a3be13914daee9fb5b9b2d39e68aa99526600d9a99448b346992d6de48171 not found: manifest unknown: manifest unknown
```

please issue this command

```console
qliksense cache clear
```