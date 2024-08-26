# "docker-build-multi-platform-oci-ta pipeline"
## Parameters
|name|description|default value|used in (taskname:taskrefversion:taskparam)|
|---|---|---|---|
|build-args| Array of --build-arg values ("arg=value" strings) for buildah| []| build-images:0.2:BUILD_ARGS|
|build-args-file| Path to a file with build arguments for buildah, see https://www.mankier.com/1/buildah-build#--build-arg-file| | build-images:0.2:BUILD_ARGS_FILE|
|build-image-index| Add built image into an OCI image index| true| build-image-index:0.1:ALWAYS_BUILD_INDEX|
|build-platforms| List of platforms to build the container images on. The available set of values is determined by the configuration of the multi-platform-controller.| ['linux/x86_64', 'linux/arm64']| |
|build-source-image| Build a source image.| false| |
|dockerfile| Path to the Dockerfile inside the context specified by parameter path-context| Dockerfile| build-images:0.2:DOCKERFILE ; push-dockerfile:0.1:DOCKERFILE|
|git-url| Source Repository URL| None| clone-repository:0.1:url|
|hermetic| Execute the build with network isolation| false| build-images:0.2:HERMETIC|
|image-expires-after| Image tag expiration time, time values could be something like 1h, 2d, 3w for hours, days, and weeks, respectively.| | clone-repository:0.1:ociArtifactExpiresAfter ; prefetch-dependencies:0.1:ociArtifactExpiresAfter ; build-images:0.2:IMAGE_EXPIRES_AFTER ; build-image-index:0.1:IMAGE_EXPIRES_AFTER|
|output-image| Fully Qualified Output Image| None| init:0.2:image-url ; clone-repository:0.1:ociStorage ; prefetch-dependencies:0.1:ociStorage ; build-images:0.2:IMAGE ; build-image-index:0.1:IMAGE ; build-source-image:0.1:BINARY_IMAGE|
|path-context| Path to the source code of an application's component from where to build image.| .| build-images:0.2:CONTEXT ; push-dockerfile:0.1:CONTEXT|
|prefetch-input| Build dependencies to be prefetched by Cachi2| | prefetch-dependencies:0.1:input ; build-images:0.2:PREFETCH_INPUT|
|rebuild| Force rebuild image| false| init:0.2:rebuild|
|revision| Revision of the Source Repository| | clone-repository:0.1:revision|
|skip-checks| Skip checks against built image| false| init:0.2:skip-checks|
## Available params from tasks
### apply-tags:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|ADDITIONAL_TAGS| Additional tags that will be applied to the image in the registry.| []| |
|CA_TRUST_CONFIG_MAP_KEY| The name of the key in the ConfigMap that contains the CA bundle data.| ca-bundle.crt| |
|CA_TRUST_CONFIG_MAP_NAME| The name of the ConfigMap to read CA bundle data from.| trusted-ca| |
|IMAGE| Reference of image that was pushed to registry in the buildah task.| None| '$(tasks.build-image-index.results.IMAGE_URL)'|
### build-image-index:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|ALWAYS_BUILD_INDEX| Build an image index even if IMAGES is of length 1. Default true. If the image index generation is skipped, the task will forward values for params.IMAGES[0] to results.IMAGE_*. In order to properly set all results, use the repository:tag@sha256:digest format for the IMAGES parameter.| true| '$(params.build-image-index)'|
|COMMIT_SHA| The commit the image is built from.| | '$(tasks.clone-repository.results.commit)'|
|IMAGE| The target image and tag where the image will be pushed to.| None| '$(params.output-image)'|
|IMAGES| List of Image Manifests to be referenced by the Image Index| None| '['$(tasks.build-images.results.IMAGE_REF[*])']'|
|IMAGE_EXPIRES_AFTER| Delete image tag after specified time resulting in garbage collection of the digest. Empty means to keep the image tag. Time values could be something like 1h, 2d, 3w for hours, days, and weeks, respectively.| | '$(params.image-expires-after)'|
|STORAGE_DRIVER| Storage driver to configure for buildah| vfs| |
|TLSVERIFY| Verify the TLS on the registry endpoint (for push/pull to a non-TLS registry)| true| |
### buildah-remote-oci-ta:0.2 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|ACTIVATION_KEY| Name of secret which contains subscription activation key| activation-key| |
|ADDITIONAL_SECRET| Name of a secret which will be made available to the build with 'buildah build --secret' at /run/secrets/$ADDITIONAL_SECRET| does-not-exist| |
|ADD_CAPABILITIES| Comma separated list of extra capabilities to add when running 'buildah build'| | |
|BUILD_ARGS| Array of --build-arg values ("arg=value" strings)| []| '['$(params.build-args[*])']'|
|BUILD_ARGS_FILE| Path to a file with build arguments, see https://www.mankier.com/1/buildah-build#--build-arg-file| | '$(params.build-args-file)'|
|CACHI2_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the prefetched dependencies.| | '$(tasks.prefetch-dependencies.results.CACHI2_ARTIFACT)'|
|COMMIT_SHA| The image is built from this commit.| | '$(tasks.clone-repository.results.commit)'|
|CONTEXT| Path to the directory to use as context.| .| '$(params.path-context)'|
|DOCKERFILE| Path to the Dockerfile to build.| ./Dockerfile| '$(params.dockerfile)'|
|ENTITLEMENT_SECRET| Name of secret which contains the entitlement certificates| etc-pki-entitlement| |
|HERMETIC| Determines if build will be executed without network access.| false| '$(params.hermetic)'|
|IMAGE| Reference of the image buildah will produce.| None| '$(params.output-image)'|
|IMAGE_APPEND_PLATFORM| Whether to append a sanitized platform architecture on the IMAGE tag| false| 'true'|
|IMAGE_EXPIRES_AFTER| Delete image tag after specified time. Empty means to keep the image tag. Time values could be something like 1h, 2d, 3w for hours, days, and weeks, respectively.| | '$(params.image-expires-after)'|
|PLATFORM| The platform to build on| None| |
|PREFETCH_INPUT| In case it is not empty, the prefetched content should be made available to the build.| | '$(params.prefetch-input)'|
|SKIP_UNUSED_STAGES| Whether to skip stages in Containerfile that seem unused by subsequent stages| true| |
|SOURCE_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the application source code.| None| '$(tasks.prefetch-dependencies.results.SOURCE_ARTIFACT)'|
|SQUASH| Squash all new and previous layers added as a part of this build, as per --squash| false| |
|STORAGE_DRIVER| Storage driver to configure for buildah| vfs| |
|TARGET_STAGE| Target stage in Dockerfile to build. If not specified, the Dockerfile is processed entirely to (and including) its last stage.| | |
|TLSVERIFY| Verify the TLS on the registry endpoint (for push/pull to a non-TLS registry)| true| |
|YUM_REPOS_D_FETCHED| Path in source workspace where dynamically-fetched repos are present| fetched.repos.d| |
|YUM_REPOS_D_SRC| Path in the git repository in which yum repository files are stored| repos.d| |
|YUM_REPOS_D_TARGET| Target path on the container in which yum repository files should be made available| /etc/yum.repos.d| |
|caTrustConfigMapKey| The name of the key in the ConfigMap that contains the CA bundle data.| ca-bundle.crt| |
|caTrustConfigMapName| The name of the ConfigMap to read CA bundle data from.| trusted-ca| |
### clair-scan:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|ca-trust-config-map-key| The name of the key in the ConfigMap that contains the CA bundle data.| ca-bundle.crt| |
|ca-trust-config-map-name| The name of the ConfigMap to read CA bundle data from.| trusted-ca| |
|docker-auth| unused, should be removed in next task version.| | |
|image-digest| Image digest to scan.| None| '$(tasks.build-image-index.results.IMAGE_DIGEST)'|
|image-url| Image URL.| None| '$(tasks.build-image-index.results.IMAGE_URL)'|
### clamav-scan:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|ca-trust-config-map-key| The name of the key in the ConfigMap that contains the CA bundle data.| ca-bundle.crt| |
|ca-trust-config-map-name| The name of the ConfigMap to read CA bundle data from.| trusted-ca| |
|docker-auth| unused| | |
|image-digest| Image digest to scan.| None| '$(tasks.build-image-index.results.IMAGE_DIGEST)'|
|image-url| Image URL.| None| '$(tasks.build-image-index.results.IMAGE_URL)'|
### deprecated-image-check:0.4 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|BASE_IMAGES_DIGESTS| Digests of base build images.| | |
|CA_TRUST_CONFIG_MAP_KEY| The name of the key in the ConfigMap that contains the CA bundle data.| ca-bundle.crt| |
|CA_TRUST_CONFIG_MAP_NAME| The name of the ConfigMap to read CA bundle data from.| trusted-ca| |
|IMAGE_DIGEST| Image digest.| None| '$(tasks.build-image-index.results.IMAGE_DIGEST)'|
|IMAGE_URL| Fully qualified image name.| None| '$(tasks.build-image-index.results.IMAGE_URL)'|
|POLICY_DIR| Path to directory containing Conftest policies.| /project/repository/| |
|POLICY_NAMESPACE| Namespace for Conftest policy.| required_checks| |
### ecosystem-cert-preflight-checks:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|ca-trust-config-map-key| The name of the key in the ConfigMap that contains the CA bundle data.| ca-bundle.crt| |
|ca-trust-config-map-name| The name of the ConfigMap to read CA bundle data from.| trusted-ca| |
|image-url| Image url to scan.| None| '$(tasks.build-image-index.results.IMAGE_URL)'|
### git-clone-oci-ta:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|caTrustConfigMapKey| The name of the key in the ConfigMap that contains the CA bundle data.| ca-bundle.crt| |
|caTrustConfigMapName| The name of the ConfigMap to read CA bundle data from.| trusted-ca| |
|depth| Perform a shallow clone, fetching only the most recent N commits.| 1| |
|enableSymlinkCheck| Check symlinks in the repo. If they're pointing outside of the repo, the build will fail. | true| |
|fetchTags| Fetch all tags for the repo.| false| |
|httpProxy| HTTP proxy server for non-SSL requests.| | |
|httpsProxy| HTTPS proxy server for SSL requests.| | |
|noProxy| Opt out of proxying HTTP/HTTPS requests.| | |
|ociArtifactExpiresAfter| Expiration date for the trusted artifacts created in the OCI repository. An empty string means the artifacts do not expire.| | '$(params.image-expires-after)'|
|ociStorage| The OCI repository where the Trusted Artifacts are stored.| None| '$(params.output-image).git'|
|refspec| Refspec to fetch before checking out revision.| | |
|revision| Revision to checkout. (branch, tag, sha, ref, etc...)| | '$(params.revision)'|
|sparseCheckoutDirectories| Define the directory patterns to match or exclude when performing a sparse checkout.| | |
|sslVerify| Set the `http.sslVerify` global git config. Setting this to `false` is not advised unless you are sure that you trust your git remote.| true| |
|submodules| Initialize and fetch git submodules.| true| |
|url| Repository URL to clone from.| None| '$(params.git-url)'|
|userHome| Absolute path to the user's home directory. Set this explicitly if you are running the image as a non-root user. | /tekton/home| |
|verbose| Log the commands that are executed during `git-clone`'s operation.| false| |
### init:0.2 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|image-url| Image URL for build by PipelineRun| None| '$(params.output-image)'|
|rebuild| Rebuild the image if exists| false| '$(params.rebuild)'|
|skip-checks| Skip checks against built image| false| '$(params.skip-checks)'|
### prefetch-dependencies-oci-ta:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|SOURCE_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the application source code.| None| '$(tasks.clone-repository.results.SOURCE_ARTIFACT)'|
|caTrustConfigMapKey| The name of the key in the ConfigMap that contains the CA bundle data.| ca-bundle.crt| |
|caTrustConfigMapName| The name of the ConfigMap to read CA bundle data from.| trusted-ca| |
|config-file-content| Pass configuration to cachi2. Note this needs to be passed as a YAML-formatted config dump, not as a file path! | | |
|dev-package-managers| Enable in-development package managers. WARNING: the behavior may change at any time without notice. Use at your own risk. | false| |
|input| Configures project packages that will have their dependencies prefetched.| None| '$(params.prefetch-input)'|
|log-level| Set cachi2 log level (debug, info, warning, error)| info| |
|ociArtifactExpiresAfter| Expiration date for the trusted artifacts created in the OCI repository. An empty string means the artifacts do not expire.| | '$(params.image-expires-after)'|
|ociStorage| The OCI repository where the Trusted Artifacts are stored.| None| '$(params.output-image).prefetch'|
### push-dockerfile-oci-ta:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|ARTIFACT_TYPE| Artifact type of the Dockerfile image.| application/vnd.konflux.dockerfile| |
|CONTEXT| Path to the directory to use as context.| .| '$(params.path-context)'|
|DOCKERFILE| Path to the Dockerfile.| ./Dockerfile| '$(params.dockerfile)'|
|IMAGE| The built binary image. The Dockerfile is pushed to the same image repository alongside.| None| '$(tasks.build-image-index.results.IMAGE_URL)'|
|IMAGE_DIGEST| The built binary image digest, which is used to construct the tag of Dockerfile image.| None| '$(tasks.build-image-index.results.IMAGE_DIGEST)'|
|SOURCE_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the application source code.| None| '$(tasks.prefetch-dependencies.results.SOURCE_ARTIFACT)'|
|TAG_SUFFIX| Suffix of the Dockerfile image tag.| .dockerfile| |
### sast-snyk-check-oci-ta:0.2 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|ARGS| Append arguments.| --all-projects --exclude=test*,vendor,deps| |
|CACHI2_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the prefetched dependencies.| | |
|SNYK_SECRET| Name of secret which contains Snyk token.| snyk-secret| |
|SOURCE_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the application source code.| None| '$(tasks.prefetch-dependencies.results.SOURCE_ARTIFACT)'|
|image-digest| Image digest to report findings for.| | '$(tasks.build-image-index.results.IMAGE_DIGEST)'|
|image-url| Image URL.| | '$(tasks.build-image-index.results.IMAGE_URL)'|
### sbom-json-check:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|CA_TRUST_CONFIG_MAP_KEY| The name of the key in the ConfigMap that contains the CA bundle data.| ca-bundle.crt| |
|CA_TRUST_CONFIG_MAP_NAME| The name of the ConfigMap to read CA bundle data from.| trusted-ca| |
|IMAGE_DIGEST| Image digest.| None| '$(tasks.build-image-index.results.IMAGE_DIGEST)'|
|IMAGE_URL| Fully qualified image name to verify.| None| '$(tasks.build-image-index.results.IMAGE_URL)'|
### show-sbom:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|CA_TRUST_CONFIG_MAP_KEY| The name of the key in the ConfigMap that contains the CA bundle data.| ca-bundle.crt| |
|CA_TRUST_CONFIG_MAP_NAME| The name of the ConfigMap to read CA bundle data from.| trusted-ca| |
|IMAGE_URL| Fully qualified image name to show SBOM for.| None| '$(tasks.build-image-index.results.IMAGE_URL)'|
|PLATFORM| Specific architecture to display the SBOM for. An example arch would be "linux/amd64". If IMAGE_URL refers to a multi-arch image and this parameter is empty, the task will default to use "linux/amd64".| linux/amd64| |
### source-build-oci-ta:0.1 task parameters
|name|description|default value|already set by|
|---|---|---|---|
|BASE_IMAGES| By default, the task inspects the SBOM of the binary image to find the base image. With this parameter, you can override that behavior and pass the base image directly. The value should be a newline-separated list of images, in the same order as the FROM instructions specified in a multistage Dockerfile.| | |
|BINARY_IMAGE| Binary image name from which to generate the source image name.| None| '$(params.output-image)'|
|CACHI2_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the prefetched dependencies.| | '$(tasks.prefetch-dependencies.results.CACHI2_ARTIFACT)'|
|SOURCE_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the application source code.| None| '$(tasks.prefetch-dependencies.results.SOURCE_ARTIFACT)'|

