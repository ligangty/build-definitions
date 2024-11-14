# Build-maven-zip task

Build-maven-zip task builds prefetched maven artifacts into a OCI-artifact with zip bundle  and pushes the OCI-artifact into container registry.
In addition it generates a SBOM file, injects the SBOM file into final container image and pushes the SBOM file as separate image using cosign tool.
Note that this task needs the output of prefetch-dependencies task. If it is not activated, there will not be any output from this task.

## Parameters

| name                 | description                                                            | default value    | required |
| -------------------- | ---------------------------------------------------------------------- | ---------------- | -------- |
| IMAGE                | Reference of the OCI-Artifact this build-maven-zip task will produce.  |                  | true     |
| PREFETCH_INPUT       | The prefetched content which is used in the build.                     | generic          | false    |
| PREFETCH_ROOT        | The root directory of the artifacts in the prefetched directory.       | maven-repository | false    |
| caTrustConfigMapName | The name of the ConfigMap to read CA bundle data from.                 | trusted-ca       | false    |
| caTrustConfigMapKey  | The name of the key in the ConfigMap that contains the CA bundle data. | ca-bundle.crt    | false    |

## Results

| name          | description                                                                       |
| ------------- | --------------------------------------------------------------------------------- |
| IMAGE_DIGEST  | Digest of the OCI-Artifact just built                                             |
| IMAGE_URL     | Image repository and tag where the built OCI-Artifact was pushed                  |
| SBOM_BLOB_URL | Reference of SBOM blob digest to enable digest-based verification from provenance |

## Workspaces

| name   | description                                    | optional |
| ------ | ---------------------------------------------- | -------- |
| source | Workspace containing the source code to build. | false    |
