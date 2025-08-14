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

for snapshots.

Afterwards, you must also configure JReleaser in your gradle.build file:

```groovy
plugins {
    id 'java-library'
    id 'maven-publish'
    id 'org.jreleaser' version '1.18.0'
}

java {
    withJavadocJar()
    withSourcesJar()
}

ext.stagingRepositoryPath = "${rootDir.getCanonicalFile()}/build/staging-deploy"

publishing {
    publications {
        mavenJava(MavenPublication) {
            from components.java
            artifact sourcesJar
            artifact javadocJar

            groupId = project.group
            artifactId = project.name
            version = project.version

            pom {
                name = project.name
                description = "YOUR DESCRIPTION"
                packaging = 'jar'

                url = "https://github.com/entur/${project.name}"

                scm {
                    connection = "scm:git:https://github.com/entur/${project.name}.git"
                    developerConnection = "scm:git:https://github.com/entur/${project.name}.git"
                    url = "https://github.com/entur/${project.name}"
                }

                licenses {
                    license {
                        name = "European Union Public Licence v. 1.2"
                        url = "https://www.eupl.eu/"
                    }
                }

                developers {
                    developer {
                        id = "someid"
                        name = "yourname"
                        email = "youremail@entur.org"
                    }
                }
            }
        }
    }

    repositories {
        maven {
            url = stagingRepositoryPath
        }
    }
}

jreleaser {
    signing {
        active = 'ALWAYS'
        armored = true
    }
    deploy {
        maven {
            mavenCentral {
                release {
                    active = 'RELEASE'
                    url = 'https://central.sonatype.com/api/v1/publisher'
                    stagingRepository(stagingRepositoryPath)
                }
            }
            nexus2 {
                snapshot {
                    active = 'SNAPSHOT'
                    applyMavenCentralRules = true
                    snapshotSupported = true
                    closeRepository = true
                    releaseRepository = true
                    url = "https://ossrh-staging-api.central.sonatype.com/service/local"
                    snapshotUrl = 'https://central.sonatype.com/repository/maven-snapshots/'
                    stagingRepository(stagingRepositoryPath)
                }
            }
        }
    }
    release {
        github {
            skipTag = false // Skip creating a new github tag?
            skipRelease = true // Skip creating a new github release?
        }
    }
}

```

## Multi-module projects
Do not configure individual submodules. In the root build file, go with

```
id 'org.jreleaser' version '1.18.0' apply false
```

then

```
task clean {
}

apply plugin: 'org.jreleaser'
```

before the `jreleaser` block.

## Inputs

<!-- AUTO-DOC-INPUT:START - Do not remove or modify this section -->

