
- Physical
	- Data Store
	- Domain Controllers
		- Host a copy of the AD DS Store
			- Consists of Ntds.dit file - this NTDS.dit has important information(password hashes)
			- Is stored by default in the %SystemRoot%\NTDS folder on all domain controllers
			- Is accessible only through  the domain controller processes and protocols
		- Provide Authentication and Authorization Services
		- Replicate updates to other domain controllers in the domain and forest
		- Allow administrative access to manage user accounts a resources
	- Global Catalog Server
	- Read-Only Domain Controller (RODC)
- Logical
	- Partitions
	- Schema
		- Defines every type of object that can be stored  in the directory
		- Enforces rules regarding object creation and configuration
		- Class Object - function is what objects can be created in the directory (e.g. User, Computer)
		- Attribute Object - Information that can be attached to an object (Display name)
	- Domains
		- Are used to group and manage objects in an organization
		- An administrative body for applying policies to objects and groups
		- A replication boundary  for replicating data between domain controllers
		- An authentication and authorization boundary that provides a way to limit the scope of access to resources

	- Domain Trees
		-  hierarchy of domains in AD DS
		-  Can have additional child domains
		- By default create a two-way transitive trust with other domains
	- Forests
		- Collection of one or more domain trees
		- Share a common schema
		- Share a common configuration partition
		- Share a common global catalogue to enable searching
		- Enables trust between all domains in the forest
		- Share the Enterprise Admins and Schema group
	- Sites
	- Organizational Units (OU)
		- Are active directory containers that contain users, groups, computers and other OUs
		- Represent your organization hierarchically and logically
		- Manage a collection of objects in a consistent way
		- Delegate permissions to administer a group of objects
		- Apply policies
	- Trusts
		- provides a mechanism  for users to gain  access to resources in another domain
		- Directional Trust - The trust direction flows from trusting domain to the trusted domain
		- Transitive Trust - The trust relationship is extended beyond two domain trust to include other trusted domains
		- All domains in a forest trust all other domains in the forest
		- Trust can extend outside forest
	- Objects

| Object         | Description                                                                                   |
| -------------- | --------------------------------------------------------------------------------------------- |
| User           | - Enable network resource access for a user                                                   |
| Inetorgperson  | - Similar to User account; Used for compatibility with other directory services               |
| Contacts       | - Used primarily to assign e-mail addresses to external users; Does not enable network access |
| Groups         | - Use to simplify administration of access control                                            |
| Computers      | - Enables auhtnetication and audition computer access to resources                            |
| Printers       | - Use to simplify process of locating printers                                                |
| Shared Folders | - Enables users to search  for shared folders based on properties                             |
