Changing way in which an object is displayed in the search results is similar to the process of [changing the object display](documentation-system-administration-general-administration-branding-03-changing-the-object-display). This template will change how people are displayed in the Mint search results. To make life a little easier, use the Parties-People view so that you're only seeing people records.


1. Open `portal/default/local/display/parties_people`
1. Create a new file named `result.vm`
1. Open `result.vm in a text editor`
Copy the following text into `result.vm`:





        #set($id = $self.getFirst("id"))
        #set($title = $self.getEscapeHtml($self.getFirst("dc_title")))
        #set($email = $self.getEscapeHtml($self.getFirst("Email")))
        #set($description = $self.getFirst("dc_description"))
        #set($format = $self.get("dc_format"))


        <div class="$itemClass" id="$id">
        <h3>
          <a href="$portalPath/detail/$id/">#if($title.trim() == "")<em>Untitled</em>#{else}$title#end</a>
        </h3>
        <p class="item-description condense" rel="$!displayType"><b>email</b>: $!email&nbsp;</p>
        <p class="item-description condense" rel="$!displayType">$!description&nbsp;</p>
        <div class="clear"></div>
        </div>
        

Now restart Mint and do a search in the Parties-People view.