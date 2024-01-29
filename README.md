# ReversingLabs rl-scanner-cloud Jenkins Examples

This repository contains working examples of Jenkins pipeline scripts to illustrate scanning with the
[ReversingLabs secure.software Portal](https://docs.secure.software/portal/integrations/).

ReversingLabs secure.software Portal is capable of scanning
[nearly any type](https://docs.secure.software/concepts/language-coverage)
of software artifact or package that results from a build.

In this example, we're using the source code and Maven build instructions for the Struts2 showcase web app, which came with [Apache Struts v2.5.28](https://archive.apache.org/dist/struts/2.5.28/).

The following examples are provided in this repository:

- **Jenkinsfile**


## Jenkinsfile

This pipeline script builds the WAR file and scans it using the
[ReversingLabs rl-scanner-cloud Docker image](https://hub.docker.com/r/reversinglabs/rl-scanner-cloud).

After the file is scanned, analysis reports in JSON, CycloneDX, and SPDX formats are published as a build artifact.

The script requires that you create the `RLPORTAL_ACCESS_TOKEN` secret credential within Jenkins to store your ReversingLabs
[Portal access token](https://docs.secure.software/api/generate-api-token).

**Supported parameters**

| Name | Required | Type | Description |
| ---- | -------- | ---- | ----------- |
| `RLPORTAL_ACCESS_TOKEN` | *Yes* | string | A Personal Access Token for authenticating requests to the secure.software Portal. Before you can use this example, you must [create the token](https://docs.secure.software/api/generate-api-token) in your Portal settings. Tokens can expire and be revoked, in which case you'll have to update this value. |
| `RLPORTAL_SERVER`      | *Yes* | string | Name of the secure.software Portal instance to use for the scan. The Portal instance name usually matches the subdirectory of `my.secure.software` in your Portal URL. For example, if your portal URL is `my.secure.software/demo`, the instance name to use with this parameter is `demo`. |
| `RLPORTAL_ORG`         | *Yes* | string | The name of a secure.software Portal organization to use for the scan. The organization must exist on the Portal instance specified with `RLPORTAL_SERVER`. The user account authenticated with the token must be a member of the specified organization and have the appropriate permissions to upload and scan a file. Organization names are case-sensitive. |
| `RLPORTAL_GROUP`       | *Yes* | string | The name of a secure.software Portal group to use for the scan. The group must exist in the Portal organization specified with `RLPORTAL_ORG`. Group names are case-sensitive. |
| `ARTIFACT2SCAN` | *Yes* | string | The artifact (file) you want to scan. The file must be in any of the [formats supported by secure.software](https://docs.secure.software/concepts/reference). | 


## Pipeline result

The scan in this example will produce the `FAIL` CI status.
The pipeline will fail, as the scanner detects 4 critical issues: `Found 4 vulnerabilities matching selected criteria.`

## Useful resources

  1. [The official secure.software Portal documentation](https://docs.secure.software/portal/)
  2. The official `reversinglabs/rl-scanner-cloud` Docker image [on Docker Hub](https://hub.docker.com/r/reversinglabs/rl-scanner-cloud)
