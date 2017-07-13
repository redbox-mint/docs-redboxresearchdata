ReDBox 1.6.1 provides a completed implementation of the Researcher Dashboard features. Key elements for this feature include:

* A researcher dashboard, available via the redbox/dashboard/home path
* A new workflow and form system to improve the ease of creating and reconfiguring ReDBox forms

 * The forms use language files to allow for simpler editing of the form labels and help text
 * As of 1.6.1 the original "review" forms have not been migrated to the new system
* Form and workflow for the creation of data management plans as well as:

 * The ability to share view access with other users
 * Creation of versioned PDF versions of the plan (implemented using the new PDF transformer)
 * Ability to create a plan via the alerts system and allocate plan ownership
* Form and workflow for the self-submission of data collection information

 * Configurable pre-fill of the form based on data management plan information
 * A transition system for allowing completed forms to be passed into the main review workflow
* Ability to send template-based email notifications based on record state (e.g. the creation of a new data management plan via alerts)

Documentation for configuring the data management planning forms and collection self-submission has been provided in [Setup a new workflow](documentation-how-to-setup-a-new-workflow)


Other work has included:

* Addition of the "Grant Number" and "Funding Body" options in the reporting tool.