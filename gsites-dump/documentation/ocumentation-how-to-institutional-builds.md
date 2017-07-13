[[_TOC_]]


## []()Status

This document is in DRAFT.
## []()Introduction

Institutional Builds are the way to make customisations for your Redbox/Mint implementation and creating one is the first step to moving from a Demo/Evaluation installation to one that will be suitable for your needs in production. Institutional Builds allow you to configure/customise:

* re-brand the application through modifying CSS and templates
* configure plugins
* make general configuration changes 
* create custom harvests and mappings
* much much more!

## []()Background reading


* [Maven: The Complete Reference](http://www.sonatype.com/books/mvnref-book/reference/) in particular the [Project Dependencies](http://www.sonatype.com/books/mvnref-book/reference/pom-relationships-sect-project-dependencies.html) and [Build Profiles](http://www.sonatype.com/books/mvnref-book/reference/profiles.html) chapters

## []()Procedure

Before you start this procedure, please make sure that you have [Apache Maven installed correctly.](http://maven.apache.org/download.html#Installation) 
### []()1. Step 1 Take a copy of an existing demonstration institutional build

There is a fair bit of configuration in these institutional build pom files that is needed to make it work. Ideally we would set up an archetype for institutional builds but at this stage the best way to get this configuration is to get this is to take a copy of one of an [existing demonstration build](https://code.google.com/p/redbox-mint/source/browse/#svn%2Fredbox%2Fbuilds.), any archetype that we would create would most likely be very similar to the dev-local build. To take a copy, clone the git repository and then remove the .git directory in the root directory of the tree.


        git clone https://github.com/redbox-mint/mint-build-dev-local.git
        cd mint-build-dev-local
        rm .git/

### []()2. Step 2 Edit the Maven Project Details

The next step is to modify the Maven Project Details, it is important to at least change the groupId, artifactId, version and scm (source code management) values to ones that are relevant to your institutional build as they are used heavily when building, releasing and deploying your project. There are a lot of additional tags that are used mainly for metadata (name tag is an example) that you may also like to edit



        <groupId>au.edu.examples</groupId>
        <artifactId>redbox-uni-of-examples</artifactId>
        <version>1.0-SNAPSHOT</version>

         <scm>
                <url>https://svn.examples.edu.au/redbox/redbox-uni-of-examples</url>
                <connection>scm:svn:http://svn.examples.edu.au/redbox/redbox-uni-of-examples</connection>
                <developerConnection>scm:svn: https://svn.examples.edu.au/redbox/redbox-uni-of-examples</developerConnection>
         </scm>
        

In our demonstration builds, our version always matches the version of redbox/mint we are working with. This is because they are sample builds that rarely have changes in them apart from updating the redbox/mint version it is using. In a real world scenario, this institutional build is your development project and as such may have a life of it's own that is somewhat separate to what is happening to the core of the code. For this reason, we recommend using your own version number system (as I have done in the above sample code). [Here's an explanation of Maven versioning](http://www.sonatype.com/books/mvnref-book/reference/pom-relationships-sect-pom-syntax.html#pom-reationships-sect-versions) 


You may like to consider various plugins for use in your build. Check out our [Plugin Directory](documentation-system-administration-plugin-directory) for a list of available plugins.

### []()3. Step 3 Make changes to files to customise your instance

There are so many different things you can customise in your ReDBox/Mint instance, I will only cover it in general terms here. For more in depth information on customising specific aspects of the system, check the [How-to](documentation-how-to) section regularly. Looking at the project structure you can see that it follows the same structure as what is outputted post build but with only the files that have been customised.


Institutional Build Structure

[![Institutional Build Structure](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1346218339604/documentation/how-to/institutional-builds/institutional%20build%20structure.png)
](documentation-how-to-institutional-builds-institutional%20build%20structure-png?attredirects=0)

Customising is as simple as grabbing the file you want to change and putting it in the equivalent place in your institutional build structure and making the modifications. New files that appear in this tree structure will also appear in your final build.



### []()4. Step 4 Build!

To build your institutional build, simply type at the command prompt: 
        mvn clean package
        

This will build the application and output all the directories ready for you to run it. This is fine for development, but what if you are building the application on a build server to deploy to testing servers or production? In the next step, we discuss how to manage this.



### []()5. Step 5 Building for different environments

The command given in step 4 will build the application and output all the directories in place, but what if you need to deploy to another server? The institutional builds are set up with a custom profile that you can specify if you want to build a tarball in your target directory (.tar.gz file) that you can upload to a server (or Nexus as is discussed in Step 7). The command to type at the command prompt is:
mvn clean package -P `build-package`
        

Taking this one step further, your test servers and production server are almost certainly going to require different configuration to run correctly once deployed. Changing the values of these properties once you've extracted the tarball on the server is one way of handling this but is error prone and means your deployments aren't guaranteed to be "repeatable". A better way to handle this situation is to create profiles in your pom file for each environment and specify them at build time.
  

        <profile>
                <id>uat</id>
                <properties>
                       <redbox.location>/opt/redbox</redbox.location>
                       <server.url.base>http://uat.examples.edu.au/redbox/</server.url.base>
                </properties>
        </profile>
        

Once we have a profile set up as above, the command to type at the command prompt is:
 mvn clean package -P uat,`build-package`



### []()6. Step 6 Performing releases
Institutional builds work with the [Maven release plugin](http://maven.apache.org/plugins/maven-release-plugin/) and is the recommended method of performing releases.



### []()7. Step 7 Using Sonatype Nexus with Institutional Builds

[Sonatype Nexus](http://www.sonatype.org/nexus/) is a Maven artifact repository. The build artifacts that institutional builds produce can be deployed to such a repository. This can be a very powerful way to store and manage your build artifacts as:


* Snapshots versions that are deployed are time-stamped. This especially useful if you have set up a Continuous Integration environment to deploy artifacts regularly.
* Release versions are also catalogued
* The artifacts can then be downloaded via http on deployment servers so deployment scripts can be a little more powerful

To configure your institutional build you need to specify the repositories in the distributionManagement area of your pom file

        <distributionManagement>
                        <snapshotRepository>
                                <id>redbox-nexus-snapshots</id>
                                <name>Redbox Nexus Snapshots</name>
                                <url>http://dev.redboxresearchdata.com.au/nexus/content/repositories/snapshots/</url>
                        </snapshotRepository>
                        <repository>
                                <id>redbox-nexus-releases</id>
                                <name>Redbox Nexus Releases</name>
                                <url>http://dev.redboxresearchdata.com.au/nexus/content/repositories/releases/</url>
                        </repository>
        </distributionManagement>
        

Then specify the username and password in the servers are of your maven settings.xml file

        <servers>
                ` <server>`
                    <id>redbox-nexus-snapshots</id>
                    <username>deploymentUserNameInNexus</username>
                    <password>whateverYourPasswordIs</password>
                </server>
                <server>
                    <id>redbox-nexus-releases</id>
                    <username> `deploymentUserNameInNexus`` ``</username>`
                    <password> `whateverYourPasswordIs`` ``</password>`
                </server>
        </servers>
        

If everything is configured correctly, you can use the Maven deploy plugin to deploy the artifact to nexus.


For a snapshot build:
        mvn clean package deploy -P <environmentProfile>,build-package
For a release build:
        mvn release:prepare release:perform deploy -P <environmentProfile>,build-package 

### []()Finishing up

This How-to only really covers the basics of Institutional Builds as well as some strategies to help you work with it through your Software Development Life-cycle. Please see the other How-to's for more ideas on how to customise your build.


The [Institutional Builds](documentation-technical-institutional-builds) page delved into the subject to a greater depth.