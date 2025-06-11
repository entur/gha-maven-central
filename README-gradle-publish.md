# `gha-maven-central/gradle-publish`

## Usage

Add the following step to your workflow configuration:

```yml
jobs:
  publish-release:
    name: Publish release to maven central
    uses: entur/gha-maven-central/.github/workflows/gradle-publish.yml@v1
    secrets: inherit
    
```

or

```yml
jobs:
  publish-snapshot:
    name: Publish snapshot to maven central
    uses: entur/gha-maven-central/.github/workflows/gradle-publish.yml@v1
    with:
        snapshot: true
        push_to_repo: false
    secrets: inherit
    
```

for snapshots

## Inputs

<!-- AUTO-DOC-INPUT:START - Do not remove or modify this section -->

|                                                     INPUT                                                      |  TYPE   | REQUIRED |        DEFAULT        |                                                                                                                      DESCRIPTION                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------|---------|----------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                             <a name="input_git_ref"></a>[git_ref](#input_git_ref)                              | string  |  false   |                       |                                                                                             Option to override git reference <br>to checkout before build                                                                                              |
|              <a name="input_java_distribution"></a>[java_distribution](#input_java_distribution)               | string  |  false   |     `"liberica"`      |                                                                                                            Java distribution for Setup-Java                                                                                                            |
|                      <a name="input_java_version"></a>[java_version](#input_java_version)                      | number  |  false   |         `21`          |                                                                                                                  Java Runtime Version                                                                                                                  |
|                      <a name="input_next_version"></a>[next_version](#input_next_version)                      | string  |  false   |       `"auto"`        | The next (semver) version of <br>the artifact. Increments can be <br>noop (''), automatically detected ('auto') <br>from the commit message, partially <br>incremented using ('major', 'minor', 'patch'), or be <br>forcefully defined (e.g. '1.3.0')  |
|                      <a name="input_push_to_repo"></a>[push_to_repo](#input_push_to_repo)                      | boolean |  false   |        `true`         |                                                                                                 Whether to push the new <br>version back to the repo                                                                                                   |
|                               <a name="input_runner"></a>[runner](#input_runner)                               | string  |  false   |   `"ubuntu-24.04"`    |                                                                                                                       Job Runner                                                                                                                       |
|                            <a name="input_snapshot"></a>[snapshot](#input_snapshot)                            | boolean |  false   |        `false`        |                                                                                                     Whether the artifact is a <br>snapshot or not                                                                                                      |
|              <a name="input_version_file_name"></a>[version_file_name](#input_version_file_name)               | string  |  false   | `"gradle.properties"` |                                                                                                The name of the file <br>containing the version number                                                                                                  |
| <a name="input_version_file_variable_name"></a>[version_file_variable_name](#input_version_file_variable_name) | string  |  false   |      `"version"`      |                                                                                             The name of the file <br>variable the previous version number                                                                                              |
|                <a name="input_version_strategy"></a>[version_strategy](#input_version_strategy)                | string  |  false   |       `"file"`        |                                                                                  Strategy to use for version <br>extraction. Currently supports "file" or <br>"tag".                                                                                   |
|             <a name="input_version_tag_prefix"></a>[version_tag_prefix](#input_version_tag_prefix)             | string  |  false   |         `"v"`         |                                                                                           The prefix of the tag <br>containing the previous version number                                                                                             |

<!-- AUTO-DOC-INPUT:END -->

## Golden Path


### Example