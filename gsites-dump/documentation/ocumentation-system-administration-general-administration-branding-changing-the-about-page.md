### []()Let's start off by changing the About page and adding some text that describes our Mint installation. Before editing anything please take a look at the About page that ships with Mint:


1. Start Mint
1. Browse to the About page
1. Login as the administrator

You'll notice that a new section is displayed to the administrator - a set of tabs describing the plugins used in the system. This is an important consideration when editing the portal files - you must test to make sure that the user-interface is working as expected for both anonymous and logged-in users!


Once you've had a look at the About page it's time to start editing:

1. Open the `portal/``default/mint/` directory 
1. Copy the `about.vm` file
1. Paste the file into `portal/``default/local/ `
1. Open the newly pasted `about.vm` in a text editor

For anyone that's used Velocity templates before, this should look familiar - there's a lot of HTML with some template logic interspersed. Locate the sections listed in the "Original" column and alter them to match their respective "Change" value.


*** Original******Change ***`#set($pageTitle = "About")`` #set($pageTitle = "About this system")``#set($contentTitle = "Description")`` #set($contentTitle = "Welcome to the Uni X Mint system")`
        <div class="article first">
          The Fascinator is an extensible, open-source platform for managing your digital objects. Using a variety of plugins, you can transform your digital objects into new formats, search and browse through your collection, collaborate through tagging and annotations, and create packages of information for publishing to all manner of systems.
          See our <a href="https://sites.google.com/site/fascinatorhome/">website</a> for more information.
        </div>
        <div class="article first">
          This system is operated by Uni X
        </div>


Checking for administrator
In` about.vm` you may have noticed the following code line:

        #if($page.authentication.is_admin())


This is a very handy directive - it tests if the user is an administrator and, if so, will display the plugin list. It's an important bit of code to remember, especially if you're about to start changing lots of details.
#### **[]()Changing the other pages

You can change the other pages in the system in much the same way you just edited the About page. The best starting place for finding the files you need is in `portal/``default/default/ `- just copy these to your local skin and start editing:

* `layout.vm: `starts the page layout - you can trace which templates are used
* `home.vm`: is the home page layout
* `detail.vm`: is used to present details about an object in the repository