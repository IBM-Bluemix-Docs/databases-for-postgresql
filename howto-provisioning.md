---

copyright:
  years: 2018
lastupdated: "2018-09-27"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}

# Provisioning {{site.data.keyword.databases-for-postgresql}}

To create an {{site.data.keyword.databases-for-postgresql_full}} deployment, you need to create an {{site.data.keyword.cloud}} service instance. A service instance can represent different types of service. The service type is determined by the service ID and you need to specify the appropriate service ID when you create a new service instance. The service ID for {{site.data.keyword.databases-for-postgresql}} is `databases_for_postgresql`.


## Using the catalog

You can create a {{site.data.keyword.databases-for-postgresql}} service from the [{{site.data.keyword.databases-for-postgresql}} page](https://{DomainName}/catalog/services/databases-for-postgresql/) in the {{site.data.keyword.cloud_notm}} catalog.

![Catalog Deployment Page](images/catalog-deployment.png)

When you create the deployment from the catalog, you need to specify the following parameters.

1. **The service name** - The name can be any string and is the name that is used on the web and in the command line to identify the new database deployment.
2. **The region** - The region in which the database deployment resides. Currently, only US-South is available.
3. **The database version** - The major version of the database to be created within the deployment. The latest minor version is always be used automatically. 

PostgreSQL changed versioning strategy at version 10. Previously, a major version was two sets of numbers (for example, 9.6) but from version 10, the major version is a single number (for example, 10 or 11). The most recent version is always automatically selected.
{: .tip}

Users can optionally set:

1. **The resource group** - If you are organizing your services into [resource groups](/docs/resources/bestpractice_rgs.html#bp_resourcegroups), you can specify the resource group in this field. Otherwise, you can leave it at default.
2. **Disk encryption** - Optionally, a Key Protect instance can be selected if the user has Key Protect configured. If it is configured, once the service is selected, a disk encryption key can be selected from the Key Protect service. By default, Key Protect is not used and the deployment automatically creates and manages its own disk encryption key. 

Users cannot set:

1. **The service plan** - A service can have a number of plan types with different pricing, resources or other features, each with their own service plan ID. {{site.data.keyword.databases-for-postgresql}} currently has one service plan, standard. 

Once you select the appropriate settings, click **Create** to start the provisioning process off.

The database takes some time to deploy. The user is navigated back to the {{site.data.keyword.cloud_notm}} Dashboard.

## Using the Command Line

The {{site.data.keyword.cloud_notm}} CLI tool is what you use to communicate with {{site.data.keyword.cloud_notm}} from your terminal or command line. For more information, see [Download and install {{site.data.keyword.cloud_notm}} CLI](https://{DomainName}/docs/cli/reference/bluemix_cli/download_cli.html).

To create a {{site.data.keyword.databases-for-postgresql}} deployment, you use the CLI to request a service instance with a `databases-for-postgresql` service ID.

The command template is:

```
ibmcloud resource service-instance-create <service-name> <service-id> <service-plan-id> <region>
```

More information about this command, in general, is available in the [CLI reference for resource groups](https://{DomainName}/docs/cli/reference/ibmcloud/cli_resource_group.html#ibmcloud_resource_service_instance_create).

In the specific case of creating a {{site.data.keyword.databases-for-postgresql}} deployment, set the service name (quote any name with spaces in it). Then, set `databases-for-postgresql` as the service ID. Enter `standard` for the service plan ID and `us-south` for the region.

```
ibmcloud resource service-instance-create example-psql databases-for-postgresql standard us-south
```

When the command is run, provisioning the database deployment begins. The database takes some time to deploy. You can check on its progress on your {{site.data.keyword.cloud_notm}} Dashboard. You can also run:

```
ibmcloud resource service-instance <service-name>
```

This command reports the current state of the service instance.

### Additional parameters

The `service-instance-create` command supports a `-p` flag, which allows addition parameters to be passed to the provisioning process. The parameters are in JSON format. Some parameters values are CRNs (Cloud Resource Name), which uniquely identifies a resource in the cloud. All parameter names and values are passed as strings.

* `backup_id`- A CRN of a backup resource to restore from. The backup must have been created by a database deployment with the same service ID. The backup is loaded after provisioning and the new deployment starts up that uses that data. A backup CRN is in the format `crn:v1:<...>:backup:<uuid>`. If omitted, the database is provisioned empty. This parameter cannot be set with a **version** parameter
* `version` - The version of the database to be provisioned. If omitted, the database is created with the most recent major and minor version. This parameter cannot be set with a **backup_id** parameter.
* `key_protect_key` - A CRN that references a Key Protect key, which is then used for disk encryption.
* `members_memory_allocation_mb` -  Total amount of memory to be shared between the database members within the database. For example, if the value is "4096" then the two database members get 4 GB of RAM between them, giving 2 GB of RAM per member. If omitted, the default value is used; "2048".

For example, if a database is being provisioned from a particular backup and the new database deployment needs two members, each with 2 GB, then the command looks like:

```
ibmcloud resource service-instance-create example-psql databases-for-postgresql standard us-south \
-p \ '{
  "backup_id": "crn:v1:bluemix:public:databases-for-postgresql:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
  "members_memory_allocation_mb": "4096"
}'
```

## Using Terraform

[Terraform](https://www.terraform.io/) enables you to safely and predictably create, change, and improve infrastructure. It is an open source tool that codifies APIs into declarative configuration files that can be shared amongst team members, treated as code, edited, reviewed, and versioned.

The [IBM Cloud provider for Terraform](https://ibm-cloud.github.io/tf-ibm-docs/v0.13.0) supports the ability to provision an IBM Cloud Database. Here is an example to provision a Database for Postgres:

```
data "ibm_resource_group" "group" {
  name = "default"
}

resource "ibm_resource_instance" "icd_postgres" {
  name              = "icd-postgres-created-with-terraform"
  location          = "eu-de"
  service           = "databases-for-postgresql"
  plan              = "standard"
  # If not provided it takes the default resource group.
  # resource_group_id = "${data.ibm_resource_group.group.id}"


  parameters = {
    version = "9.6"
    # Total amount of memory to be shared between the DB members
    # Postgres has 2 members by default.
    "members_memory_allocation_mb" = "8192"
  }
}
```

More information about this terraform tag is available in the [Resource Instance](https://ibm-cloud.github.io/tf-ibm-docs/v0.13.0/r/resource_instance.html).


## Provisioning through the Resource Controller API

You can provision new deployments by using the Resource Controller API. However, in order to use the Resource Controller API, you need some additional preparation.

1. [Obtain an IAM token from your API token](https://{DomainName}/apidocs/resource-controller#authentication).
2. You need to know the ID of the resource group that you would like to deploy to. This information is available through the [{{site.data.keyword.cloud_notm}} CLI](https://{DomainName}/docs/cli/reference/ibmcloud/cli_resource_group.html#ibmcloud_resource_groups). You can find a list of resource groups with `ibmcloud resource groups` and the ID of a resource group with `ibmcloud resource group`. 
3. You need to know the region that you would like to deploy to.

Once you have all the information, the create request is a `POST` to the `https://resource-controller.bluemix.net/v2/resource_instances` endpoint.

```
curl -X POST \
  https://resource-controller.bluemix.net/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "my-instance",
    "target": "bluemix-us-south",
    "resource_group": "5g9f447903254bb58972a2f3f5a4c711",
    "resource_plan_id": "databases-for-postgresql-standard"
  }'
```

The parameters `name`, `target`, `resource_group`, and `resource_plan_id` are all required. You can send in the same additional parameters to the API as the CLI: `backup_id`, `version`, `key_protect_key`, and `members_memory_allocation_mb`. Add the parameters that you need to the request body.

Sample Response (formatted)

```
{
  "id": "crn:v1:bluemix:public:databases-for-postgresql:us-south:a/274074dce64e9c423ffc238516c755e1:5269c1f5-0c91-4b7a-9266-6083dfd86c96::",
  "guid": "5269c1f5-0c91-4b7a-9266-6083dfd86c96",
  "url": "/v2/resource_instances/5269c1f5-0c91-4b7a-9266-6083dfd86c96",
  "created_at": "2018-10-23T15:50:57.839880242Z",
  "updated_at": "2018-10-23T15:50:57.839880242Z",
  "deleted_at": null,
  "name": "my-api-instance",
  "region_id": "us-south",
  "account_id": "274074dce64e9c423ffc238516c755e1",
  "resource_plan_id": "databases-for-postgresql-standard",
  "resource_group_id": "6ac1aa3b70a84f1e98ea0d9c4687dc99",
  "resource_group_crn": "crn:v1:bluemix:public:resource-controller::a/274074dce64e9c423ffc238516c755e1::resource-group:6ac1aa3b70a84f1e98ea0d9c4687dc99",
  "target_crn": "crn:v1:bluemix:public:globalcatalog::::deployment:databases-for-postgresql-standard%3Aus-south",
  "crn": "crn:v1:bluemix:public:databases-for-postgresql:us-south:a/274074dce64e9c423ffc238516c755e1:5269c1f5-0c91-4b7a-9266-6083dfd86c96::",
  "state": "inactive",
  "type": "service_instance",
  "sub_type": "Public",
  "resource_id": "databases-for-postgresql",
  "dashboard_url": "https://console.us-south.databases.cloud.ibm.com/?instance_id=crn%3Av1%3Abluemix%3Apublic%3Adatabases-for-postgresql%3Aus-south%3Aa%2F274074dce64e9c423ffc238516c755e1%3A5269c1f5-0c91-4b7a-9266-6083dfd86c96%3A%3A\\u0026platform=ibm",
  "last_operation": {
    "type": "create",
    "state": "in progress",
    "description": null,
    "updated_at": "2018-10-23T15:50:57.839880242Z"
  },
  "resource_aliases_url": "/v2/resource_instances/5269c1f5-0c91-4b7a-9266-6083dfd86c96/resource_aliases",
  "resource_bindings_url": "/v2/resource_instances/5269c1f5-0c91-4b7a-9266-6083dfd86c96/resource_bindings",
  "resource_keys_url": "/v2/resource_instances/5269c1f5-0c91-4b7a-9266-6083dfd86c96/resource_keys",
  "plan_history": [
    {
      "resource_plan_id": "databases-for-postgresql-standard",
      "start_date": "2018-10-23T15:50:57.839880242Z"
    }
  ],
  "migrated": false,
  "controlled_by": ""
}
```

More information on the Resource Controller API is found in its [API Reference](https://{DomainName}/apidocs/resource-controller#create-provision-a-new-resource-instance)
.




