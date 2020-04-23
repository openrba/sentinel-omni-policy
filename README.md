# sentinel-omni-policy
 Hashicorp sentinel policy that uses declarative markup to create a base set of rules for any resource terraform supports.  This repo includes the following components:

 - The omni sentinel policy that uses the python-omni-data repository to define rules for terraform arguments.
 - Sentinel test configurations for those policies

 It is intended to be combined with the following:

 - A Terrafrom Enteprise workspace, which runs Terraform to update your Sentinel policies whenever the repo changes.
 - A lightweight CI solution (like Github Actions), for continuously testing your sentinel code.

## How it works
 This sentinel policy can be used against any provider, resource and argument that Terrafrom supports.  It uses a dynamic lookup against a repository of json documents created in the omni-policy framework found at openrba/python-omni-policy.  It includes the following capabilities.

 - Status - Status is similar to a mandatory access control framework that is loosely based on the excellent techradar ring status from Thoughtworks.  Five rings exist eg. REJECT, HOLD, ASSESS, TRIAL, ADOPT.  A resource is permitted in Terraform if the min_ring parameter passed in is lower than the resource's ring status.  For example, if the sentinel policy is initiated with the parameter min_ring=ASSESS then any resource with a status of ASSESS,TRIAL, and ADOPT would be permitted.  RBA uses this status to determine if certain providers or resources are permitted in various environments (dev,prod).  It allows the technology groups to clearly understand the status of various technologies within the organization.
 - String Match - Will match a resource's argument against a single string.  Example "standard" matches "standard"
 - List Match - Will match a resource's argument against any of a list of strings.  Example "['enabled','disabled']" will match on either "enabled" or "disabled".
 - Regex Match - Will match a resource's argument against a regex expression, note regex expression must start with the "^" character or it will default to a string match.  Example: "^[a-z]." will permit any alpha lowercase chracters.
 - Convention Match -  Expects a naming convention that includes lookup fields agains the custom.json values in omni-policy.  Example: the value of "<market>-<businessUnit>-[0-9][0-9]" will match to "us-core-01" assuming "us" and "core" are allowed values in your custom.json. 


