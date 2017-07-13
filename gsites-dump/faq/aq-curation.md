[[_TOC_]]


## []()Q: How does curation work?

**A: **As you can configure ReDBox and Mint a number of different ways, it's not always easy to give a straight answer. However, let's look at how ReDBox and Mint interact, assuming you're using handles and want NLA IDs for people:

1. When creating a new collection record in ReDBox you need to indicate that Ludo Smith was a creator. You search for his identity using Mint

 * The call to Mint from ReDBox looks like  [http://demo.redboxresearchdata.com.au/mint/Parties_People/opensearch/lookup?searchTerms=ludo%20smith](http://demo.redboxresearchdata.com.au/mint/Parties_People/opensearch/lookup?searchTerms=ludo%20smith)
1. ReDBox then stores Ludo's name and ID in its metadata
1. When you're ready to publish the collection, you click "Publish" in ReDBox and:

 1. ReDBox determines if the collection needs an identifier (such as a handle) and, if so, gets one
 1. ReDBox (using messaging) asks Mint to get its record for Ludo Smith ready for publishing

  1. If Ludo has an NLA identifier, Mint tells ReDBox the record is ready
  1. If Ludo needs an NLA identifier, Mint goes through the process of getting the details to the NLA and bringing back an NLA ID (you may have to step in using NLA's identity manager)
 1. Once Ludo has an NLA ID Mint advises ReDBox, and ReDBox inserts Ludo's NLA ID into the collection record
1. ReDBox can now publish a correctly linked collection record and Ludo's NLA ID is fully operational. 

This process is different if you are integrating with systems such as VTLS Vital, DOIs, URI identifiers (rather than handles) and so on. Your best bet is to sketch out your preferred model and fire it off to the mailing list to see what people suggest your options are. 


See also: [Curating Linked Data](documentation-system-overview-curating-linked-data)