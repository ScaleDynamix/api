# Nestify api v1

REST API for managing stacks and sites on Nestify platform

## Authentication

Include your API key in request header 'Key; 

    curl -H "Key: $KEY" https://api.nestify.io/v1/sites

API keys can be found at https://my.nestify.io/settings

# Endpoint

	https://api.nestify.io/v1/

## List all servers

List currently active servers with nested instance data

	GET /v1/servers

Parameters: None

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/servers

Response

    {
        "result": {
            "stacks": [
                {
                    "id": "32",
                    "name": "s1-us-east",
                    "created": "2019-01-08 08:50:20",
                    "provider": "do"
                }
            ]
        },
        "success": true,
        "errors": []
    }

## List all sites

Lists currently active sites with associated metadata, such as DNS IP, cms type and php version.

	GET /v1/sites

Parameters: None

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/sites

Response

    {
        "result": {
            "sites": [
                {
                    "id": "3",
                    "name": "site3",
                    "status": "processing",
                    "created": "2018-08-04 16:10:04"
                },
                {
                    "id": "52",
                    "name": "MyWPSite",
                    "status": "active",
                    "created": "2019-01-08 09:18:03"
                }
            ]
        },
        "success": true,
        "errors": []
    }


## Create a new site

Launches a new site on specified stack. Returns site_id on success

	POST /v1/sites
	
Parameters

| Parameter | Description |
| ------ | ------ |
| name | Unique name for the new site. (Required) |
| vps_id | Server ID where this site needs to be created. (Required) |
| type | CMS Type for the new site. (Required) |
| clonesourceid | Site ID to clone data from (Required for site type 9) |

CMS Types

| Type ID | CMS Type |
| ------- | -------- |
| 1 | WordPress
| 2 | WordPress Multisite (Subdomains) |
| 3 | WordPress Multisite (Subdirectories) | 
| 4 | WooCommerce | 
| 5 | PHP / HTML |
| 9 | Clone another site |

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/sites -X POST -d "name=MyWPSite&stack_id=32&type=1"

Response

    {
        "result": [
            {
                "name": "MyWPSite",
                "vps_id": "32",
                "type": "1",
                "id": "52",
                "status": "processing"
            }
        ],
        "success": true,
        "errors": []
    }


## Delete a site

Deletes a site and associated DNS records, certificates and files.

	DELETE /v1/sites/:site_id

Parameters: None

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/sites/1 -X DELETE

Response

    {
        "result": {
            "id": "1"
        },
        "success": true,
        "errors": []
    }
    
## List site metadata

Returns wp-admin and ssh credentials of specified site along with site tags, domains and git origin.

	GET /v1/sites/:site_id

Parameters: None

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/sites/52 

Response

    {
        "result": [
            {
                "id": "52",
                "stackid": "32",
                "name": "MyWPSite",
                "domain": "MyWPSite.dxpsites.com",
                "type": "WordPress",
                "sshport": "40745",
                "metadata": {
                    "dbpass": "pr9flsssUm1vMA5zAt",
                    "wppass": "Bxxlba3naA608qk0t",
                    "sshuser": "sitemanager",
                    "wpuser": "sitemanager"
                },
                "status": "active",
                "created": "2019-01-08 09:18:03",
                "ssh_ip": "8.183.96.3",
                "domains": [],
                "tags": [],
                "git-origin": "ssh:\/\/sitemanager@8.183.96.3:40745\/~\/git"
            }
        ],
        "success": true,
        "errors": []
    }

## Copy data from another site

Copies data from another site to specified site and updates WordPress siteurl, home values in the database. Used for staging operations. 

If .clone.sh script is present in source site's web root (public_html), it will be executed on destination site after staging operation.

	PUT /v1/sites/:site_id
	
Parameters

| Parameter | Description |
| ------ | ------ |
| clonesourceid | Site ID to clone data from. (Required) |
| copyscope | Scope of copy operation. (Required) |


Copy Scope

