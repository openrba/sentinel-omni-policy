# sentinel-omni-policy
 Hashicorp sentinel policy that uses declarative markup to create a base set of rules for any resource terraform supports.  This repo includes the following components:

 - The omni sentinel policy that uses the python-omni-data repository to define rules for Terraform arguments.
 - Sentinel test configurations for those policies

 It is intended to be combined with the following:

 - A Terraform Enteprise workspace, which runs Terraform to update your Sentinel policies whenever the repo changes.
 - A lightweight CI solution (like Github Actions), for continuously testing your sentinel code.

## How it works
 This sentinel policy can be used against any provider, resource and argument that Terraform supports.  For each resource being modified in Terraform it does a lookup of the omni-policy map for that resource located at openrba/python-omni-policy and applies a set of rules based on certain keys provided by that map.  The following keys are used by this policy:

 - **status** (resource) - Status is a key-value pair at the resource level that indicates the current ring level of the resource.  Ring levels are a continuation of the excellent work of the thoughworks techradar that organizes technologies into five distinct classifications e.g REJECT,HOLD,ASSESS,TRIAL,ADOPT. Using the input parameter min_ring, the sentinel policy applies a mandatory access control method to permit anything at that ring level or higher.  For example a min_ring value of "ASSESS" would permit any resource at a ring level of ASSESS,TRIAL, and ADOPT.

- **required** (argument) - Required is a key-value pair at the argument level of a resource that indicates whether the argument is required or not.  This policy uses this key from the omni-policy map for the resource to assure the argument is present in any resource being created through Terraform.

- **policy** (argument) - Policy is a key-value pair at the argument level that indicates the policy to be applied to the argument's value when passed through the policy.  It includes the following capabilities:
    - **string-match** - Exact match of an argument's value against a specific string and is the simpliest form of enforcing a specific value.  Example: `standard` matches only "standard".
    - **list-match** - Exact match of an argument's value against *any* of a list of strings.  Example: `['standard','basic']` would match either "standard" or "basic".
    - **regex-match** - A regex match of an argument's value against a specific regex expression.  This value must start with a `^` or a string-match will occur.  Example: `^[a-z].` would validate that the argument must contain any number of lowercase alpha characters.
    - **convention-match** - A match that expects a naming convention that includes lookup fields.  Fields will be looked up against the custom.json map that contains allowed_values for a particular field.  Example: the value of `<market>-<businessUnit>-[0-9][0-9]` will match to `us-core-01` assuming `us` is an allowed_value for market and `core` is allowed_value for businessUnit in your custom.json.


