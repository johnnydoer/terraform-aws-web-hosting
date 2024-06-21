# Virtual Private Cloud

Now that we have everything set up, we can start writing Terraform code and creating infrastructure in AWS.

### Create a \`.gitignore\` file

We will first start with creating a "_.gitignore_" file that will make sure that none of the sensitive or unnecessary files that are created are pushed to your GitHub repository.

1. Create a file in your repository called .gitignore (mind the dot in the beginning).
2. Copy the contents from [here](https://github.com/github/gitignore/blob/main/Terraform.gitignore) to the .gitignore file.

This `.gitignore` file ensures that sensitive files such as Terraform state files and variable definitions are not pushed to your GitHub repository.

### Creating Terraform files

Next, we'll create the essential Terraform files (consider these as the main.py for a Python project). These files form the base of your Terraform project.

Create the following files in the root of our project directory:

* main.tf
  * Defines the primary resources to be created.
* outputs.tf
  * Specifies the output values that you can use to display information about the resources that were provisioned in AWS.
* provider.tf
  * Configures the AWS provider, which is necessary to interact with AWS services.
* variables.tf
  * Declares the variables used in your Terraform configuration, allowing you to customize your setup without modifying the main code.

Let's start with configuring our AWS provider.
    {% code title="provider.tf" %}
    ```yaml
    # Specify the AWS provider and region
    provider "aws" {
      region = "us-west-2" # AWS region where resources will be deployed
    }
    ```
    {% endcode %}

I am using the region "us-west-2" as it is closest to my location, you can select whichever is closest to you. You can find the list of regions [here](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html#Concepts.RegionsAndAvailabilityZones.Regions).

Now that we have configured our provider, run the following command to fetch all the code from Terraform that will be required to interact with the provider, in this case, AWS.



```bash
terraform init
```