| Type ID | CMS Type |
| ------- | -------- |
| files | Copy only files from another site. | 
| db | Copy only database from another site. |
| filesdb | Copy files and database from another site. | 


cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/sites/53 -X PUT -d "clonesourceid=52&copyscope=filesdb"

Response

    {
        "result": [
            {
                "id": "53",
                "stackid": "32",
                "name": "AnotherSite",
                "domain": "AnotherSite.dxpsites.com",
                "type": "1",
                "sshport": "40371",
                "metadata": {
                    "dbpass": "2HiW6zcX0POSi2a7qX",
                    "wppass": "NVTOCQbLQWJhVqJui1",
                    "clonesourceid": "52",
                    "copyscope": "filesdb"
                },
                "status": "processing",
                "created": "2019-01-08 11:12:20"
            }
        ],
        "success": true,
        "errors": []
    }

## List site tags

Returns a list of tags assigned to a site.

	GET /v1/tags/:site_id

Parameters: None

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/tags/52 

Response

    {
        "result": {
            "tags": {
                "9": "prod",
                "10": "5.2",
                "11": "wp"
            }
        },
        "success": true,
        "errors": []
    }

## Assign a new site tag    
Assigns a new tag to specified site.

	POST /v1/tags/:site_id
	
Parameters

| Parameter | Description |
| ------ | ------ |
| tag | Alphanumeric tag with no spaces. (Required) |

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/tags/52 -X POST -d "tag=yoast"

Response

    {
        "result": {
            "tags": {
                "9": "prod",
                "10": "5.2",
                "11": "wp",
                "12": "yoast"
            }
        },
        "success": true,
        "errors": []
    }

## Delete a site tag
Deletes a tag from specified site

	DELETE /v1/tags/:site_id
	
Parameters

| Parameter | Description |
| ------ | ------ |
| tag_id | ID of tag to be deleted. (Required) |

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/tags/52 -X DELETE -d "tag_id=11"

Response

    {
        "result": {
            "tags": {
                "9": "prod",
                "10": "5.2",
                "12": "yoast"
            }
        },
        "success": true,
        "errors": []
    }
    
## Add a new domain to a site

Points a new domain to a site and generates LetsEncrypt SSL.

	POST /v1/domains/:site_id
	
Parameters

| Parameter | Description |
| ------ | ------ |
| domain | Domain name or subdomain. (Required) |

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/domains/52 -X POST -d "domain=myapi.com"

Response

    {
        "result": {
            "id": "11"
        },
        "success": true,
        "errors": []
    }

## List all domains pointed to a site

Lists all domain names pointed to a site

	GET /v1/domains/:siteid

Parameters: None

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/domains/52

Response

    {
        "result": {
            "domains": [
                {
                    "id": "11",
                    "domain": "myapi.com",
                    "primary": false,
                    "created": "2019-01-08 11:03:38"
                },
                {
                    "id": 0,
                    "domain": "MyWPSite.dxpsites.com",
                    "primary": true
                }
            ]
        },
        "success": true,
        "errors": []
    }

## Change primary domain of a site
Changes the primary domain from a site and updates WordPress siteurl, home values in the database.

	PUT /v1/domains/:site_id
	
Parameters

| Parameter | Description |
| ------ | ------ |
| domain_id | ID of domain name to be made primary. (Required) |

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/domains/52 -X PUT -d "domain_id=11"

Response

    {
        "result": {
            "id": "11"
        },
        "success": true,
        "errors": []
    }

## Delete a domain from a site
Deletes a domain name pointed to a site. Returns error if the domain is primary.

	DELETE /v1/domains/:site_id

Parameters

| Parameter | Description |
| ------ | ------ |
| domain_id | ID of domain name to be deleted. (Required) |

cURL (Example)

    curl -H "Key: $KEY" https://api.nestify.io/v1/domains/52 -X DELETE -d "domain_id=12"

Response

    {
        "result": {
            "id": "12"
        },
        "success": true,
        "errors": []
    }
