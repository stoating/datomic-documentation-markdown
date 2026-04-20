# Datomic Cloud Releases

This page lists the releases for all [Datomic Cloud software](#software):

- [Current releases](#current)
- [Critical notices](#critical-notices)
- [Release history](../02-cloud-change-log/cloud-change-log.md)

## Datomic Software

Datomic Cloud software includes:

- CloudFormation templates to [start](../../../05-operation/02-cloud/02-start-a-system/start-a-system.md) or [upgrade](../../../05-operation/02-cloud/14-upgrading/upgrading.md) a system
- The [Client API library](../../../04-apis/04-client-api/client-api.md)
- [Ion libraries](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md)
- The [Datomic CLI tools](../../../05-operation/02-cloud/07-cli-tools/cli-tools.md)

To track Datomic Local releases, see the [Datomic Local documentation](../../../01-setup/03-local-setup/local-setup.md).

## Critical Notices

If you are using any version of Datomic Cloud older than 470-8654.1 (April 2019), follow the recommendations in the table below. See the release link for full details.

| Affected | Date | Release | Recommendation |
|---|---|---|---|
| Production, solo, query groups | 2022/12/12 | [981-9188](../02-cloud-change-log/cloud-change-log.md#981-9188) | [Upgrade as soon as possible](../02-cloud-change-log/cloud-change-log.md#990-9202) |
| Production, solo, query groups | 2021/07/13 | [884-9095](../02-cloud-change-log/cloud-change-log.md#884-9095) | [Review Upgrading Documentation](../02-cloud-change-log/cloud-change-log.md#884-9095) |
| Production, solo, query groups | 2020/11/30 | [732-8992](../02-cloud-change-log/cloud-change-log.md#732-8992) | Upgrade as soon as possible |
| Production, solo, query groups | 2020/02/21 | [616-8879](../02-cloud-change-log/cloud-change-log.md#616-8879) | Introduced with 569-8835, Upgrade as soon as possible |
| Production, solo, query groups | 2019/04/25 | [470-8654.1](../02-cloud-change-log/cloud-change-log.md#470-8654.1) | Upgrade as soon as possible |
| Production, query groups | 2019/02/22 | [470-8654](../02-cloud-change-log/cloud-change-log.md#470-8654) | See note if upgrading to or past this version |
| Production, query groups | 2018/10/11 | [441-8505](../02-cloud-change-log/cloud-change-log.md#441-8505) | Upgrade as soon as possible |

## Current Releases

- Ion libraries (`ion` and `ion-dev`) are available on the [datomic-cloud repository](../../../07-datomic-cloud-ions/02-ions-reference/ions-reference.md#libraries).
- The `client API` library is available on the Maven [central repository](https://search.maven.org/).
- CloudFormation templates and CLI tool are linked directly in the table below.

| Artifact | Release Date | Version | Release |
|---|---|---|---|
| [Client API](../../../04-apis/04-client-api/client-api.md#installing) | 2025/02/03 | 1.0.131 | `com.datomic/client-cloud "1.0.131"` |
| [ion-dev](../../../05-operation/02-cloud/11-how-to/how-to.md#ion-dev) | 2026/02/17 | 1.0.352 | `com.datomic/ion-dev "1.0.352"` |
| [ion](../../../05-operation/02-cloud/11-how-to/how-to.md#ion) | 2024/07/11 | 1.0.71 | `com.datomic/ion "1.0.71"` |
| [CLI Tools](../../../05-operation/02-cloud/07-cli-tools/cli-tools.md) | 2022/01/14 | 1.0.91 | [Download](https://datomic-releases-1fc2183a.s3.amazonaws.com/tools/datomic-cli/datomic-cli-1.0.91.zip) |
| [Datomic Presto Server](../../../08-analytics/03-cloud-configuration/cloud-configuration.md) | 2021/07/13 | 0.9.96 | [Download (1.17GB)](https://downloads.datomic.com/builds/datomic-presto-server/datomic-presto-server-96.zip) |
| [REBL](../../../08-analytics/13-other-tools/other-tools.md) | 2022/04/06 | 0.9.245 | [Get dev-tools](https://www.cognitect.com/dev-tools) |
| [datomic-local](../../../01-setup/03-local-setup/local-setup.md) | 2025/03/19 | 1.0.291 | |
| [Storage](../../../05-operation/02-cloud/14-upgrading/upgrading.md#storage-only-upgrade) | 2026/02/17 | 1217-9399 | [Storage Template](https://s3.amazonaws.com/datomic-cloud-1/cft/1217/storage-template-9399-1217.json) |
| [Primary Compute](../../../05-operation/02-cloud/14-upgrading/upgrading.md#compute-only-upgrade) | 2026/02/17 | 1217-9399 | [Compute Template](https://s3.amazonaws.com/datomic-cloud-1/cft/1217/compute-template-9399-1217.json) |
| [Query Group](../../../05-operation/02-cloud/05-compute-templates/compute-templates.md#update) | 2026/02/17 | 1217-9399 | [Query Group Template](https://s3.amazonaws.com/datomic-cloud-1/cft/1217/query-group-template-9399-1217.json) |
