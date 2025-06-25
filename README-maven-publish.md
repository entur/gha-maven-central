# `gha-maven-central/maven-publish`

## Usage

Add the following step to your workflow configuration:

```yml
jobs:
  publish-release:
    name: Publish release to maven central
    uses: entur/gha-maven-central/.github/workflows/maven-publish.yml@v1
    secrets: inherit
    
```

or

```yml
jobs:
  publish-snapshot:
    name: Publish snapshot to maven central
    uses: entur/gha-maven-central/.github/workflows/maven-publish.yml@v1
    with:
        snapshot: true
        push_to_repo: false
    secrets: inherit
    
```

for snapshots.

Afterwards, you must also configure JReleaser in your pom.xml file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.acme</groupId>
    <artifactId>app</artifactId>
    <version>1.0.0</version>

    <name>app</name>
    <description>Sample application</description>
    <url>https://github.com/entur/yourapp</url>
    <inceptionYear>2021</inceptionYear>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.release>11</maven.compiler.release>
    </properties>

    <licenses>
        <license>
            <name>European Union Public License v. 1.2</name>
            <url>https://www.eupl.eu/</url>
        </license>
    </licenses>

    <developers>
        <developer>
            <id>yourid</id>
            <name>yourname</name>
        </developer>
    </developers>

    <scm>
        <connection>scm:git:https://github.com/aalmiray/app.git</connection>
        <developerConnection>scm:git:https://github.com/aalmiray/app.git</developerConnection>
        <url>https://github.com/aalmiray/app.git</url>
        <tag>HEAD</tag>
    </scm>

    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-deploy-plugin</artifactId>
                    <version>3.1.1</version>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.13.0</version>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-javadoc-plugin</artifactId>
                    <version>3.6.3</version>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-source-plugin</artifactId>
                    <version>3.3.1</version>
                </plugin>
                <plugin>
                    <groupId>org.jreleaser</groupId>
                    <artifactId>jreleaser-maven-plugin</artifactId>
                    <version>1.18.0</version>
                </plugin>
            </plugins>
        </pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.jreleaser</groupId>
                <artifactId>jreleaser-maven-plugin</artifactId>
                <inherited>false</inherited>
                <configuration>
                    <jreleaser>
                        <signing>
                            <active>ALWAYS</active>
                            <armored>true</armored>
                        </signing>
                        <deploy>
                            <maven>
                                <mavenCentral>
                                    <sonatype>
                                        <active>RELEASE</active>
                                        <url>https://central.sonatype.com/api/v1/publisher</url>
                                        <stagingRepositories>target/staging-deploy</stagingRepositories>
                                    </sonatype>
                                </mavenCentral>
                                <nexus2>
                                    <maven-central>
                                        <active>SNAPSHOT</active>
                                        <url>https://ossrh-staging-api.central.sonatype.com/service/local</url>
                                        <snapshotUrl>https://central.sonatype.com/repository/maven-snapshots</snapshotUrl>
                                        <applyMavenCentralRules>true</applyMavenCentralRules>
                                        <snapshotSupported>true</snapshotSupported>
                                        <closeRepository>true</closeRepository>
                                        <releaseRepository>true</releaseRepository>
                                        <stagingRepositories>target/staging-deploy</stagingRepositories>
                                    </maven-central>
                                </nexus2>
                            </maven>
                        </deploy>
                        <release>
                            <github>
                                <skipTag>false</skipTag>
                                <skipRelease>true</skipRelease>
                            </github>   
                        </release>
                    </jreleaser>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <profiles>
        <profile>
            <id>publication</id>
            <properties>
                <altDeploymentRepository>local::default::file:./target/staging-deploy</altDeploymentRepository>
            </properties>
            <build>
                <defaultGoal>deploy</defaultGoal>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-javadoc-plugin</artifactId>
                        <executions>
                            <execution>
                                <id>attach-javadocs</id>
                                <goals>
                                    <goal>jar</goal>
                                </goals>
                                <configuration>
                                    <attach>true</attach>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-source-plugin</artifactId>
                        <executions>
                            <execution>
                                <id>attach-sources</id>
                                <goals>
                                    <goal>jar</goal>
                                </goals>
                                <configuration>
                                    <attach>true</attach>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>
</project>

```

## Inputs

<!-- AUTO-DOC-INPUT:START - Do not remove or modify this section -->

|                                         INPUT                                          |  TYPE   | REQUIRED |               DEFAULT               |                                                                                                                      DESCRIPTION                                                                                                                       |
|----------------------------------------------------------------------------------------|---------|----------|-------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                 <a name="input_git_ref"></a>[git_ref](#input_git_ref)                  | string  |  false   |                                     |                                                                                             Option to override git reference <br>to checkout before build                                                                                              |
|  <a name="input_java_distribution"></a>[java_distribution](#input_java_distribution)   | string  |  false   |            `"liberica"`             |                                                                                                            Java distribution for Setup-Java                                                                                                            |
|          <a name="input_java_version"></a>[java_version](#input_java_version)          | number  |  false   |                `21`                 |                                                                                                                  Java Runtime Version                                                                                                                  |
|          <a name="input_next_version"></a>[next_version](#input_next_version)          | string  |  false   |              `"auto"`               | The next (semver) version of <br>the artifact. Increments can be <br>noop (''), automatically detected ('auto') <br>from the commit message, partially <br>incremented using ('major', 'minor', 'patch'), or be <br>forcefully defined (e.g. '1.3.0')  |
|          <a name="input_push_to_repo"></a>[push_to_repo](#input_push_to_repo)          | boolean |  false   |               `true`                |                                                                                                 Whether to push the new <br>version back to the repo                                                                                                   |
|                   <a name="input_runner"></a>[runner](#input_runner)                   | string  |  false   |          `"ubuntu-24.04"`           |                                                                                                                       Job Runner                                                                                                                       |
|  <a name="input_settings-xml_file"></a>[settings-xml_file](#input_settings-xml_file)   | string  |  false   | `"https://link-to-my-settings.xml"` |                                                                                                      Link to the external settings.xml <br>file                                                                                                        |
|                <a name="input_snapshot"></a>[snapshot](#input_snapshot)                | boolean |  false   |               `false`               |                                                                                                     Whether the artifact is a <br>snapshot or not                                                                                                      |
|  <a name="input_version_file_name"></a>[version_file_name](#input_version_file_name)   | string  |  false   |             `"pom.xml"`             |                                                                                                The name of the file <br>containing the version number                                                                                                  |
|    <a name="input_version_strategy"></a>[version_strategy](#input_version_strategy)    | string  |  false   |              `"file"`               |                                                                                  Strategy to use for version <br>extraction. Currently supports "file" or <br>"tag".                                                                                   |
| <a name="input_version_tag_prefix"></a>[version_tag_prefix](#input_version_tag_prefix) | string  |  false   |                `"v"`                |                                                                     The prefix of the tag <br>containing the previous version number <br>to extract (used with the tag strategy)                                                                       |

<!-- AUTO-DOC-INPUT:END -->

## Golden Path


### Example
