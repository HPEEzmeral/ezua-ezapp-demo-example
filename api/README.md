# API

This scripts are designed to simplify the deployment and configuration of an application using EzAppConfig and Helm charts.
API folder contains the scripts and configuration files related to the EzAppConfig cli API.

## Prerequisites

Before running the scripts in this folder, ensure that the following tools are installed:

- [helm](https://github.com/helm/helm/releases/tag/v3.14.2)
- [jq](https://jqlang.github.io/jq/download/)
- [yq](https://github.com/mikefarah/yq?tab=readme-ov-file#download-the-latest-binary)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)

## Getting started

Clone the repository:

```console
git clone git@github.com:HPEEzmeral/ezua-ezapp-demo-example.git
```

Navigate to the script directory:

```console
cd api/tooling/
```

Ensure that the required tools are installed by running the following command:

```console
./check_required_tools.sh
```

Run the folowing command to see usage help:

```console
./bootstrap.sh
```

You will see the following output

```
Checking required tools...
- Helm version: v3.14.3
- jq version: 1.6
- yq version: v4.40.5
- kubectl version: v1.29.3
Required tools checked.
Usage: ./tooling/bootstrap.sh <COMMAND> [ARGUMENTS]

Commands:
        install
        upgrade
        delete
        build_and_upload_chart_bundle
                Uploads the current chart version to chartmuseum
        delete_chart_bundle [chart_version]
                Deletes the chart version from chartmuseum, if chart_version is not provided, current version is passed
```

Before you begin installing the application, please read the [configuration](#configuration) section.

### Install the application:

```console
./bootstrap.sh install
```

### Upgrade the application:

```console
./bootstrap.sh upgrade
```

### Delete the application:

```console
./bootstrap.sh delete
```

### Build and upload the chart tarball to Chartmuseum:

```console
build_and_upload_chart_bundle
```

### Delete the chart tarball from Chartmuseum:

```console
delete_chart_bundle [<chart_version>]
```
 The optional <chart_version> parameter is used to specify a specific chart version to delete. If not provided, the current version from `${CHART_PATH}/Chart.yaml` will be used.


## Configuration

To configure the API, modify the `configure` function in the `bootstrap.sh` file located in `api/tooling` folder.

Modify the following constants:

* `CHART_PATH`: Path to the application helm chart.

* `EZAPPCONFIG_TEMPLATE_PATH`: Path to the ezappconfig-template.yaml file.

* `EZAPPCONFIG_PATH`: Path to the configured ezappconfig.yaml file.

* `EZAPP_LOGO_PATH`: Path to the application logo PNG file.

### Configure the EzAppConfig for the application

The configure function is used to configure the values.yaml file for a specific application chart. It performs the following steps:

* Uses the yq tool to modify the values.yaml file for the chart. It updates the following fields:
Use this section to set override values for the application.

* `ezua.virtualService.endpoint` is set to the value of the ENDPOINT variable
Used to provide an endpoint be available from the UI App tile.

* `airgap.registry.url` is required if you going to install the application on airgapped environment.
set to the value of the AIRGAP_REGISTRY variable

* The modified content is then written to the `${TMP_VALUES_PATH}` file.

* `create_ezappconfig_from_template` function create the EzAppConfig from a template, using the values from the `${TMP_VALUES_PATH}` file.

* `build_and_upload_chart_bundle` function builds and upload the chart tarball to the chartmuseum.

**_NOTE:_** `${TMP_VALUES_PATH}` this is a local variable used to specify the path to the modified `values.yaml` -> `tmp.yaml` file. Don't modify it.

**_NOTE:_** if the application chart version already present in the chartmuseum, it will not override it. Bump the chart version first in `Chart.yaml` to use higer chart version or [delete chart from chartmuseum](#delete-the-chart-tarball-from-chartmuseum) before try to upload new chart tarball.

## EzAppConfig-template.yaml

The `test-app` Application use [ezappconfig-test-app-template.yaml](templates/ezappconfig-test-app-template.yaml) as an example.

The EzAppConfig API is described in more details here [EzAppConfig Spec Api](templates/README.md).
