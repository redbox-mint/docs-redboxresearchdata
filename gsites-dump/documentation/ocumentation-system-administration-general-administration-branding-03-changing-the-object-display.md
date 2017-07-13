By default the Mint display for each object is rather technical - let's try and tidy it up a bit!

1. Open the `portal/``default/mint/` directory 
1. Copy the `display `directory
1. Paste the directory into `portal/``default/local/ `
1. Open the newly pasted `display `directory

This is a handy way to start changing interfaces - copying from an existing skin.


If you go to `portal/default/local/display/default/detail/preview` you'll find a header and footer template. Compare that to portal/default/default/display/default/detail/preview and you can see that all the original Mint code does is "overload" two of the default templates. If you look at portal/default/default/display you'll see that there's a range of display templates for various content - including audio, video, text. Naturally, the system uses the `default `templates for content that doesn't have a specific template. These content aren't used much in the ReDBox and Mint systems but it's worth poking around the various files to get a feel for how things are done.


So let's change the display a little to create a basic staff profile page.

* Open `portal/default/local`
* Create a new directory named `display`
* Under `display `create a new directory named `parties_people`
* Under `parties_people `create a new directory named `detail`
* Under `detail`` `create a new directory named `preview`

You should now have the following directory structure: `portal/default/local/display/parties_people/detail/preview`. In this directory add a file called `body.vm` with the following contents:





        
#macro( displayValue $value )
  #if($value.size() > 1)
    #foreach($item in $value)
      <span class="meta-value">$self.escape($item)</span><br/>
    #end
  #else
    $self.escape($value.get(0))
  #end
#end


#foreach($key in $metadata.getJsonObject().keySet())
  $key - 
  #displayValue($metadata.getList($key))
  <br/>
#end


As you change these files you're most likely going to need to restart Mint for the changes to take effect. Restart Mint now and browse to a person record - I always like Cosmo Costanza from the test data. When Cosmo's details display you should see a list of all of the key-value pairs available for us to display.


The details displayed are handy for determining the sort of variables we could use to create a staff profile page. Let's edit the `body.vm` file again:





        #macro( displayValue $metadata $key )
          #set( $value = $metadata.getList($key) )
          #if($value.size() > 1)
            <ul>
            #foreach($item in $value)
              <li><span class="meta-value">$self.escape($item)</span></li>
            #end
            </ul>
          #else
            $self.escape($value.get(0))
          #end
        #end


        <p><b>Position:</b> 
        #displayValue( $metadata, "Job_Title")
        </p>


        <p><b>email:</b> 
        #displayValue( $metadata, "Email")
        </p>


        <p>
        #displayValue( $metadata, "Description")
        </p>


        <hr/>
        #foreach($key in $metadata.getJsonObject().keySet())
          $key - 
          #displayValue($metadata $key)
          <br/>
        #end
        <hr/>


Check the page and see what you think - perhaps some layout fine tuning and extra information are needed...