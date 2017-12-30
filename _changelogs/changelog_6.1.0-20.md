---
title: Search Guard 6.1.0-20
slug: changelog-6.1.0-20
category: changelogs
order: 990
layout: changelogs
description: Changelog for Search Guard 6.1.0-20
---
<!---
Copryight 2017 floragunn GmbH
-->

# Search Guard 6.1.0-20

Release Date: 22.12.2017

## Fixes from 6.0.0-17.beta1 to 6.1.0-20

* Kibana: [Login not redirecting when global tenant is disabled](https://github.com/floragunncom/search-guard/issues/411){:target="_blank"}
* Kibana: [Unknown handler async in SG6-beta1 plugin when disabling basicauth](https://github.com/floragunncom/search-guard-kibana-plugin/issues/49){:target="_blank"}
* Kibana: [SG GUI management panel overlaps](https://github.com/floragunncom/search-guard-kibana-plugin/issues/54)

## Search Guard SSL

* Disable client initiated TLS renegotiation by default
* Disable HTTP compression by default when https is enabled

## Search Guard Core

* Search Guard ships as [Enterprise Edition](../_docs/license_enterprise.md) by default
  * All enterprise modules are already contained in the plugin
  * You don't need to download and install them manually anymore
* [Search Guard Community Edition](../_docs/license_community.md) switch
  * Enable the Community Edition by adding a switch in elasticsearch.yml     
* Improved the [demo installer](../_docs/quickstart.md) installer for easy PoC setup
  * If configured, Search Guard initializes its index automatically if it's not present
* Usage of the Search Guard certificates generated by the demo installer [must be allowed explicitely](../_docs/demo_installer_artefacts.md)
  * Production safeguard
* Full [Cross Cluster Search support](../_docs/crossclustersearch_tribes.md)
* Full support for [Custom authentication modules](../_docs/custom_auth.md)
* [Authenticators can be enabled and disabled for REST and transport individually](../_docs/configuration_auth.md)
* Custom user attributes for index names and [DLS queries](../_docs/dlsfls_dls.md)
  * Use additional properties from authentication backends for variable substitution
  * For example, use a JWT claim value directly in an index name or DLS query
* User impersonation on REST layer  
* [Role Mapping Modes](../_docs/configuration_roles_mapping_modes.md)
  * You can now map backend roles to Search Guard roles directly 
* Made PEM certificates a first class citizen
* **BREAKING**: Alias checks on index-level
  * You can only create aliases on indices you have permissions for  
* `whoami` switch for [sgadmin](../_docs/sgadmin.md)
  * Outputs infos regarding the used certificate, useful for debugging
License system, license checks
* Introduced [License Handling](../_docs/license_enterprise.md)
* Added endpoint for license and installed modules
  * `https://<host>:<http_port>/_searchguard/license`
* Removed fallback to default authenticator
  * If no authentication domain is configured, Search Guard will now raise an error

## REST management API

* Introduced [role-based access control](../_docs/restapi_api_access.md)
  * You can configure which roles have access to the API in `elasticsearch.yml` 
  * If the Search Guard index is initialized, access is possible without an admin certificate
* Access control for endpoints and methods
  * You can grant roles permissions for specific endpoints and methods
  * For example, allow to view roles, but disallow to change or delete them
* Globally disable endpoints and methods
* Reserved resources
  * Any resource, like users, role or permissions, can be marked as read-only
  * read-only permissions are not changeable by the REST API
  * Use to protected resources like the Kibana server user  

## Search Guard Kibana Plugin

* Moved release location from 6.x onwards to [Maven Central](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22search-guard-kibana-plugin%22){:target="_blank"}
 

## Kibana Configuration GUI

* Completely new [Kibana based configuration GUI](../_docs/kibana_config_gui.md)
  * Roles, Role Mappings, Action Groups and Internal Users
* Can be used in conjunction or as an alternative to `sgadmin`
* Configure index- and document-type level permissions
* Configure Document- and Field-Level-Security
* Configure Multi Tenancy
* Display configured authentication and authorization modules
* Display system status, installed modules and license information
* Auto-detection of available modules
  * No configuration in `kibana.yml` necessary  
* Based on the role-based access feature of the REST-API

## BREAKING: Audit logging

* Completely revised the [Audit Log Module](../_docs/auditlogging.md)
  * Since the structure of the events has changed, the new default index name is `auditlog6`
* Events can be tracked on REST-layer, transport-layer or both
* Events can be skipped based on their action (transport layer) and/or REST request path (REST layer)
* You can configure whether sub-requests for bulk requests should be stored separately
  * Before, sub-requests have been added to the original event as numbered fields
  * This leads to field limit problems for huge bulk requests 
  * Sub-requests can now be logged as separate events and then correlated
* Events can now be correlated by a field `task_id`
* Introduced new category GRANTED_PRIVILEGES
  * Before, both succesfull authentication events and other successfull requests have been logged in the AUTHENTICATED category
  * Succesfull authentication events are still written to the AUTHENTICATED category
  * Other successfull events are written to the GRANTED_PRIVILEGES category
* Improved back-pressure handling 
* Configurable queue sizes
* Added PEM support for external_elasticsearch and webhook storage types
* Added PEM support for external_elasticsearch and webhook storage types
* Added `log4j` storage type
  * Use `log4j` appenders as event sink, e.g. SNMP 

## Document-Level security

* Use custom user attributes for dynamic [DLS queries](../_docs/dlsfls_dls.md)
 * This makes it possible to write powerful dynamic queries based on user attributes 
 * For example, JWT claims
 * For example, LDAP attributes

## JWT

* All claims are added as custom user attributes with the `attr.jwt.` prefix
* Can be used as variables in DLS queries and index names

## Active Directory / LDAP

* All attributes of the user entry are added as custom user attributes with the `ldap.ldap.` prefix
* Can be used as variables in DLS queries and index names

## Known Issues

* The Kibana Config GUI might log a JSON error on the console from time to time
* Multi Tenancy upgrade from Kibana 5 to Kibana 6 may break
* Warning message on install about missing sha512 checksum