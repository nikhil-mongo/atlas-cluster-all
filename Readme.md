# Example - A basic example to start with the MongoDB Atlas and Terraform

This project aims to provide a very straight-forward example of setting up Terraform with MongoDB Atlas.


## Dependencies

* Terraform v0.13
* A MongoDB Atlas account - provider.mongodbatlas: version = "~> 0.6.5"

## Usage

**1\. Ensure your MongoDB Atlas credentials are set up.**

This can be done using environment variables:

```bash
export MONGODB_ATLAS_PUBLIC_KEY="xxxx"
export MONGODB_ATLAS_PRIVATE_KEY="xxxx"
```

... or follow as in the `variables.tf` file and create **terraform.tfvars** file with all the variable values and make sure **not to commit it**.

**2\. Review the Terraform plan.**

Execute the below command and ensure you are happy with the plan.

``` bash
$ terraform plan
```
This project currently does the below deployments:

- Atlas Project
- MongoDB cluster - M10
- Database User
- IP Whitelist

**3\. Configure the security group as required.**

The security group in this configuration allows All Traffic access in Inbound and Outbound Rules.

**4\. Execute the Terraform apply.**

Now execute the plan to provision the AWS and Atlas resources.

``` bash
$ terraform apply
```

**5\. Destroy the resources.**

Once you are finished your testing, ensure you destroy the resources to avoid unnecessary charges.

``` bash
$ terraform destroy
```

**Important Point**

To fetch the connection string follow the below steps:
```
output "atlasclusterstring" {
    value = mongodbatlas_cluster.cluster.connection_strings
}
```
**Outputs:**
```
atlasclusterstring = [
  {
    "aws_private_link" = {
      "vpce-0ebb76559e8affc96" = "mongodb://pl-0-us-east-1.za3fb.mongodb.net:1024,pl-0-us-east-1.za3fb.mongodb.net:1025,pl-0-us-east-1.za3fb.mongodb.net:1026/?ssl=true&authSource=admin&replicaSet=atlas-d177ke-shard-0"
    }
    "aws_private_link_srv" = {
      "vpce-0ebb76559e8affc96" = "mongodb+srv://mongodb-atlas-pl-0.za3fb.mongodb.net"
    }
    "private" = ""
    "private_srv" = ""
    "standard" = "mongodb://mongodb-atlas-shard-00-00.za3fb.mongodb.net:27017,mongodb-atlas-shard-00-01.za3fb.mongodb.net:27017,mongodb-atlas-shard-00-02.za3fb.mongodb.net:27017/?ssl=true&authSource=admin&replicaSet=atlas-d177ke-shard-0"
    "standard_srv" = "mongodb+srv://mongodb-atlas.za3fb.mongodb.net"
  },
]
```

To fetch a particular connection string, use the **lookup()** function of terraform as below:

```
output "plstring" {
    value = lookup(mongodbatlas_cluster.cluster.connection_strings[0].aws_private_link_srv, aws_vpc_endpoint.ptfe_service.id)
}
```
**Output:**
```
plstring = mongodb+srv://cluster-atlas-pl-0.za3fb.mongodb.net
```
