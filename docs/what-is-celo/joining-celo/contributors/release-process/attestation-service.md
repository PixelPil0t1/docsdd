---
title: Celo Attestation Service Release Process
description: Details of the release process for updating the attestation service on the Celo platform.
---

# Attestation Service Release Process

Details of the release process for updating the attestation service on the Celo platform.

:::warning
As of block height 31,056,500 (March 26, 2025, 3:00 AM UTC), Celo is no longer a standalone Layer 1 blockchain—it is now an Ethereum Layer 2!
Some documentation may be outdated as updates are in progress. If you encounter issues, please [file a bug report](https://github.com/celo-org/docs/issues/new/choose).

For the most up-to-date information, refer to our [Celo L2 documentation](https://docs.celo.org/cel2).
:::

---

:::tip note

This release process is currently in use.

:::

## Versioning

Releases of Attestation Service are made as needed. Releases are numbered according to semantic versioning, as described at [semver.org](https://semver.org).

Development builds should be identified with `-dev`, and only one commit should exist with a released version `x.y.z` for any `(x, y, z)`.

## Documentation

Documentation is maintained in the [celo-org/docs](https://github.com/celo-org/docs) repo and is hosted on [docs.celo.org](/).

## Identifying releases

### Git branches

Development is done on the `master` branch, which corresponds to the next major or minor version. Changes to be included in a patch release of an existing minor version are cherry-picked to that existing release branch.

### Git tags

Each release should be [created on Github](https://github.com/celo-org/celo-monorepo/releases) and tagged with the version number, e.g. `attestation-service-vX.Y.Z`. Each release should include a summary of the release contents, including links to pull requests and issues with detailed description of any notable changes.

Tags should be signed and can be verified with the following command.

```bash
git verify-tag attestation-service-vX.Y.Z
```

On Github, each release tag should have attached signatures that can be used to verify the Docker images.

### Docker tags

Each Docker image is tagged with `attestation-service-<commithash>`. Just as a Git tag immutably points to a commit hash, the Docker tag should immutably point to an image hash.

In addition, each Docker image corresponding to a released version should be tagged with `attestation-service-vX.Y.Z`.

The latest image qualified for deployment to various networks are also tagged as follows:

- Alfajores: `attestation-service-alfajores`
- Baklava: `attestation-service-baklava`
- Mainnet: `attestation-service-mainnet`

### Signatures

Artifacts produced by this build process (e.g. tags, Docker images) will be signed by a [core developer key](https://github.com/celo-org/celo-monorepo/blob/master/developer_key_publishing.md).

Public keys for core developers are hosted on celo.org and can be imported to `gpg` with the following command:

```bash
gpg --auto-key-locate wkd --locate-keys $EMAIL
```

Currently hosted core developer keys used for Attestation Service releases include:

- tim@clabs.co

## Build process

### Docker images

Docker images are built automatically with [Google Cloud Build](https://cloud.google.com/build) upon pushes to `master` and all release branches. Automated builds will be tagged in [Google Artifact Registry](https://cloud.google.com/artifact-registry) with the corresponding commit hash.

A signature should be produced over the image automatically built at the corresponding commit hash and included with the Github release.

Release image signatures can be verified with the following command:

```bash
docker save $(docker image inspect us.gcr.io/celo-testnet/celo-monorepo:attestation-service-vX.Y.Z -f '{{ .Id }}') | gpg --verify attestation-service-vX.Y.Z.docker.asc -
```

## Testing

As well as monorepo CI tests, all releases are expected to go through manual testing as needed to verify security properties, accuracy of documentation, and compatibility with deployed and anticipated versions of `celocli` and wallets including Valora. Releases currently involve coordinating with Valora to run the verification e2e tests in CI.

## Promotion process

### Source control

Patch releases should be constructed by cherry-picking all included commits from `master` to the `release/attestation-service/x.y` branch, if necessary created from the `attestation-service-vX.Y.Z` tag of the most recent major or minor release. The first commit of this process should change the version number encoded in the source from `x.y.z` to `x.y.z+1-dev` and the final commit should change the version number to `x.y.z+1`.

Major and minor releases should be constructed by pushing a commit to the `master` branch to change the encoded version number from `x.y.z-dev` to `x.y.z`. A `attestation-service-vX.Y.Z` tag should be created at this commit which uniquely references one commit; release notes should be published alongside this. The next commit should change the version number from `x.y.z` to `x.y+1.0-dev`, or `x+1.0.0-dev` if the next planned release is a major release.

### Distribution

Distribution of an image follows this schedule:

<table>
  <tr>
    <td>Date</td>
    <td>Action</td>
  </tr>
  <tr>
    <td>T-1w</td>
    <td>
      <ol>
        <li>Deploy release candidate build to Alfajores testnet</li>
        <li>Test manually and via e2e verification tests</li>
      </ol>
    </td>
  </tr>  
  <tr>
    <td>T</td>
    <td>
      <ol>
        <li>Confirm Valora production and testing builds against Alfajores experience no issues and that e2e verification tests complete successfully</li>
        <li>Publish the release notes and tag the relevant commit on GitHub</li>
        <li>Tag released Docker image with <code>attestation-service-alfajores</code>, <code>attestation-service-baklava</code>, <code>attestation-service-mainnet</code>, and <code>attestation-service-vX.Y.Z</code> tags (removing tags from other releases)</li>
        <li>Inform the community of the new release via Discord and the Celo Forum</li>
      </ol>
    </td>
  </tr>
  <tr>
    <td>T+1w onwards</td>
    <td>
      <ol>
        <li>Confirm Mainnet services have upgraded without issues</li>
        <li>Continue monitoring dashboards for user issues</li>
      </ol>
    </td>
  </tr>
</table>

### Emergency Patches

Bugs which affect the security, stability, or core functionality of the Celo identity protocol or prevent new users onboarding to wallets including Valora may need to be released outside the standard release cycle. In this case, an emergency patch release should be created on top of all supported minor releases which contains the minimal change and corresponding test for the fix.

If the issue is not exploitable, release notes should describe the issue in detail and the image should be distributed publicly.

If the issue is exploitable and mitigations are not readily available, a patch should be prepared privately and signed binaries should be distributed from private commits. Establishing trust is key to pushing out the fix. An audit from a reputable third party may be contracted to verify the release to help earn that trust.

## Vulnerability Disclosure

Vulnerabilities in Attestation Service releases should be disclosed according to the [security policy](https://github.com/celo-org/celo-blockchain/blob/master/SECURITY.md).