## Results
|name|description|value|
|---|---|---|
|CHAINS-GIT_COMMIT| |$(tasks.clone-repository.results.commit)|
|CHAINS-GIT_URL| |$(tasks.clone-repository.results.url)|
|IMAGE_DIGEST| |$(tasks.build-image-index.results.IMAGE_DIGEST)|
|IMAGE_URL| |$(tasks.build-image-index.results.IMAGE_URL)|
## Available results from tasks
### build-image-index:0.1 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|IMAGES| List of all referenced image manifests| |
|IMAGE_DIGEST| Digest of the image just built| deprecated-base-image-check:0.4:IMAGE_DIGEST ; clair-scan:0.1:image-digest ; sast-snyk-check:0.2:image-digest ; clamav-scan:0.1:image-digest ; sbom-json-check:0.1:IMAGE_DIGEST ; push-dockerfile:0.1:IMAGE_DIGEST|
|IMAGE_URL| Image repository and tag where the built image was pushed| show-sbom:0.1:IMAGE_URL ; deprecated-base-image-check:0.4:IMAGE_URL ; clair-scan:0.1:image-url ; ecosystem-cert-preflight-checks:0.1:image-url ; sast-snyk-check:0.2:image-url ; clamav-scan:0.1:image-url ; sbom-json-check:0.1:IMAGE_URL ; apply-tags:0.1:IMAGE ; push-dockerfile:0.1:IMAGE|
### buildah-remote-oci-ta:0.2 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|IMAGE_DIGEST| Digest of the image just built| |
|IMAGE_REF| Image reference of the built image| build-image-index:0.1:IMAGES|
|IMAGE_URL| Image repository and tag where the built image was pushed| |
|JAVA_COMMUNITY_DEPENDENCIES| The Java dependencies that came from community sources such as Maven central.| |
|SBOM_BLOB_URL| Reference of SBOM blob digest to enable digest-based verification from provenance| |
|SBOM_JAVA_COMPONENTS_COUNT| The counting of Java components by publisher in JSON format| |
### clair-scan:0.1 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|CLAIR_SCAN_RESULT| Clair scan result.| |
|IMAGES_PROCESSED| Images processed in the task.| |
|TEST_OUTPUT| Tekton task test output.| |
### clamav-scan:0.1 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|IMAGES_PROCESSED| Images processed in the task.| |
|TEST_OUTPUT| Tekton task test output.| |
### deprecated-image-check:0.4 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|IMAGES_PROCESSED| Images processed in the task.| |
|TEST_OUTPUT| Tekton task test output.| |
### ecosystem-cert-preflight-checks:0.1 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|TEST_OUTPUT| Preflight pass or fail outcome.| |
### git-clone-oci-ta:0.1 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|SOURCE_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the application source code.| prefetch-dependencies:0.1:SOURCE_ARTIFACT|
|commit| The precise commit SHA that was fetched by this Task.| build-images:0.2:COMMIT_SHA ; build-image-index:0.1:COMMIT_SHA|
|commit-timestamp| The commit timestamp of the checkout| |
|url| The precise URL that was fetched by this Task.| |
### init:0.2 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|build| Defines if the image in param image-url should be built| |
### prefetch-dependencies-oci-ta:0.1 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|CACHI2_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the prefetched dependencies.| build-images:0.2:CACHI2_ARTIFACT ; build-source-image:0.1:CACHI2_ARTIFACT ; ecosystem-cert-preflight-checks:0.1:CACHI2_ARTIFACT|
|SOURCE_ARTIFACT| The Trusted Artifact URI pointing to the artifact with the application source code.| build-images:0.2:SOURCE_ARTIFACT ; build-source-image:0.1:SOURCE_ARTIFACT ; sast-snyk-check:0.2:SOURCE_ARTIFACT ; push-dockerfile:0.1:SOURCE_ARTIFACT|
### push-dockerfile-oci-ta:0.1 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|IMAGE_REF| Digest-pinned image reference to the Dockerfile image.| |
### sast-snyk-check-oci-ta:0.2 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|TEST_OUTPUT| Tekton task test output.| |
### sbom-json-check:0.1 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|IMAGES_PROCESSED| Images processed in the task.| |
|TEST_OUTPUT| Tekton task test output.| |
### source-build-oci-ta:0.1 task results
|name|description|used in params (taskname:taskrefversion:taskparam)
|---|---|---|
|BUILD_RESULT| Build result.| |
|IMAGE_REF| Image reference of the built image| |
|SOURCE_IMAGE_DIGEST| The source image digest.| |
|SOURCE_IMAGE_URL| The source image url.| |

