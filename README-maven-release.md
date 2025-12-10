# `gha-maven-central/maven-release`

## Overview

This is a standalone workflow for publishing Maven artifacts to Maven Central with custom versions. Use this workflow when you want to manage versioning yourself and only need the publishing functionality.

## When to Use This Workflow

Use `maven-release.yml` when:
- You manage your own versioning strategy
- You want to publish a specific version without automatic version incrementing
- You need more control over the release process
- You want to separate version management from the publishing step

## Usage

### Basic Usage

```yml
jobs:
  publish-release:
    name: Publish to Maven Central with JReleaser
    uses: entur/gha-maven-central/.github/workflows/maven-release.yml@v1
    with:
      version: "1.3.0"  # Required: specify the version to publish
    secrets: inherit
```

### Snapshot Release

```yml
jobs:
  publish-snapshot:
    name: Publish snapshot to Maven Central
    uses: entur/gha-maven-central/.github/workflows/maven-release.yml@v1
    with:
      version: "1.3.0-SNAPSHOT"
      snapshot: true
    secrets: inherit
```

### Advanced Usage

```yml
jobs:
  publish-custom:
    name: Publish with custom configuration
    uses: entur/gha-maven-central/.github/workflows/maven-release.yml@v1
    with:
      version: "1.3.0"
      java_version: 17
      java_distribution: "temurin"
      skip_version_update: true  # Use this if version is already set in pom.xml
    secrets: inherit
```

## Inputs

| INPUT | TYPE | REQUIRED | DEFAULT | DESCRIPTION |
|-------|------|----------|---------|-------------|
| `runner` | string | false | `"ubuntu-24.04"` | Job Runner |
| `git_ref` | string | false | | Option to override git reference to checkout before build |
| `settings-xml_file` | string | false | | Link to the external settings.xml file |
| `java_version` | number | false | `21` | Java Runtime Version |
| `java_distribution` | string | false | `"liberica"` | Java distribution for Setup-Java |
| `version` | string | **true** | | **Required**: The version to release (e.g., '1.3.0' or '1.3.0-SNAPSHOT') |
| `version_file_name` | string | false | `"pom.xml"` | The name of the file containing the version number |
| `version_tag_prefix` | string | false | `"v"` | The prefix for the git tag (e.g., 'v' for 'v1.0.0') |
| `snapshot` | boolean | false | `false` | Whether the artifact is a snapshot or not |
| `skip_version_update` | boolean | false | `false` | Skip setting version in pom.xml (useful if version is already set) |

## Secrets

The following secrets are required:

| SECRET | REQUIRED | DESCRIPTION |
|--------|----------|-------------|
| `GIT_SSH_KEY` | false | Option to override git ssh key to checkout before build |
| `SONATYPE_AUTH_USER` | **true** | Sonatype username for Maven Central |
| `SONATYPE_AUTH_TOKEN` | **true** | Sonatype token for Maven Central |
| `SONATYPE_GPG_KEY_PUBLIC` | **true** | Base64-encoded GPG public key |
| `SONATYPE_GPG_KEY` | **true** | Base64-encoded GPG private key |
| `SONATYPE_GPG_KEY_PASSWORD` | **true** | GPG key password |

## Outputs

| OUTPUT | DESCRIPTION |
|--------|-------------|
| `version` | Published version |

## Complete Example Workflow

Here's a complete example showing how to use this workflow in your repository:

```yml
name: Release to Maven Central

on:
  workflow_dispatch:
    inputs:
      release_version:
        description: 'Version to release (e.g., 1.3.0)'
        required: true
        type: string

jobs:
  release:
    name: Release version ${{ inputs.release_version }}
    uses: entur/gha-maven-central/.github/workflows/maven-release.yml@v1
    with:
      version: ${{ inputs.release_version }}
    secrets: inherit
```

## Comparison with maven-publish.yml

| Feature | maven-publish.yml | maven-release.yml |
|---------|-------------------|----------------------------|
| Automatic version detection | ✅ Yes | ❌ No (manual input) |
| Automatic version increment | ✅ Yes | ❌ No |
| Version push to repo | ✅ Yes | ❌ No |
| JReleaser stage & release | ✅ Yes | ✅ Yes |
| Control over version | ❌ Limited | ✅ Full control |
| Use case | Automated releases | Manual/controlled releases |

## Architecture

Both workflows use a shared composite action (`.github/actions/jreleaser-release`) for JReleaser operations, ensuring consistency and eliminating code duplication.

## JReleaser Configuration

Ensure your `pom.xml` is configured with JReleaser as shown in the [main documentation](README-maven-publish.md#jreleaser-configuration).