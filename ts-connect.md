---

copyright:
  years: 2020
lastupdated: "2020-12-10"

keywords: troubleshooting for PostgreSQL

subcollection: databases-for-postgresql

content-type: troubleshoot

---

{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:support: data-reuse='support'}
{:codeblock: .codeblock}
{:pre: .pre}
{:note:.deprecated}
{:troubleshoot: data-hd-content-type='troubleshoot'}


# Why can’t I connect to my PostgreSQL deployment?
{: #troubleshoot-connect}
{: troubleshoot}
{: support} <!-- Only add this attribute to entries that you want to display in the support center. -->

If you encounter errors when connecting to your {{site.data.keyword.databases-for-postgresql_full}} deployment, review these common causes and resolutions.
{:shortdesc}

You receive an error message or fail to connect to a {{site.data.keyword.databases-for-postgresql}} deployment.  If reviewing application logs, you might see errors that mention intermittent connection timeouts or unable to connect.
{:tsSymptoms}

Review the following information to troubleshoot and resolve common connectivity problems:
{:tsResolve}
* An unsecured connection is a common cause of connectivity errors.  All {{site.data.keyword.databases-for-postgresql}} connections use TLS/SSL encryption; {{site.data.keyword.databases-for-postgresql}} rejects unsecured connections.  To avoid errors, make sure you configured a secure connection.  Refer to [Getting Started](/docs/databases-for-postgresql?topic=databases-for-postgresql-getting-started) for an example of a secure connection.
* If you are using a private endpoint, make sure that you specify connection strings that contain the private endpoint (see [Credentials for Private Endpoints](/docs/databases-for-postgresql?topic=cloud-databases-service-endpoints#credentials-for-private-endpoints)) and that you followed the steps in [Connecting through Private Endpoints](/docs/databases-for-postgresql?topic=cloud-databases-service-endpoints#private-endpoint-connections).
* If your application log captures a short connection interruption, that behavior is expected as a normal part of operations for this managed service. However, if you experience several minutes of connection interruption check the Cloud Status for the service. For more information, see [Application-level High-Availability](/docs/databases-for-postgresql?topic=databases-for-postgresql-high-availability#application-level-high-availability).