## Workspaces
|name|description|optional|used in tasks
|---|---|---|---|
|git-auth| |True| clone-repository:0.1:basic-auth ; prefetch-dependencies:0.1:git-basic-auth|
|netrc| |True| prefetch-dependencies:0.1:netrc|
## Available workspaces from tasks
### git-clone-oci-ta:0.1 task workspaces
|name|description|optional|workspace from pipeline
|---|---|---|---|
|basic-auth| A Workspace containing a .gitconfig and .git-credentials file or username and password. These will be copied to the user's home before any git commands are run. Any other files in this Workspace are ignored. It is strongly recommended to use ssh-directory over basic-auth whenever possible and to bind a Secret to this Workspace over other volume types. | True| git-auth|
|ssh-directory| A .ssh directory with private key, known_hosts, config, etc. Copied to the user's home before git commands are executed. Used to authenticate with the git remote when performing the clone. Binding a Secret to this Workspace is strongly recommended over other volume types. | True| |
### prefetch-dependencies-oci-ta:0.1 task workspaces
|name|description|optional|workspace from pipeline
|---|---|---|---|
|git-basic-auth| A Workspace containing a .gitconfig and .git-credentials file or username and password. These will be copied to the user's home before any cachi2 commands are run. Any other files in this Workspace are ignored. It is strongly recommended to bind a Secret to this Workspace over other volume types. | True| git-auth|
|netrc| Workspace containing a .netrc file. Cachi2 will use the credentials in this file when performing http(s) requests. | True| netrc|