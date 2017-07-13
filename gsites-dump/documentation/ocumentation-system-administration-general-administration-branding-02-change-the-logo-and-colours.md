### []()If your ReDBox and/or Mint system are to be publicly accessible you're probably going to want to brand the system with your corporate template. This can be as easy as changing the logo and some colours or be a full reformatting of the Mint/ReDBox interfaces.





1. Open the `portal/``default/mint/` directory 
1. Copy the `layout` directory
1. Paste the directory into `portal/``default/local/ `
1. Open the newly pasted `layout` directory

You'll see 3 files in the `layout` directory:

* `header-branding.vm`: This is the file you need for changing the logo
* `html-head-links.vm`: Sets the HTML link tags - this is really important as it points to the CSS file
* `html-head-title.vm`: Sets the title of the web page - this can be useful if you need some fixed text in the tile

Let's change the header branding in header-branding.vm:


***Original******Change***

        <div class="grid_16 branding">
          <h1 id="branding"><a href="$portalPath/home">The Mint</a></h1>
          <div class="subtitle">Linked Authority Control Service</div>
        </div>
        <div class="clear"></div>
<div class="grid_16 branding">
  <h1 id="branding">
    <a href="$portalPath/home">
    <img src="http://www.openclipart.org/image/800px/svg_to_png/1316090534.png" height="150"/>
    Uni X - Mint</a></h1>
  <div class="subtitle">We know that you know that we know</div>
</div>
<div class="clear"></div>

Now we need to change our colour scheme to better match our new logo:

1. Open the `portal/``default/mint/` directory 
1. Copy the `css `directory
1. Paste the directory into `portal/``default/local/ `
1. Open the newly pasted `css `directory
1. Change the file named `mint.css` to `local.css`

In order to use local.css we'll need to edit `portal/``default/local/layout/`html-head-links.vm: 



 ***Original******Change ***`<link rel="stylesheet" type="text/css" href="$portalPath/css/mint.css" />``<link rel="stylesheet" type="text/css" href="$portalPath/css/local.css" />`


Now that we're pointing to the correct CSS file, open `portal/``default/local/css/local.css `and make some changes:
***Original******Change***

        h1#branding, .branding {
            background:none repeat scroll 0 0 #C5DAAF;
            padding-bottom:0;
        }
        h1#branding, .branding {
            background:none repeat scroll 0 0 #FFE30A;
            padding-bottom:0;
        }
        .box {
            background: #CAE8B2;
        }
        .box {
            background: #FFEC56;
        }

        .box h2 {
            background: #5E7553;
        }
        .box h2 {
            background: #242001;
        }




It's not pretty but the aesthetics are your job :)