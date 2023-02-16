# HashiCorp Infrastructure Automation Certification / Terraform Associate (002) Study  (Theory) Notes

**Date:** *02-14-2023* 
**Author:** [`@GangGreenTemperTatum`](https:github.com/ganggreentempertatum) 

### Resources:
* [HashiCorp Infrastructure Automation Certification](https://www.hashicorp.com/certification/Terraform-associate)
* [Prepare for Terraform Certification (002)](https://developer.hashicorp.com/Terraform/tutorials/certification?product_intent=Terraform)
* [Exam Review - Terraform Associate Certification (002)](https://developer.hashicorp.com/Terraform/tutorials/certification/associate-review)
* [Terraform: Up and Running, 3rd Edition](https://www.oreilly.com/library/view/Terraform-up-and/9781098116736/)
* [HashiCorp Terraform GitHub Repo](https://github.com/hashicorp/Terraform)
* [Terraform Extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform)

### Lab Resources: (TODO)
* [Perform CRUD Operations with Providers](https://developer.hashicorp.com/terraform/tutorials/providers/provider-use) AKA `HashiCups`
	* [terraform-provider-hashicups](https://github.com/hashicorp/terraform-provider-hashicups)
*  [Lock and Upgrade Provider Versions](https://developer.hashicorp.com/terraform/tutorials/configuration-language/provider-versioning)


| X | Y |
| ------ | ------ |
| A | [X](X) |
| B | [X](X) |
| C | [X](X) |
| D | [X](X) |
| E | [X](X) |
| F |[X](X) |

## What is immutable infrastructure?

* Immutable infrastructure is an approach to managing services and software deployments on IT resources wherein components are replaced rather than changed.

------------------------------------------------------------------------

## **(Expand to view Terraform Exam Review Certification Concepts:)**

------------------------------------------------------------------------

### **1) Understand Infrastructure as Code (IaC) concepts**

<details>
<summary><b>Explain what IaC is</b></summary>
<br>
* Infrastructure as code (IaC) tools allow you to manage infrastructure with configuration files rather than through a graphical user interface. IaC allows you to build, change, and manage your infrastructure in a safe, consistent, and repeatable way by defining resource configurations that you can version, reuse, and share.

```
- Terraform is an IaC tool to define both cloud and on-prem resources in human-readable configuration files (Modules) that you can version, reuse, and share. 
- Terraform is open-source.
- Terraform can manage low-level components like compute, storage, and networking resources, as well as high-level components like DNS entries and SaaS features.
- Terraform creates and manages resources on cloud platforms and other services through their application programming interfaces (APIs).

Provider example = AWS, GCP, Azure

$ terraform init .. prompts Terraform to install the provider with a registry, and alternatively can be done with a `provider` block located in the local plugins directory
(if custom, need to download and compile the binary to build from source and requires GoLang)
```

* Terraform's language is stored in a plain-text file with the `.tf` file extension
* There is also a JSON-based variant of the language that is named with the `.tf.json` file extension.

### **IaC and the Infrastructure Lifecycle**

1) Provisioning Day 0 Deployment = Running nothing to something
2) Provisioning Day 1 Deployment = Existing set of infrastructure that evolves

* [Terraform Registry](https://registry.Terraform.io/) includes publically available provider templates

### **Terraform Workflow**

```
############ Write: ############
You define resources, which may be across multiple cloud providers and services.
Configuration components called Modules define configutable collections of infrastructure.

############ Plan: (Topology) ############
Terraform creates an execution plan describing the infrastructure it will create, update, or destroy based on the existing infrastructure and your configuration.

$ Terraform refresh .. Queries Registry provider's API's for up-to-date topology of infrastructure

$ Terraform plan .. Reconcile what is running with desired configuration, compares the monolithic core against the state file

############ Apply: ############
On approval, Terraform performs the proposed operations in the correct order, respecting any resource dependencies. Following this, Terraform updates the "State" file
Resource dependencies are identified via Terraform's built Resource Graphs and creates or modifies non-dependent resources in parallel.

$ Terraform apply .. Makes the plan a destination configuration and state

$ Terraform destroy .. Destroys elements via a plan at the end of a lifecycle to cleanup and decom
```

* Terraform keeps track of your real infrastructure (as a topology) in a [state file](https://developer.hashicorp.com/Terraform/language/state)(`terraform.tfstate`), which acts as a source of truth for your environment and is extremely important to reflect accurate for the workflow.
> * Terraform diffs the state file to reality and makes changes to your configuration based on that.
> * The configuration files are `declarative`, meaning they describe the **end-state** of the infrastructure
> * Configuration files can be version controlled, approved and secured within CI/CD pipelines and collaborated via code repositories and private registries.

### **Terraform Workflow**

* `State` file should always be centrally managed, no unique lineages or forks to avoid conflicts whilst allowing collaboration to ensure consistent true reflection and deployment
	* Central collaboration is similar to GitHub teams and HashiCorp's solution is a version controlled system called Terraform Enterprise
	* Terraform also allows non-HashiCorp centralized repositories
* Local Environment Variables are used for secrets and other sensitive data, normally stored on single developer's machines
	* Variables should be stored centrally, secured by RBAC and least-privilege
	* This is resolved with Inputs & Output variables to Terraform `Modules`

### Terraform Cloud vs Terraform Enterprise

* All the features of Terraform Cloud and Terraform Enterprise are the same except additional features in Terraform Enterprise are audit logging, SAML single sign-on, private instance with no limits etc. SAML Single sign on- Terraform Enterprise supports SAML 2.0, And it works with a variety of identity providers.

> * [Terraform Cloud](https://developer.hashicorp.com/Terraform/intro/Terraform-editions#Terraform-cloud) is HashiCorp's Terraform SaaS.
> 	* Terraform Cloud Team and Governance tier is available with Sentinel for policy-as-code compliance & governance
> * [Terraform Enterprise](https://developer.hashicorp.com/Terraform/enterprise) is a self-hosted distribution of Terraform Cloud. It offers enterprises a private instance of the Terraform Cloud application, with no resource limits and with additional enterprise-grade architectural features like audit logging and SAML single sign-on.
> 	* Terraform Enterprise also allows for private registries for hosting modules
<br>
</details>

<details>
<summary><b>Describe advantages of IaC patterns</b></summary>
<br>

* Automation for efficiency of deployment and maintenance 
* Version control, transparency of documentation and auditing of logs by checking into version control systems, E.G GitHub, GitLab, BitBucket
* Cleaner CI/CD pipeline with approvals
* Elasticity of infrastructure
	* Allows scaling up and down of infrastructure with ease
	* Manual method of building/maintaining infrastructure is not elastic, robust and prone to errors/time consuming
* Terraform can help you enforce policies on the types of resources teams can provision and use which alleviates long-winded ticket-based review processes that bottleneck deploment
	* Can also use Sentinel (policy-as-code framework) to enforce compliance and governance policies prior to Terraform involvement
* Ability to collaborate internally and externally
* Existing registries and open-source provides ease of deployment and a wider capabilty
* Terraform state acts as a source of truth for your environment with up-to-date information
* Standardizes workflows to reduce risk of human errors
* Easy to provision and apply with a common syntax across all infrastructure providers
* IaC makes changes idempotent, consistent, repeatable and predictable
* Easier form of rollback if required
* Code of IaC can be tested, prior to execution in target environment
	* We can iterate on the code until results pass tests and align expectations

<br>
</details>

------------------------------------------------------------------------

### **2) Understand Terraform's purpose (vs other IaC)**

<details>
<summary><b>Explain multi-cloud and provider-agnostic benefits</b></summary>
<br>

* Increases fault-tolerance, allowing for more graceful recovery from cloud provider outages

> *Disadvantages:
> 	* Each cloud provider having unique interface, tools and workflows can add complexity
> 	* This is resolved with Terraform's single configuration syntax approach

<br>
</details>

<details>
<summary><b>Explain the benefits of `state`</b></summary>
<br>

* For mapping configuration to resources in the real world, Terraform uses its own state structure.
	* The Terraform state is treat as a single source of truth which allows collaboration and an efficient CI/CD pipeline with auditing, approvals, versioning etc.
	* By default, when running a `$ terraform plan` or `$ terraform apply`, Terraform queries your providers to sync the latest attributes from resources
	* This can be laborious and slow for large infrastructure and lead to API rate limiting, as well as introduce latency
	* As such, Terraform can tailor performance with cache of attribute values for all resources in the state
* If importing objects outside fo Terraform (not recommended), you must ensure each distinct object is imported only to one resource to avoid being ambiguous to cause unexpected behavior
	* **Remote State** is a solution to this problem which can avoid conflicts, human error and complexity to the infrastructure whilst allowing developer collaboration
* An example of **metadata** for `State` (`terraform.tfstate`) is resource dependencies
	* Mappings exist in a state file between resources so Terraform can understand dependencies to apply configuration in the correct dependency order
	* Terraform retains a copy of the most recent set of dependencies within the state. 
		* Now Terraform can still determine the correct order for destruction from the state when you delete one or more items from the configuration.
	* Terraform also stores other metadata for similar reasons, such as a pointer to the provider configuration that was most recently used with the resource in situations where multiple aliased providers are present.

<br>
</details>

------------------------------------------------------------------------

### **3) Understand Terraform basics**	

<details>
<summary><b>Handle Terraform and provider installation and versioning</b></summary>

### Terraform Providers
* Providers (AWS, GCP, SaaS providers etc.) allow Terraform to interact via API's
* Terraform `.tf` files must **declare** providers used
* Provider configurations belong in the `root` module of a Terraform configuration
	* Child modules receive provider configurations from root module
	* Example of a provider code block
- "google" is given local name of provider which is included in the `required_providers` block

```
provider "google" {  <---- Start of argument
  project = "acme-app" <---- Example argument
  region  = "us-central1" <---- Example argument
} <---- EOF argument
```

- Expressions are allowed in the values of configuration arguments but only that can be known before the configuration is applied
	- I.E, input variables
- Terraform assumes an empty default configuration for any provider that is not explicitly configured

* Terraform `alias` (`configuration_aliases`) allows for multiple configurations for the same provider to use on a per-resource or module basis to support multiple regions for a cloud platform, or targeting multiple Docker/Consul hosts etc.
	* Anything not defined as an alias is considered **Default Provider Configuration**
	* When Terraform needs the name of a provider configuration, it expects a reference of the form `<PROVIDER NAME>.<ALIAS>`

* Terraform `version` (**depredacted**) specifies a version constraint for a provider

### Terraform Settings

* Terraform settings are gathered together into `terraform` blocks:

```
terraform {
	# ...
}
```

* Each `terraform` block can contain a number of settings related to Terraform's behavior.
* Within a `terraform` block, only constant values can be used; 
	* arguments may not refer to named objects such as resources, input variables, etc, and may not use any of the Terraform language built-in functions.*

### Terraform Settings nested blocks:

* **`cloud`** nested-block enables CLI-driven run workflow
* **`backend`** nested-block defines the state data files storage location
	* `local` (default - current working dir `.terraform/`), `backend` (non Terraform-cloud, remote backend)  
* **`required_version`** block defines the version constraint string, specific version(s) of Terraform to be used within config file - Applies only to the version of Terraform CLI
* **`required_providers`** block specifies all providers required by the current module
	* Maps each local provider name to a source access and version constraint
* **`experiments`** is for beta-based new language features when opting-in
* **`provider_meta`** nested block for each provider a module is using, if the provider defines a scheme for it

* When multiple users or automation tools run the same Terraform configuration, they should all use the same versions of their required providers. There are two ways for you to manage provider versions in your configuration.
1.  Specify provider version constraints in your configuration's `terraform` block.
2.  Use the [dependency lock file](https://developer.hashicorp.com/terraform/language/files/dependency-lock)
	1. Located in the current working directory when running Terraform which also contains `.tf` files for the root module of configuration
	2. Lock file is always named `terraform.lock.hcl`
	3. Lock file is always created or updated when running the `$ terraform init` command
	4. File should be included in version-control repository
	5. No existing recorded selection -> newest available version
	6. Performs checksum verification `trust on first use` approach
```
Terraform has made some changes to the provider dependency selections recorded
in the .terraform.lock.hcl file. Review those changes and commit them to your
version control system if they represent changes you intended to make.
```
* By default, Terraform will download the latest provider version that fulfills the version constraint (determine which versions of dependencies are _potentially_ compatible) and could lead to unexpected infrastructure changes unless you specify the carefully scoped provier versions.
* Using the `dependency lock` file, you can ensure Terraform is using the correct provider version consistently.

<br>
</details>

<details>
<summary><b>Describe plug-in based architecture</b></summary>
<br>

### **CRUD** (term describes the four essential operations for creating and managing persistent data elements) with providers

1) **Create**
2) **Read**
3) **Update**
4) **Delete**

* Terraform **CORE** reads configuration and builds dependency graphs
* Terraform **PLUGINS** ([Terraform Plugin SDK v2](https://github.com/hashicorp/terraform-plugin-sdk)) bridge the gap between Terraform Core and providers and their respective target APIs.
* Terraform **PROVIDER PLUGINS**implement resources via basic CRUD-based API's, Terraform CORE performs RPC interface-based request to target's API library upon `$ terraform plan` and `$ terraform apply` actions
* Build infrastructure provider:
	* [Perform CRUD Operations with Providers](https://developer.hashicorp.com/terraform/tutorials/providers/provider-use) AKA `HashiCups` is an example of a Terraform Provider and CRUD-based API to manage resources.

<br>
</details>

<details>
<summary><b>Demonstrate using multiple providers</b></summary>
<br>

* See "[]()"

<br>
</details>

<details>
<summary><b>Describe how Terraform finds and fetches providers</b></summary>
<br>

* See "[]()"

<br>

</details>

<details>
<summary><b>Explain when to use and not use provisioners and when to use local-exec or remote-exec</b></summary>

<br>

```
code
```

<br>
</details>

------------------------------------------------------------------------

### 4)Use the Terraform CLI (outside of core workflow)	

<details>
<summary><b>Given a scenario: choose when to use `Terraform fmt` to format code</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Given a scenario: choose when to use `Terraform taint` to taint Terraform resources</b></summary>
<br>

```
code
```

<br>
</details>

</details>

<details>
<summary><b>Given a scenario: choose when to use terraform import to import existing infrastructure into your Terraform state</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Given a scenario: choose when to use terraform workspace to create workspaces</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Given a scenario: choose when to use terraform state to view Terraform state</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Given a scenario: choose when to enable verbose logging and what the outcome/value is	</b></summary>
<br>

```
code
```

<br>
</details>

------------------------------------------------------------------------

### 5) Interact with Terraform Modules	

<details>
<summary><b>Contrast module source options</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Interact with module inputs and outputs</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Describe variable scope within modules/child modules</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Discover modules from the public Terraform Registry</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Defining module version</b></summary>
<br>

```
code
```

<br>
</details>


------------------------------------------------------------------------

### 6) Navigating Terraform Workflow	

<details>
<summary><b>Describe Terraform workflow ( Write -> Plan -> Create )</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Initialize a Terraform working directory (terraform init)</b></summary>
<br>

```
code
```

<br>
</details>
<details>
<summary><b>Validate a Terraform configuration (terraform validate)</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Generate and review an execution plan for Terraform (terraform plan)</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Destroy Terraform managed infrastructure (terraform destroy)</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Validate a Terraform configuration (terraform validate)</b></summary>
<br>

```
code
```

<br>
</details>

------------------------------------------------------------------------

### 7) Implement and maintain state

<details>
<summary><b>Describe default local backend</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Outline state locking</b></summary>
<br>

```
code
```

<br>
</details>
<details>
<summary><b>Handle backend authentication methods</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Describe remote state storage mechanisms and supported standard backends</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Describe effect of Terraform refresh on state</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Describe backend block in configuration and best practices for partial configurations</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Understand secret management in state files</b></summary>
<br>

```
code
```

<br>
</details>

------------------------------------------------------------------------

### 8) Read, generate, and modify configuration

<details>
<summary><b>Demonstrate use of variables and outputs</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Describe secure secret injection best practice</b></summary>
<br>

```
code
```

<br>
</details>
<details>
<summary><b>Create and differentiate resource and data configuration</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Use resource addressing and resource parameters to connect resources together</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Use Terraform built-in functions to write configuration</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Configure resource using a dynamic block</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Describe built-in dependency management (order of execution based)</b></summary>
<br>

```
code
```

<br>
</details>

------------------------------------------------------------------------

### 9) Understand Terraform Cloud and Enterprise capabilities

<details>
<summary><b>Describe the benefits of Sentinel, registry, and workspaces</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Differentiate OSS and Terraform Cloud workspaces</b></summary>
<br>

```
code
```

<br>
</details>
<details>
<summary><b>Summarize features of Terraform Cloud</b></summary>
<br>

```
code
```

------------------------------------------------------------------------------------------------------------------------------------------------------

💾 `EOF`
