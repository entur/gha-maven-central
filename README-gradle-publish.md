# `gha-maven-central/gradle-publish`

## Usage

Add the following step to your workflow configuration:

```yml
jobs:
  publish:
    name: Publish to docker
    uses: entur/gha-maven-central/.github/workflows/gradle-publish.yml@v1
    secrets: inherit
    
```

or

```yml
jobs:
  publish:
    name: Publish to docker
    uses: entur/gha-maven-central/.github/workflows/gradle-publish.yml@v1
    with:
        snapshot: true
        push_to_repo: false
    secrets: inherit
    
```

for snapshots

## Inputs

<!-- AUTO-DOC-INPUT:START - Do not remove or modify this section -->
<!-- AUTO-DOC-INPUT:END -->

## Golden Path


### Example