## []()Introduction

When working on ReDBox or Mint it is handy to deploy snapshots on a regular basis. This helps other projects using our Maven artifacts and keeps up with the continuous release goodness.
Please refer to [OSS Maven Repository Usage Guide](https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide) for an overview of the Sonatype OSS Maven Repository infrastructure. 

*  If you are going to release anything you will need a login for this service and a GPG key.
*  Once you have a login for https://issues.sonatype.org you need to visit https://issues.sonatype.org/browse/OSSRH-1609 and ask them to give you access to the project.

See also: [https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/deploying-to-maven](https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/deploying-to-maven)
## []()Details

### []()0. Start

It's important that you check in any changes and update your code-base. 
Add the sonatype nexus repo to your settings.xml:


        <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                              http://maven.apache.org/xsd/settings-1.0.0.xsd">
            <servers>
                <server>
                    <id>sonatype-nexus-snapshots</id>
                    <username>XXXXX</username>
                    <password>XXXXX</password>
                </server>
                <server>
                    <id>sonatype-nexus-staging</id>
                    <username>XXXXX</username>
                    <password>XXXXX</password>
                </server>
            </servers>
        </settings>


### []()1. Deploy

This is pretty easy:


        mvn clean
        mvn deploy


Because of our project structure, clean may have a problem but just run deploy and you'll be right.
If you're working on a SNAPSHOT and that's all you want to deploy, STOP HERE. The rest if for a tagged release.
### []()2. Release

You MUST ensure that any dependencies are for released versions and not SNAPSHOTS. This may mean you have to release artifacts from other code bases (such as The Fascinator). 
When you've sorted out dependencies you can start the release procedure below:


        mvn release:clean
        mvn release:prepare
        mvn release:perform


## []()Finding deployed code


* Snapshots: [https://oss.sonatype.org/content/repositories/snapshots/com/googlecode/redbox-mint/](https://oss.sonatype.org/content/repositories/snapshots/com/googlecode/redbox-mint/)
* Release staging area: [https://oss.sonatype.org/content/groups/staging/com/googlecode/redbox-mint/](https://oss.sonatype.org/content/groups/staging/com/googlecode/redbox-mint/)
* Maven Central: [http://search.maven.org/#browse|808788309](http://search.maven.org/#browse%7C808788309)

## []()Institutional Builds

Institutional builds do not need to be released per se, but you can use '`mvn release:prepare`' to generate tagged branches in subversion for you.