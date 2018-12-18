# ScaleDynamix api v1

REST API for managing stacks and sites on ScaleDynamix platform

## Authentication

Include your API key in request header 'Key; 

    curl -H "Key: $KEY" https://api.scaledynamix.com/v1/sites
API keys can be found at https://platform.scaledynamix.com/settings

# Endpoint

	https://api.scaledynamix.com/v1/

## Add a new cloud provider

Add a new cloud provider account that can be used to launch new instances. Returns provider ID on success.

	POST /v1/providers

	Type (String): {aws,gcp,digitalocean,azure}
	Data (String): {api_data/authentication_certificate}

	Result: {status, metadata = array()}

## List cloud providers

List currently defined cloud providers

	GET /v1/providers

	Result: {array()}

## Delete a cloud provider

Deletes a cloud provider account if it is not used by any stacks. Returns error if the provider is in use.

	DELETE /v1/providers

	Provider_id (Int)

	Result: {status, metadata = array()}
		
## Create a new stack

Launches one or more instances using specified cloud provider. Returns stack ID on success

	POST /v1/stacks
	
	Name (String)
	Provider_id (Int)
	Stackdata (Array)

	Result: {status, metadata = array()}

## List all stacks

List currently active stacks with nested instance data

	GET /v1/stacks

	Result: {array()}

## Delete a stack with all active sites

Deletes a stack and all associated instances, sites and related cloud resources. 

	DELETE /v1/stacks

	Stack_id (Int)

	Result: {status, metadata = array()}
	
## Create a new site

Launches a new site on specified stack. Returns site_id on success

	POST /v1/sites
	
	Name (String)
	Stack_id (Int)
	Type (String): {wp,wpmu,wpmusub,wc,php,html}

	Result: {status, metadata = array()}


## Clone an existing site to a new site

Copies uploads and database from an existing site to a new site. Returns site_id of new site on success

	POST /v1/staging
	
	Newsitename (String)
	Source Site_id (Int)

	Result: {status, metadata = array()}

## List all sites

Lists currently active sites with associated metadata, such as DNS IP, cms type and php version.

	GET /v1/sites

	Result: {array()}

## Delete a site

Deletes a site and associated DNS records, certificates and files.

	DELETE /v1/sites

	Site_id (Int)

	Result: {status, metadata = array()}

## List site metadata

Returns wp-admin and ssh credentials of specified site along with site tags, domains and git origin.

	GET /v1/sites/id

	Result: {array()}
		
## Add a new domain to a site

Points a new domain to a site and generates LetsEncrypt SSL. If domain is set to primary, this will also update WordPress siteurl and home values in database. 

	POST /v1/domains
	
	Site_id (Int)
	Domain (String)
	Primary (Bool)

	Result: {status, metadata = array()}

## List all domains pointed to a site

Lists all domain names pointed to a site

	GET /v1/domains/siteid

	Result: {array()}

## Change primary domain from a site
Changes the primary domain from a site and updates WordPress siteurl, home values from the database

	PATCH /v1/domains
	
	Site_id (Int)
	Domain (String)

	Result: {status, metadata = array()}
	
## Delete a domain from a site
Deletes a domain name pointed to a site. Returns error if the domain is primary.

	DELETE /v1/domains

	Site_id (Int)
	Domain (String)

	Result: {status, metadata = array()}
