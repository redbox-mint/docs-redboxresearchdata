When we build Mint and/or ReDBox for public release a demonstration build needs to be bundled as a tarball file for inclusion in [Downloads](downloads). This page outlines the process using the 'dev-local' build.
## []()Clean Checkout

First of all we need to ensure that you perform this against a clean checkout of the code. The developer build deploys a server around the source directory that you can then boot and use, and we don't want to get any of the files created by, and related to, your development server whilst it was running.
So step one is to create a new directory somewhere and checkout a copy of the source used only for this purpose. I typically label it as such, eg.:

        git clone https://github.com/redbox-mint/redbox-build-dev-local.git
## []()Neutral Configuration

The normal build process will try to find your IP address and insert this value into the configuration for the server. We don't want this to occur when building the tarball however, so we will use the 'build-package' build profile.
More importantly, this build profile is also configured to build our tarball...

        mvn -P build-package clean install
## []()Uploading the Files

The development team make these files available via our Nexus repository: [http://dev.redboxresearchdata.com.au/nexus/content/repositories/releases/com/googlecode/redbox-mint/](http://dev.redboxresearchdata.com.au/nexus/content/repositories/releases/com/googlecode/redbox-mint/)