|                                                     INPUT                                                      |  TYPE   | REQUIRED |        DEFAULT        |                                                                                                                      DESCRIPTION                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------|---------|----------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                             <a name="input_git_ref"></a>[git_ref](#input_git_ref)                              | string  |  false   |                       |                                                                                             Option to override git reference <br>to checkout before build                                                                                              |
|                       <a name="input_git_ssh_key"></a>[git_ssh_key](#input_git_ssh_key)                        | string  |  false   |                       |                                                                                              Option to override git ssh <br>key to checkout before build                                                                                               |
|              <a name="input_java_distribution"></a>[java_distribution](#input_java_distribution)               | string  |  false   |     `"liberica"`      |                                                                                                            Java distribution for Setup-Java                                                                                                            |
|                      <a name="input_java_version"></a>[java_version](#input_java_version)                      | number  |  false   |         `21`          |                                                                                                                  Java Runtime Version                                                                                                                  |
|                      <a name="input_next_version"></a>[next_version](#input_next_version)                      | string  |  false   |       `"auto"`        | The next (semver) version of <br>the artifact. Increments can be <br>noop (''), automatically detected ('auto') <br>from the commit message, partially <br>incremented using ('major', 'minor', 'patch'), or be <br>forcefully defined (e.g. '1.3.0')  |
|                      <a name="input_push_to_repo"></a>[push_to_repo](#input_push_to_repo)                      | boolean |  false   |        `true`         |                                                                                                 Whether to push the new <br>version back to the repo                                                                                                   |
|                               <a name="input_runner"></a>[runner](#input_runner)                               | string  |  false   |   `"ubuntu-24.04"`    |                                                                                                                       Job Runner                                                                                                                       |
|                            <a name="input_snapshot"></a>[snapshot](#input_snapshot)                            | boolean |  false   |        `false`        |                                                                                                     Whether the artifact is a <br>snapshot or not                                                                                                      |
|              <a name="input_version_file_name"></a>[version_file_name](#input_version_file_name)               | string  |  false   | `"gradle.properties"` |                                                                                                The name of the file <br>containing the version number                                                                                                  |
| <a name="input_version_file_variable_name"></a>[version_file_variable_name](#input_version_file_variable_name) | string  |  false   |      `"version"`      |                                                                                             The name of the file <br>variable the previous version number                                                                                              |
|                <a name="input_version_strategy"></a>[version_strategy](#input_version_strategy)                | string  |  false   |       `"file"`        |                                                                                  Strategy to use for version <br>extraction. Currently supports "file" or <br>"tag".                                                                                   |
|             <a name="input_version_tag_prefix"></a>[version_tag_prefix](#input_version_tag_prefix)             | string  |  false   |         `"v"`         |                                                                     The prefix of the tag <br>containing the previous version number <br>to extract (used with the tag strategy)                                                                       |

<!-- AUTO-DOC-INPUT:END -->

## Golden Path


### Example: Mono repository where modules are deployed together
This setup demonstrates how to publish all modules in a Gradle multi-module (mono) repository in a single release, using JReleaser for deployment to Maven Central and GitHub Releases.

### ./gradle.properties
```
group=org.entur.myArtifact
version=1.0.0-SNAPSHOT
```

- group – Maven groupId for all modules in the repository.
- version – The current project version, using -SNAPSHOT for development builds.

#### ./build.gradle  (root project)
Configures JReleaser to handle signing, staging, and deployment for all modules.

``` properties
plugins {
    id 'base'                             // Provides lifecycle tasks like 'assemble' and 'check'
    id 'org.jreleaser' version "1.19.0"   // Handles packaging and publishing
}

jreleaser {
    signing {
        active = 'ALWAYS'  // Always sign artifacts
        armored = true     // Use ASCII-armored signatures
    }
    deploy {
        maven {
            // Maven Central release deployment configuration
            mavenCentral {
                release {
                    active = 'RELEASE'
                    url = 'https://central.sonatype.com/api/v1/publisher'
                    stagingRepository('build/staging-deploy')
                }
            }
            
            // Nexus 2 snapshot deployment configuration
            nexus2 {
                snapshot {
                    active = 'SNAPSHOT'
                    applyMavenCentralRules = true
                    snapshotSupported = true
                    closeRepository = true
                    releaseRepository = true
                    url = "https://ossrh-staging-api.central.sonatype.com/service/local"
                    snapshotUrl = 'https://central.sonatype.com/repository/maven-snapshots/'
                    stagingRepository('build/staging-deploy')
                }
            }
        }
    }
    release {
        github {
            enabled = true
        }
    }
}
```

Key points:
- mavenCentral block is for releases. 
- nexus2 block is for snapshots. 
- Both use the same local staging directory (build/staging-deploy) where artifacts are assembled before upload.

#### ./module/build.gradle (per module)
Configures the module to produce JARs, sources, and Javadoc for publishing.

```
plugins {
    id 'java-library'   // Java library conventions
    id 'maven-publish'  // Enables Maven publishing tasks
}

// Build a JAR with sources
tasks.register('sourcesJar', Jar) {
    dependsOn classes
    archiveClassifier.set('sources')
    from sourceSets.main.allSource
}

// Build a JAR with Javadoc
tasks.register('javadocJar', Jar) {
    dependsOn javadoc
    archiveClassifier.set('javadoc')
    from javadoc.destinationDir
}

publishing {
    publications {
        mavenJava(MavenPublication) {
            from components.java
            artifact sourcesJar
            artifact javadocJar

            groupId = project.group
            artifactId = project.name
            version = project.version

            pom {
                name = project.name
                description = "A Java for *****."  // Replace with real description
                packaging = 'jar'

                url = "https://github.com/entur/${rootProject.name}"

                scm {
                    connection = "scm:git:https://github.com/entur/${rootProject.name}.git"
                    developerConnection = "scm:git:https://github.com/entur/${rootProject.name}.git"
                    url = "https://github.com/entur/${rootProject.name}"
                }

                licenses {
                    license {
                        name = "European Union Public Licence v. 1.2"
                        url = "https://www.eupl.eu/"
                    }
                }

                developers {
                    developer {
                        id = "myId"                   // Replace with real GitHub id
                        name = "MyName"               // Replace with real name
                        email = "MyName@entur.org"    // Replace with real email
                        }
                    }
            }
        }
    }

    // Local staging directory; JReleaser picks this up for deployment
    repositories {
        maven {
            url = rootProject.layout.buildDirectory.dir("staging-deploy")
        }
    }
}
```

Key points:
- Each module produces:
  - Main JAR (.jar)
  - Sources JAR (-sources.jar)
  - Javadoc JAR (-javadoc.jar)
- repositories points to the same staging directory used in the root JReleaser config so all module artifacts are collected together.

#### .github/workflows/publish.yaml
GitHub Actions workflow to manually trigger a release.

```
name: Publish Release to Sonatype
on:
  workflow_dispatch:
    inputs:
      next_version:
        description: "Version bump"
        required: false
        type: choice
        options:
          - major
          - minor
          - patch
        default: "patch"

jobs:
  publish:
    name: Publish Release to Maven Central
    uses: entur/gha-maven-central/.github/workflows/gradle-publish.yml@v1
    with:
      next_version: ${{ inputs.next_version }}
      version_file_name: "gradle.properties"
    secrets: inherit
```

Key points:
- Triggered manually via workflow_dispatch in GitHub Actions. 
- Calls a shared Gradle publish workflow (entur/gha-maven-central). 
- Supports semantic version bumping (major, minor, patch). 
- Uses repository secrets for signing keys, OSSRH credentials, and GitHub authentication.
