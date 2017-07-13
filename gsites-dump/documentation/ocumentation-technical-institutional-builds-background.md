## []()Introduction

With the release of v1.4 the build process for production systems has changed. For the sake of brevity I refer to the projects/artifacts as ReDBox, but the same applies equally for Mint as well.
## []()The Old Way

Previously the ReDBox POMs would

1.  list Fascinator as a dependency and deploy the required components into the filesystem
1.  then we would deploy all configuration, web content etc. (ie. 'resources' in a Java sense) around (or over) these
1.  the end result was a complete server ready to boot

So what was wrong with this? Well there were a number of issues starting to appear:

*  Each institution that wanted to use one or more of the servers with slight differences needed to follow an ever increasing amount of documented processes to 'undo' a default build and convert to what they wanted.
*  It was difficult for institutions to encapsulate with great ease how their setup differed from others, and usually ended up being simply a description of what was changed.
*  The pre-launch Maven project structure made it easy to have your system's/institutional identities detected by the Maven POM (ie. "I am being built on the prod server") but this was abstracted out at launch because it was difficult for packaged ZIPs to alter these values. The new build process allows for their return, and of course you can add as many site specific environments to the POM because it is YOUR build, not a generic one.
* Because all of the resources that made ReDBox what they were (point 2 above) where part of the top-level JAR project, there wasn't a lot of useful content submitted as a Maven artifact for downstream re-use by the 'release' process.
* Supporting a variety of different setups without a meaningful way of quickly swapping between them was becoming difficult.



## []()What Did We Change?

So the change process was fairly linear, with each change building the one before it. In order:

1.  All of the resources in the top-level project were moved into a new sub-project called '`redbox-config`'.
1.  The new sub-project became a dependency of the top-level project and the build scripts were altered to accommodate the difference between deploying a dependencies resources (versus local project resources).
1.  The top-level project was switch to a POM classifier... this is the key to early problem on 'useful content' getting sent to Maven Central. We were deploying our resources in a JAR with build scripts, but build scripts can't be run from the JAR classified project. We needed to separate into two projects, since the POM classifier wouldn't let us bundle the resources.
1.  The build scripts have been moved into a fully contained build profile to ensure that building the 'core' ReDBox project will no longer deploy a server. We just want them to inherit from the POM classified project into the our new builds.
1.  Rather then triggering the build profile with an ID (would require additional command line options from builders on institutional builds... on every build), we have triggered the profile based on the presence of a file ([deploy.server](https://github.com/redbox-mint/redbox-build-dev-local/blob/master/deploy.server)). This file is found in all current institutional builds... one of the reasons we suggest copying an existing build as a baseline.
1.  Next we moved to the new type of project; our institutional build, and the first thing to note here is that the institutional POM lists ReDBox as both a parent (to bring in the build scripts as the POM is parsed) and a dependency (to deploy our libraries and config).
1.  Generally, there isn't a problem with this, although you need to take care with variables like ${project.version}, and make sure they are coming from where you think they are coming from. During the migration this was particularly problematic because we were moving scripts between the two projects to find the best place for them to live.
1.  So now, the institutional builds takes shape. The dependency on ReDBox will bring in all the appropriate JARs/WARs etc. and because ReDBox's POM is also parsed as a parent we can let the core build scripts take care of deploying them all for us in a standard layout.
1.  Additional dependencies for specific curation models such as Handle, VITAL etc. are spread out across the institutional builds and removed from the core.
1.  The portion of the build scripts related to deploying local resources (ie. not from a dependency) are moved up into the institutional builds as well. For now most builds do not need all of these, but they are all still present, and the resources contain a skeleton folder layout for a complete server (mostly empty). This will make it much easier for later customisation.
1.  At the moment the institutional build deploys the resources from the the '`redbox-config`' project (before the local resources). I tried doing this from the core build script but ran into issues, and it proved easier to just put these in the institutional build. As the build evolves I'd like to centralise as much of this logic into the core as possible though.

## []()The New Way

So now, the basic order of events in a build now is:

1.    ReDBox (core) deploys the Fascinator platform and all binary dependencies, unpacking any resources required as well.
1.    The local build deploys the ReDBox config (the resources from the core) in a pre-canned layout across the top of Fascinator resources it wants to replace.
1.    The local build deploys it's own resources across the top in a matching layout. They can supplement or replace.

The packaging profile has also been moved to the institutional builds, and this should make it easier if institutions intend to only have one build environment. They can then just distribute the tarball amongst their servers.


End result? If you want to deploy a server you checkout your institutional build and type:

        mvn clean install


If you want to build and package up a server for deployment elsewhere you type:

        mvn -P build-package clean install