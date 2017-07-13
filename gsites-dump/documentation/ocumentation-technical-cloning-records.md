## []()Introduction

This page outlines the process to clone a record. This is the method that is used for creating Self-Submission records based of a Data Management Plan (version 1.6.1 or later) but could just as easily be used to clone records of the same type.
## []()Process Summary

Essentially what we are trying to do is create a new record (i.e. with a new identifier), copy the metadata from the original (the source) and (optionally) transform the metadata to clean up things that you may want to be unique in your new record (e.g. the title field).


Adding cloning to the user interface


To add cloning to the user interface we need to register an on click event that makes 2 ajax calls. The first calls our packaging script which will create a new package (record) of the type we require and returns it's identifier. On successful completion of the packaging script, we call our copyTfPackage script to copy the metadata from our source package into the newly created package and sets an additional property that can be used to flag that a transform is required.


![](http://www.google.com/chart?chc=sites&cht=d&chdp=sites&chl=%5B%5BGoogle+Gadget'%3D20'f%5Cv'a%5C%3D0'10'%3D499'0'dim'%5Cbox1'b%5CF6F6F6'fC%5CF6F6F6'eC%5C0'sk'%5C%5B%22Include+gadget+(iframe)%22'%5D'a%5CV%5C%3D12'f%5C%5DV%5Cta%5C%3D10'%3D0'%3D500'%3D797'dim'%5C%3D10'%3D10'%3D500'%3D797'vdim'%5Cbox1'b%5Cva%5CF6F6F6'fC%5CC8C8C8'eC%5C'a%5C%5Do%5CLauto'f%5C&sig=LIupg-AjxUbah_DqtRj3JecPeDg)



The key things that you may need to change from the above code snippet are the packageType and the tfMetaPropertyValue. The relatedData property tells the copyTFPackage script whether to write information about it's source record, if this is used in the Data Management Tool to show what related self-submission records have been created but will break if used with the standard review (Dataset) record. If you are using it with the review record, set relatedData to "false".


Configuring the transform
We utilise ReDBox's [transformer](http://www.redboxresearchdata.com.au/documentation/system-overview/system-concepts#TOC-System-architecture) functionality to modify the newly cloned data.  As we store our metadata is stored as JSON, we utilise our [JSON Velocity Transformer](https://github.com/the-fascinator-contrib/plugin-transformer-jsonVelocity) to do the job. First we need to set up the transformer by adding the following to the transformerDefaults in our [system-config.json](https://github.com/redbox-mint/redbox-build-dev-local/blob/master/src/main/config/home/system-config.json#L304):
![](http://www.google.com/chart?chc=sites&cht=d&chdp=sites&chl=%5B%5BGoogle+Gadget'%3D20'f%5Cv'a%5C%3D0'10'%3D499'0'dim'%5Cbox1'b%5CF6F6F6'fC%5CF6F6F6'eC%5C0'sk'%5C%5B%22Include+gadget+(iframe)%22'%5D'a%5CV%5C%3D12'f%5C%5DV%5Cta%5C%3D10'%3D0'%3D500'%3D197'dim'%5C%3D10'%3D10'%3D500'%3D197'vdim'%5Cbox1'b%5Cva%5CF6F6F6'fC%5CC8C8C8'eC%5C'a%5C%5Do%5CLauto'f%5C&sig=CvjXRgodwYVKPvmsyZR7EbHx2uM)


Where:

* the TFMETAPropertyValue value needs to match the tfMetaPropertyValue that was in the ajax call above, otherwise the transform will not trigger.
* templatesPath needs to point to a velocity template that will perform the transform. More on this later.

We also need to associate this transformer to our record type. This is done in the [packages rules configuration file](https://github.com/redbox-mint/redbox/blob/master/config/src/main/config/home/harvest/workflows/self-submission.json#L16):
![](http://www.google.com/chart?chc=sites&cht=d&chdp=sites&chl=%5B%5BGoogle+Gadget'%3D20'f%5Cv'a%5C%3D0'10'%3D499'0'dim'%5Cbox1'b%5CF6F6F6'fC%5CF6F6F6'eC%5C0'sk'%5C%5B%22Include+gadget+(iframe)%22'%5D'a%5CV%5C%3D12'f%5C%5DV%5Cta%5C%3D10'%3D0'%3D500'%3D97'dim'%5C%3D10'%3D10'%3D500'%3D97'vdim'%5Cbox1'b%5Cva%5CF6F6F6'fC%5CC8C8C8'eC%5C'a%5C%5Do%5CLauto'f%5C&sig=xWbC-QmXy_fGheSRGNFrbV5nwI4)


The transform template
The final piece to the puzzle is the transform template. This is a velocity template that will produce a JSON output based on the data that is in the original metadata (that is loaded into a context object by the transformer). Snippet below is a cut down example of the transformer specified in our system-config.json, for a more complete example checkout the version in [github](https://github.com/redbox-mint/redbox/blob/master/config/src/main/config/home/templates/self-submission/selfSub.vm):


![](http://www.google.com/chart?chc=sites&cht=d&chdp=sites&chl=%5B%5BGoogle+Gadget'%3D20'f%5Cv'a%5C%3D0'10'%3D499'0'dim'%5Cbox1'b%5CF6F6F6'fC%5CF6F6F6'eC%5C0'sk'%5C%5B%22Include+gadget+(iframe)%22'%5D'a%5CV%5C%3D12'f%5C%5DV%5Cta%5C%3D10'%3D0'%3D500'%3D197'dim'%5C%3D10'%3D10'%3D500'%3D197'vdim'%5Cbox1'b%5Cva%5CF6F6F6'fC%5CC8C8C8'eC%5C'a%5C%5Do%5CLauto'f%5C&sig=CvjXRgodwYVKPvmsyZR7EbHx2uM)