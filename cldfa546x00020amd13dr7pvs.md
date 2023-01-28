# Redefining Audit Logs

Derailed's main purpose, above providing a great experience to gamers around the world, is to be customizable as well as easily expandable.

Audit Logs act as a way to do that perfectly. A missed potential with how Discord did it.

This design document explains in thorough detail my design and plan for Derailed's audit logs.

## Structure

To make audit logs easily expandable, the first place to start off is the structure:

* id - Snowflake ID of this Audit
    
* type - Type of the audit, can be set to anything at or under 45 characters
    
* auditor\_id - The user/bot which performed this Audit. Can be nulled for system audits
    
* content - Markdown-supported content providing exact details on the audit itself for users to oversee. Can be nulled. Max length is 2048
    
* guild\_id - Guild of the Audit
    
* timestamp - When this Audit happened
    

This structure sets out so:

* Audits can only be on Guilds
    
* An Audit can have whatever type
    
* Can have highly thorough detailed audits on certain things
    
* The Auditor can be tracked down
    

Although we do enforce a few limits as to not overload our systems:

* Guilds have a hard limit of 1m audits, then the oldest is deleted
    
* An Audit only lasts for a month, then it is deleted
    
* A user/bot may perform only 100k audits per 10 minutes per guild
    

Endpoints are still TBD in our actual implementation in the future, and please do note that anything here can still change because it hasn't been implemented yet.