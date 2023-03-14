# HashiCorp Infrastructure Automation Certification / Terraform Associate (002) Study  (Theory) Notes

**Date:** *02-14-2023* 

**Terraform >= v0.15**

**Author:** [`@GangGreenTemperTatum`](https://github.com/GangGreenTemperTatum) 

### Resources:
* [HashiCorp Infrastructure Automation Certification](https://www.hashicorp.com/certification/Terraform-associate)
* [Prepare for Terraform Certification (002)](https://developer.hashicorp.com/Terraform/tutorials/certification?product_intent=Terraform)
* [Exam Review - Terraform Associate Certification (002)](https://developer.hashicorp.com/terraform/tutorials/certification/associate-review)
* [Terraform: Up and Running, 3rd Edition](https://www.oreilly.com/library/view/Terraform-up-and/9781098116736/)
* [HashiCorp Terraform GitHub Repo](https://github.com/hashicorp/Terraform)
* [Install Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
* [Sample Questions - Terraform Associate Certification](https://developer.hashicorp.com/terraform/tutorials/certification/associate-questions)
* [Terraform Extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform)

### Lab Resources: (TODO)
* [Apply Terraform Configuration](https://developer.hashicorp.com/terraform/tutorials/cli/apply?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS)
* [Perform CRUD Operations with Providers](https://developer.hashicorp.com/terraform/tutorials/providers/provider-use) AKA `HashiCups`
	* [terraform-provider-hashicups](https://github.com/hashicorp/terraform-provider-hashicups)
* [Store Remote State](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-remote)
	* Migrate Terraform `local` backend configuration to remote Terraform Cloud ☁️ workspace
* [An Ultimate Terraform Hands-on Labs](https://collabnix.github.io/terraform/)
	* [View on GitHub](https://github.com/collabnix/terraform)
*  [Lock and Upgrade Provider Versions](https://developer.hashicorp.com/terraform/tutorials/configuration-language/provider-versioning)

## What is immutable infrastructure?

* Immutable infrastructure is an approach to managing services and software deployments on IT resources wherein components are replaced rather than changed.

------------------------------------------------------------------------

📚 `Exam Topics`

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

* After you initialize, Terraform creates a `.terraform/` directory locally.
	* **This directory contains the most recent backend configuration, including any authentication parameters you provided to the Terraform CLI. Do not check this directory into Git, as it may contain sensitive credentials for your remote backend.**

* Terraform's language is stored in a plain-text file with the `.tf` file extension
* There is also a JSON-based variant of the language that is named with the `.tf.json` file extension.

### **IaC and the Infrastructure Lifecycle**

| **Provisioning Day** | **Definition** |
| ------ | ------ |
| **Provisioning Day 0 Deployment** | Running nothing to something |
| **Provisioning Day 1 Deployment** | Existing set of infrastructure that evolves |

* [Terraform Registry](https://registry.Terraform.io/) includes publically available provider templates

### **Terraform Workflow**

| **Terraform Workflow State** | **Definition** |
| ------ | ------ |
| **Write:** | You define resources, which may be across multiple cloud providers and services. Configuration components called Modules define configutable collections of infrastructure. |
| **Plan: (Topology)** | Terraform creates an execution plan describing the infrastructure it will create, update, or destroy based on the existing infrastructure and your configuration. <br> `$ Terraform refresh` .. Queries Registry provider's API's for up-to-date topology of infrastructure. <br> `$ Terraform plan` .. Reconcile what is running with desired configuration, compares the monolithic core against the state file |
| **Apply:** | On approval, Terraform performs the proposed operations in the correct order, respecting any resource dependencies. Following this, Terraform updates the "State" file. Resource dependencies are identified via Terraform's built Resource Graphs and creates or modifies non-dependent resources in parallel. <br> `$ Terraform apply` Makes the plan a destination configuration and state. <br> `$ Terraform destroy` .. Destroys elements via a plan at the end of a lifecycle to cleanup and decom<br> |

* Terraform keeps track of your real infrastructure (as a topology) in a [state file](https://developer.hashicorp.com/Terraform/language/state)(`terraform.tfstate`), which acts as a source of truth for your environment and is extremely important to reflect accurate for the workflow..**Add credentials and sensitive data as environment variables here to prevent leaking of sensitive information**
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

### Terraform Cloud ☁️ vs Terraform Enterprise

* All the features of Terraform Cloud ☁️ and Terraform Enterprise are the same except additional features in Terraform Enterprise are audit logging, SAML single sign-on, private instance with no limits etc. SAML Single sign on- Terraform Enterprise supports SAML 2.0, And it works with a variety of identity providers.

> * [Terraform Cloud](https://developer.hashicorp.com/Terraform/intro/Terraform-editions#Terraform-cloud) is HashiCorp's Terraform SaaS.
> 	* Terraform Cloud ☁️ Team and Governance tier is available with Sentinel for policy-as-code compliance & governance
> 	* Terraform Cloud ☁️ provides a centralized and secure location for storing input variables and state while also bringing back a tight feedback loop for speculative plans for config authors.
> 		* Terraform configuration can interact with Terraform Cloud ☁️ through the CLI integration and team members just need a Terraform Cloud ☁️ API key to edit config and run speculative plans against the latest version of the state file using all of the remotely stored input variables. (`$ terraform workspace select <workspace>`, followed by normal Terraform Workflow commands)
> 		* Terraform Cloud ☁️ automatically runs when the pull request is completed and features a CI pipeline approval process as well as live implementation output, similar to GitLab
> * [Terraform Enterprise](https://developer.hashicorp.com/Terraform/enterprise) is a self-hosted distribution of Terraform Cloud ☁️  It offers enterprises a private instance of the Terraform Cloud ☁️ application, with no resource limits and with additional enterprise-grade architectural features like audit logging and SAML single sign-on.
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

| **Symbol** | **Definition** |
| ------ | ------ |
| 1 | **CREATE** |
| 2 | **READ** |
| 3 | **UPDATE** |
| 4 | **DELETE** |

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

*See above notes

<br>
</details>

<details>
<summary><b>Describe how Terraform finds and fetches providers</b></summary>
<br>

*See above notes

<br>

</details>

<details>
<summary><b>Explain when to use and not use provisioners and when to use local-exec or remote-exec</b></summary>

<br>

### Provisioners:

* Provisioners are a last resort since there are always certain behaviors that cannot be directly represented in Terraform's declarative model.
	* Add complexity and uncertainty in Terraform usage
	* ** Terraform cannot model the actions of provisioners as part of a plan because they can in principle take any action.** (god mode) and also require more details than Terraform usage usually requires. Exampls:
		* Direct network access to servers
		* Issuing Terraform credentials
*** Provisioners are used to execute scripts on a local or remote machine as part of resource creation or destruction.** 
	* Provisioners can be used to bootstrap a resource, cleanup before destroy, run configuration management, etc.

* Add a provisioner block inside the resource block of a compute instance.

```
resource "aws_instance" "web" {
  # ...

  provisioner "local-exec" {
    command = "echo The server's IP address is ${self.private_ip}"
  }
}
```

### Examples of where Provisioners are required:

**1) Passing data into virtual machines and other compute resources**
- Pass in data to a compute resource for related infrastructure on that the software on the server requires as a dependency OR custom machine images
- Package updates/libraries/dependencies
	- Think of this like a container image layers

**2) Running configuration management software**
- Avoid to use Terraform specialized provisioners provided for launching specific configuration management products
- Highly recommended to use [HashiCorp Packer](https://www.packer.io/) (Build Automated Machine Images) that offers a similar complement of configuration management provisioners and can run their installation steps during a separate build process, before creating a system disk image that you can deploy many times.

**3) First-class Terraform provider functionality may be available**
- When there is no official provider-Terraform support for interacting with remote objects in that system
- `local-exec` usage is highly suggested as a workaround, intending to build a "custom provider"

<br>
</details>

------------------------------------------------------------------------

### 4)Use the Terraform CLI (outside of core workflow)	

<details>
<summary><b>Given a scenario: choose when to use `Terraform fmt` to format code</b></summary>
<br>

* `terraform fmt` command is used to rewrite Terraform configuration files to a different format and style, mainly for readability
	* Available [Terraform language style conventions](https://developer.hashicorp.com/terraform/language/syntax/style):
* Ensure to stay consistent with formatting style imposed by the `terraform fmt` command. Terraform will also produce files that confirm to the style imposed by this command.
* The `terraform fmt` command is also recommended during Terraform upgrades to ensure consistency and prevent errors during upgrade that may mangle the syntax and style applied.
    
<br>
</details>

<details>
<summary><b>Given a scenario: choose when to use `Terraform taint` to taint Terraform resources</b></summary>
<br>

########## **Deprecated** ########## (recommend `-replace` option _>=v0.15.2_)
* `terraform taint` command informs Terraform that a particular object has become degraded or damaged and marks it as such within the Terraform state file to propose a replace in the next upcoming Terraform plan.
* Example of using Terraform (`-replace`) option to replace object with no configuration changes:

```
terraform apply -replace="aws_instance.example[0]"
```

* This will ensure that the change will be reflected in the Terraform plan and enhance externally-visible actions

<br>
</details>

</details>

<details>
<summary><b>Given a scenario: choose when to use `terraform import` to import existing infrastructure into your Terraform state</b></summary>
<br>

* The `terraform import` command [imports existing resources](https://developer.hashicorp.com/terraform/cli/import) into Terraform.
* **If you import existing objects into Terraform, be careful to import each remote object to only one Terraform resource address.** .. failure to do so could cause failure in Terraform's state.
* Import only imports resources into the state and does not generate any configuration
	* Beforehand, you need to manually define a `resource` configuration block for the resource to create and assign the mapping

<br>
</details>

<details>
<summary><b>Given a scenario: choose when to use `terraform workspace` to create workspaces</b></summary>
<br>

* Each Terraform configuration has an associated backend that defines how Terraform executes operations and where Terraform stores persistent data, such as State.
* This persistent data belongs to a **workspace**, by default one-to-one mapping
* Having some backends supporting multiple named workspaces, allows multiple states to be associated with a single configuration
* Multiple workspaces are acceptable with the following backends:
	* AzureRM
	* Consul
	* COS
	* GCS
	* Kubernetes
	* Local
	* OSS
	* Postgres
	* Remote
	* S3

<br>
</details>

<details>
<summary><b>Given a scenario: choose when to use `terraform state` to view Terraform state</b></summary>
<br>

* `terraform state` command (nested sub-command) can be used to modify the state with `terraform state <subcommand> [options] [args]`, instead of modifying the state itself.
* The Terraform state subcommands all work with remote state just as if it was local state.
* All terraform state subcommands that modify the state write backup files. The path of these backup file can be controlled with -backup.

<br>
</details>

<details>
<summary><b>Given a scenario: choose when to enable verbose logging and what the outcome/value is	</b></summary>
<br>

* Using vebose logging and debugging is designed for potential bugs found in Terraform actions and behaviors
* Setting the `TF_LOG` environment variable to any value will cause detailed logs to appear on `stderr`:
	* `TRACE`
	* `DEBUG`
	* `INFO`
	* `WARN`
	* `ERROR
* Setting the `TF_LOG` environment variable to `JSON` outputs logs at the `TRACE` level or higher
* To persist logged output you can set `TF_LOG_PATH` in order to force the log to always be appended to a specific file when logging is enabled. 
* When `TF_LOG_PATH` is set, `TF_LOG` must be set in order for any logging to be enabled.

<br>
</details>

------------------------------------------------------------------------

### 5) Interact with Terraform Modules	

## **Modules**

```
_Modules_ are containers for multiple resources that are used together. A module consists of a collection of `.tf` and/or `.tf.json` files kept together in a directory.
```

* A Terraform module is a set of Terraform configuration files in a single directory. 
* Even a simple configuration consisting of a single directory with one or more `.tf` files is a module. I.E:
	* Running Terraform commands from the directory ensures that it is considered the **root** module.

```
.
├── LICENSE
├── README.md
├── main.tf
├── variables.tf
├── outputs.tf
```

* Terraform configuration can use module blocks to call modules in other directories.
	* AKA **"child module"**
* Modules can be local filesystem or a remote source (I.E Terraform Registry)

<details>
<summary><b>Contrast module source options</b></summary>
<br>

* Modules are resources within providers and are version controlled (must conform to [semantic versioning](http://semver.org/))

* The **public** [Terraform Registry](https://registry.terraform.io/) makes it simple to find and use modules for easy implementation of pre-built configuration templates
* HashiCorp also ensures by default only verified (by HC) Modules are displayed in the registry
* This registry is also integrated directly into Terraform
* Types of modules = `vault`, `vpc`, `database`
* Example of specifying a Module within Terraform from the registry:

`<NAMESPACE>/<NAME>/<PROVIDER>`
`hashicorp/consul/aws`

* **Private** registries containing Modules have source strings with the following form: (same as public registry with added `<HOSTNAME>` prefix)

`<HOSTNAME>/<NAMESPACE>/<NAME>/<PROVIDER>`

<br>
</details>

<details>
<summary><b>Interact with module inputs and outputs</b></summary>
<br>

-   **Input variables** are like function arguments.
	- Input variables let you customize aspects of Terraform modules without altering the module's own source code.
	- Each input variable accepted by a module must be declared using a variable block
	- Within the module that declared a variable, its value can be accessed from within [expressions](https://developer.hashicorp.com/terraform/language/expressions) as `var.<NAME>`, where `<NAME>` matches the label given in the declaration block
-   **[Output values]**(https://developer.hashicorp.com/terraform/language/values/outputs) are like function return values.
	- Output values make information about your infrastructure available on the command line, and can expose information for other Terraform configurations to use. 
	- Output values are similar to return values in programming languages.
	- Each output value exported by a module must be declared using an `output` block
		- Outputs are only rendered when Terraform applies your plan. 
		- Running terraform plan will not render outputs
-   **[Local values]**(https://developer.hashicorp.com/terraform/language/values/locals) are like a function's temporary local variables.

* Module input resources are encpsulated so the calling module cannot access their attributes directly. However, a child module can declare [output values](https://developer.hashicorp.com/terraform/language/values/outputs) to selectively export certain values to be accessed by the calling module.

<br>
</details>

<details>
<summary><b>Describe variable scope within modules/child modules</b></summary>
<br>

* When you declare variables in the root module of your configuration, you can set their values using CLI options and environment variables. 
* When you declare them in [child modules](https://developer.hashicorp.com/terraform/language/modules), the calling module should pass values in the `module` block.
* **Terraform Variable Declarations:**
	-   [`default`](https://developer.hashicorp.com/terraform/language/values/variables#default-values) - A default value which then makes the variable optional.
	-   [`type`](https://developer.hashicorp.com/terraform/language/values/variables#type-constraints) - This argument specifies what value types are accepted for the variable.
	-   [`description`](https://developer.hashicorp.com/terraform/language/values/variables#input-variable-documentation) - This specifies the input variable's documentation.
	-   [`validation`](https://developer.hashicorp.com/terraform/language/values/variables#custom-validation-rules) - A block to define validation rules, usually in addition to type constraints.
	-   [`sensitive`](https://developer.hashicorp.com/terraform/language/values/variables#suppressing-values-in-cli-output) - Limits Terraform UI output when the variable is used in configuration.
	-   [`nullable`](https://developer.hashicorp.com/terraform/language/values/variables#disallowing-null-input-values) - Specify if the variable can be `null` within the module.

* To _call_ a module (`calling a child module`) means to include the contents of that module into the configuration with specific values for its [input variables](https://developer.hashicorp.com/terraform/language/values/variables). 
* Modules are called from within other modules using `module` blocks

<br>
</details>

<details>
<summary><b>Discover modules from the public Terraform Registry</b></summary>
<br>

* By default, only `verified` modules are shown in search results within Terraform public registry
* The Terraform Registry is integrated directly into Terraform, so a Terraform configuration can refer to any module published in the registry.
* Registry module reference:

```
<NAMESPACE>/<NAME>/<PROVIDER> <---- Terraform public registry
hashicorp/consul/aws <---- Example verified module
<HOSTNAME>/<NAMESPACE>/<NAME>/<PROVIDER> <----  Private registry module
```

* The `terraform init` command will download and cache any modules referenced by a configuration. (think of this similar to `$ docker run` behavior)

<br>
</details>

<details>
<summary><b>Defining module version</b></summary>
<br>

* Each module in the registry is versioned. 
* These versions syntactically must follow [semantic versioning](http://semver.org/):

| **Semantic Versioning Conformity** | **Definition** |
| ------ | ------ |
| **MAJOR:** | Version when you make incompatible API changes  |
| **MINOR:** | Version when you add functionality in a backwards compatible manner  |
| **PATCH:** | Version when you make backwards compatible bug fixes  |

`MAJOR.MINOR.PATCH`

<br>
</details>

------------------------------------------------------------------------

### 6) Navigating Terraform Workflow	

<details>
<summary><b>Describe Terraform workflow (` Write -> Plan -> Create `)</b></summary>
<br>


| **Terraform Workflow State** | **Definition** |
| ------ | ------ |
| **Write:** | You define resources, which may be across multiple cloud providers and services. Configuration components called Modules define configutable collections of infrastructure. |
| **Plan: (Topology)** | Terraform creates an execution plan describing the infrastructure it will create, update, or destroy based on the existing infrastructure and your configuration. <br> `$ Terraform refresh` .. Queries Registry provider's API's for up-to-date topology of infrastructure. <br> `$ Terraform plan` .. Reconcile what is running with desired configuration, compares the monolithic core against the state file |
| **Apply:** | On approval, Terraform performs the proposed operations in the correct order, respecting any resource dependencies. Following this, Terraform updates the "State" file. Resource dependencies are identified via Terraform's built Resource Graphs and creates or modifies non-dependent resources in parallel. <br> `$ Terraform apply` Makes the plan a destination configuration and state. <br> `$ Terraform destroy` .. Destroys elements via a plan at the end of a lifecycle to cleanup and decom<br> |

* Terraform is written as code within `.tf` file extensions
* Terraform code from the current working dir, is initiated with `$ terraform init`..After you initialize, Terraform creates a `.terraform/` directory locally. 
	* **This directory contains the most recent backend configuration, including any authentication parameters you provided to the Terraform CLI. Do not check this directory into Git, as it may contain sensitive credentials for your remote backend.**
* `$ terraform apply` will always prompt a display of a plan prior to proceeding with changes and is considered **last review**
* Consider using `$ git add main.tf / $ git commit -m "Upload my TF code` to push your version control repository to a remote location for safekeeping

* The core Terraform Workflow is a loop from beginning to finish and will restart each time you wish to make changes
* Consider using `$ git checkout -b <branch> to use branches when implementing IaC as a team to prevent merge conflict workflows`
* Terraform Cloud ☁️ provides a centralized and secure location for storing input variables and state while also bringing back a tight feedback loop for speculative plans for config authors.
	* Terraform configuration can interact with Terraform Cloud ☁️ through the CLI integration and team members just need a Terraform Cloud ☁️ API key to edit config and run speculative plans against the latest version of the state file using all of the remotely stored input variables. (`$ terraform workspace select <workspace>`, followed by normal Terraform Workflow commands)
	* Terraform Cloud ☁️ automatically runs when the pull request is completed and features a CI pipeline approval process as well as live implementation output, similar to GitLab

<br>
</details>

<details>
<summary><b>Initialize a Terraform working directory (`terraform init`)</b></summary>
<br>

* The `$ terraform init` command initializes a working directory containing Terraform configuration files (configuration describing resources Terraform should manage, written in the Terraform Language) and initiates the Terraform Workflow lifecycle.
* After you initialize, Terraform creates a `.terraform/` directory locally.
		* **This directory contains the most recent backend configuration, including any authentication parameters you provided to the Terraform CLI. Do not check this directory into Git, as it may contain sensitive credentials for your remote backend.**
	* Run for new configurations or when cloning
		* This initiates the following steps: (**BY DEFAULT**)
			1) Root configuration directory is consulted for backend configuration and chosen backend is initialized using the given configuration settings
			2) Configuration is searched for `module` blocks and the source code for referenced modules is retrieved from their locations given in their source arguments
			3) Terraform already finds, downloads and installs the necessary provider plugins (third-party registry, public Terraform registry etc)
* Running this command multiple times, although safe will not remove any configuration or state but may present errors.
	* Will also update the working directory to use the new backend settings for intialized backend
	* Re-running init with modules already installed will install the sources for any modules that were added to configuration since the last init, but will not change any already-installed modules
* `-lock-timeout=<duration>` = **0 seconds** (immediate failure if the lock is already held by another process)
* `-from-module-=<MODULE_SOURCE>` option is for initializing Terraform with configuration sourced different from current working dir 

## Working Directory Contents

A Terraform working directory typically contains:
-   A Terraform configuration describing resources Terraform should manage. This configuration is expected to change over time. (I.E, `main.tf`)
-   A hidden `.terraform` directory, which Terraform uses to manage cached provider plugins and modules, record which [workspace](https://developer.hashicorp.com/terraform/cli/workspaces) is currently active, and record the last known backend configuration in case it needs to migrate state on the next run. This directory is automatically managed by Terraform, **and is created during initialization**.
-   State data, if the configuration uses the default `local` backend. This is managed by Terraform in a `./terraform.tfstate` file (if the directory only uses the default workspace) or a `terraform.tfstate.d` directory (if the directory uses multiple workspaces).

<br>
</details>
<details>
<summary><b>Validate a Terraform configuration (`terraform validate`)</b></summary>
<br>

* **Pre-Req:** Initialized working directory including any referenced modules and plugins installed
* The `terraform validate (JSON)` command validates the configuration files (syntactically valid, internally consistent etc) in a directory, referring only to the configuration and not accessing any remote services such as remote state, provider APIs, etc.
	* Useful for reusable modules and correctness of attribute names and types
* `terraform init -backend=false`
* Recommended to run `terraform plan` when using configuration verification in the context of a particular run, workspace or input veriables

<br>
</details>

<details>
<summary><b>Generate and review an execution plan for Terraform (`terraform plan`)</b></summary>
<br>

* **Use environment variables to supply credentials and other sensitive data. If you use -backend-config or hardcode these values directly in TF configuration, Terraform will include these values in both the `.terraform` subdirectory and in `plan` files. 
	* **`This can leak sensitive credentials.`**

* `terraform plan (-OUT=<STDOUT-TO-FILE>` command creates an execution plan, which lets you preview the changes that Terraform plans to make to your infrastructure
	* **DEFAULT PLANNING BEHAVIOR: (AKA `"Normal" Planning Mode`**
		1) Reads the current state of any already-existing objects to ensure it's up-to-date
		2) Compares  from the working directory of the current root configuration, to the state and marks diffs
		3) Proposes a set of change actions with the intention if applied to make the remote objects match the configuration
			1) `terraform show <FILE>`
* **Planning Modes:**
	* **Destroy Mode:** `-destroy`
		* Creates a plan containing an objective to destroy all remote objects currently existing and is the same as `terraform destroy`.
	* **Refresh-Only Mode**: 
		* Creates a plan containing an objective to update Terraform state and any root module output values to match changes made to remote objects outside of Terraform
		* Reconciliation and verification of single source of truth

<br>
</details>

<details>
<summary><b>Execute changes to infrastructure with Terraform (`terraform apply`)</b></summary>
<br>

* By default, `terraform apply` command automatically generates a new plan and prompts for you to approve it.
* `terraform apply` can also be passed an argument of an already saved `terraform plan`'s `-OUT` file when using Terraform in automation
	* `terraform show <FILE>`
	* **When doing so, Terraform takes the actions in the saved plan without any prompts for confirmation**

* `terraform apply` command executes the actions proposed in a Terraform plan.

<br>
</details>

<details>
<summary><b>Destroy Terraform managed infrastructure (`terraform destroy`)</b></summary>
<br>

* The `terraform destroy` command is a convenient way to destroy all remote objects managed by a particular Terraform configuration.
* Terraform is sometimes used to manage ephemeral infrastructure for development purposes and can be a use-case for `terraform destroy`
* `terraform destroy` AKA **`terraform apply -destroy`** and therefore the syntax options for both parameters are very similar
* Terraform can also create a speculative destroy plan to see what **the changes would entail for destorying a Terraform config** with `terraform plan -destroy`

<br>
</details>

------------------------------------------------------------------------

### 7) Implement and maintain state

<details>
<summary><b>Describe default local backend</b></summary>
<br>

* A backend defines where Terraform stores its `state` (`terraform.tfstate`) data files.
* Persistent data to keep track of Terraform managed resources
* This state data is persisted in a **backend**, or Terraform Cloud
* Locking the state while operations are being performed = Prevent overlap, duplication and conflicts
	* Only supported by certain backends
	* * Default backend = **local** (local disk file write in current working directory)

* Terraform writes the backend configuration in plain-text in two separate files:
	1) `.terraform/terraform.tfstate`
	2) `.terraform/terraform.tfstate` each time a `terraform plan` is created

* If updating a plan saved to an OUT file, TF uses the backend configuration stored in that `.terraform/terraform.tfstate` file, instead of the current backend settings

<br>
</details>

<details>
<summary><b>Outline state locking</b></summary>
<br>

* If supported by your backend, Terraform will lock your state for all operations that could write state. This prevents others from acquiring the lock and potentially corrupting your state.
	* If supported by your backend, Terraform will lock your state for all operations that could write state. This prevents others from acquiring the lock and potentially corrupting your state.
* `-lock` flag can turn off State Locking (not recommended)
* `-force-unlock` command to manually unlock the state if unlocking failed
	* **Use with risk and concern**
	* Utilizes a unique lock ID (nonce, AKA arbritrary number) for correlation and accuracy

<br>
</details>
<details>
<summary><b>Handle backend authentication methods</b></summary>
<br>

* Recommend using environment variables to supply credentials and other sensitive data. If you use -backend-config or hardcode these values directly in your configuration, Terraform will include these values in both the .terraform subdirectory and in plan files. This can leak sensitive credentials.

<br>
</details>

<details>
<summary><b>Describe remote state storage mechanisms and supported standard backends</b></summary>
<br>

* **Available Backends:**
	1) `local` - State Locking
	2) `remote` - State Locking
	3) `azurerm` - State Locking
	4) `consul` - State Locking - State Locking
	5) `cos` (Tencent Cloud Object Storage)
	6) `gcs` (Google Cloud Storage) - State Locking
	7) `http` (REST client using HTTP `GET`, `POST`, `DELETE` [**CRUD**] and supports state locking too)
	8) `kubernetes` (Kubernetes Secret) - State Locking
	9) `oss` (Alibaba Cloud OSS) - State Locking
	10) `pg` (Postres DB >=v10.0) - State Locking
	11) `s3` (Amazon AWS S3 Bucket) - State Locking

<br>
</details>

<details>
<summary><b>Describe effect of `Terraform refresh` on state (**DEPRECATED**)</b></summary>
<br>

* **DEPRECATED**, avoid usage.
* The `terraform refresh` command reads the current settings from all managed remote objects and updates the Terraform state to match.
* **Modifies the Terraform state**, not remote objects
* This same behavior is performed each time Terraform workflow performs `terraform plan` and `terraform apply` commands
	* The following command flags are available to emulate backwards compatibility of this command:

```
terraform plan -refresh-only
terraform apply -refresh-only
```

<br>
</details>

<details>
<summary><b>Describe backend block in configuration and best practices for partial configurations</b></summary>
<br>

* Backend configuration is added as a nested **top-level** `backend` block of the Terraform configuration (apart from TF Cloud)
	* Limited to one `backend` block
	* `backed` block cannot refer to named values such as variables
	* **Do not** hard-code credentials directly into the configuration as they will be included within the `.terraform` subdirectory and in plan files and leak the sensitive credentials

```
terraform {
  backend "remote" {
    organization = "example_corp"

    workspaces {
      name = "my-app-prod"
    }
  }
}
```

* **Reinitialization**.. After changing a backend configuration, you must run `terraform init` again to validate and configure the backend before you can perform any plans, applies or state operations
	* Before migrating to a new backend, we strongly recommend manually backing up your state by copying your `terraform.tfstate` file to another location.

## **Partial Configuration:**
* Some or all of the arguements in the backend configuration are ommitted
* Terraform requires at a minimum that an empty backend configuration is specified in one of the root Terraform configuration files, to specify the backend type.
* With partial configurations, the **remaining** arguments must be provided as part of the `initialization process` (`terraform init`):
	- **File:**
		- A configuration may be specific via the `terraform init` using the `-backend-config=PATH` option
		- `*.backendname.tfbackend` (e.g. `config.consul.tfbackend`) is the recommended naming pattern.
	- **Command-Line Key/Value Pairs:**
		- Key/Value pairs can be specified in via the `terraform init` command. 
		- Shells retain terminal history so **do not** use flags for secrets
		- `terraform init -backend-config="KEY=VALUE"`

```
$ terraform init \ 
	-backend-config="address=demo.consul.io" \ 
	-backend-config="path=example_app/terraform_state" \ 
	-backend-config="scheme=https"
```

	- **Interactively:**
		- Terraform will interactively via the terminal ask you for the required values unless input is disabled.

* Storing and working on Terraform with `local` backend is mainly used for development testing and platforms
* A `remote` or **Terraform Cloud**☁️  backend is recommended for collaboration on Terraform projects with teams to prevent overlap, corruption and also provide integrity of secrets and API tokens
* `terraform init` is required to reinitialize migration of state to `remote` backend
	* Followed by removal of **local** `terraform.tfstate`

<br>
</details>

<details>
<summary><b>Understand secret management in state files</b></summary>
<br>

* When using `local` Terraform `terraform.tfstate`, via a `local` backend, secrets and confidentials are embedded within the plain-text JSON configuration file
* API token to Terraform Cloud ☁️ is entered during interactive terminal and not saved in plain-text configuration files or terminal history
	* Always encrypts state at rest
		* **AWS S3** `s3` bacjend mechanism supports state at rest data encryption if `encrypt` option is enabled and can be secured further with IAM policies and logging for access auditing to the bucket.. and of course data in-transit to the S3 bucket is TLS-encrypted
	* TLS encryption of data to establish secure channel for data in-transmit
	* **Terraform Cloud**☁️  also maintains history of state changes and users requesting access for activity tracking and auditing
	* Whilst **Terraform Enterprise** offers detailed audit logging
* `remote` state is only ever held in memory (as of Terraform >=v0.9) and *can be* encrypted at rest depending on the backend mechanism selected

<br>
</details>

------------------------------------------------------------------------

### 8) Read, generate, and modify configuration

<details>
<summary><b>Demonstrate use of variables and outputs</b></summary>
<br>

## **Input Variables:** (AKA *Function Arguments*)
* Customize Terraform modules without altering module source code for cross-sharing of configurations making them portable and reusable
* Declare variables in `root` module of configuration by setting values using CLI options and environment variables
	* Assign values to Root Module Variables via:
		* Terraform Cloud Workspace ☁️
			* TF Cloud passes Workspace t Variables the same as `.tfvars` files
		* Individually with the `-var` command line option.
			* (`terraform apply -var="image_id=ami-abc123"`)
		* Variable definitions files (`.tfvars`), specified via command line or automatically loaded
		* Environment variables 
			* (`terraform apply -var-file="testing.tfvars"`)
			* Automatically loaded variable definition files:
				1) `terraform.tfvars` or `terraform.tfvars.json`
				2) `.auto.tfvars` or `.auto.tfvars.json`
* Declaring them in `child` modules is done within the `module` block of `.tf` nested-block for passing values
	* '$ export TF_VAR_image_id=ami-abc123`

>* The label after the `variable` keyword is a name for the variable, which must be unique among all variables in the same module
>* This name is used to assign a value to the variable from outside and to reference the variable's value from within the module.
>* You cannot use the following names for variables as they are reserved module configuration block `meta-arguments`:
>	* `source`, `version`, `providers`, `count`, `for_each`, `lifecycle`, `depends_on`, `locals`

**Input variables are _created_ by a `variable` block, but you _reference_ them as attributes on an object named `var`.**

```
variable "image_id" {
  type = string
}

variable "availability_zone_names" {
  type    = list(string)
  default = ["us-west-1a"]
}

variable "docker_ports" {
  type = list(object({
    internal = number
    external = number
    protocol = string
  }))
  default = [
    {
      internal = 8300
      external = 8300
      protocol = "tcp"
    }
  ]
}
```

```
resource "aws_instance" "example" {
  instance_type = "t2.micro"
  ami           = var.image_id
}
```

### Variable Definition Precedence

* If the same variable is assigned multiple values, Terraform uses the _last_ value it finds, overriding any previous values. 
* **Note that the same variable cannot be assigned multiple values within a single source.*

1) Environment variables
2) The `terraform.tfvars` file, if present.
3) The `terraform.tfvars.json` file, if present.
4) Any `*.auto.tfvars` or `*.auto.tfvars.json` files, processed in lexical order of their filenames.
5) Any `-var` and `-var-file` options on the command line, in the order they are provided. (This includes variables set by a Terraform Cloud workspace.)

### Arguments

Terraform CLI defines the following optional arguments for variable declarations:

-   [`default`](https://developer.hashicorp.com/terraform/language/values/variables#default-values) - A default value which then makes the variable optional and can also include a `default` argument which requires a literal value and cannot be another object reference.
-   [`type`](https://developer.hashicorp.com/terraform/language/values/variables#type-constraints) - This argument specifies what value types are accepted for the variable and allows you to restrict the type of value for a variable.
	- If no type is set, no value of any type is accepted (*explicit no match*)
	- `string`, `number`, `bool`m `any`
-   [`description`](https://developer.hashicorp.com/terraform/language/values/variables#input-variable-documentation) - This specifies the input variable's documentation.
-   [`validation`](https://developer.hashicorp.com/terraform/language/values/variables#custom-validation-rules) - A block to define validation rules, usually in addition to type constraints. and used within `custom validation rules` via `validation` block.. I.E checking an AMI ID has the correct syntax:

```
variable "image_id" {
  type        = string
  description = "The id of the machine image (AMI) to use for the server."

  validation {
    condition     = length(var.image_id) > 4 && substr(var.image_id, 0, 4) == "ami-"
    error_message = "The image_id value must be a valid AMI id, starting with \"ami-\"."
  }
}
```

-   [`sensitive`](https://developer.hashicorp.com/terraform/language/values/variables#suppressing-values-in-cli-output) - Limits Terraform UI output when the variable is used in configuration.
	- Setting a variable as `sensitive` prevents Terraform from showing its value in the `plan` or `apply` output, when you use that variable elsewhere in your configuration, **but will still record sensitive values in the state** and also when values are sent to provider to configure resources of course, without obfuscation and could be visible in `stderr`.
	- `sensitive = true` ensures `plan` and `apply` terminal output replaces values with `sensitive value`
-   [`nullable`](https://developer.hashicorp.com/terraform/language/values/variables#disallowing-null-input-values) - Specify if the variable can be `null` within the module **and overrides the default value**
	- `nullable = true` OR `nullable=true`

## **Output Variables:** (AKA *Function Return Values* or `outputs`)
* Output values make information about your infrastructure available on the command line, and can expose information for other Terraform configurations to use. I.E:
	1) A child module can use outputs to expose a subset of its resource attributes to a parent module
	* E.G, Child module named `web_server=instance_ip_addr`, you can access that value as `module.web_server.instance_ip_addr` via the Parent module 
	2) A root module can use outputs to print certain values in the CLI output after running `terraform apply`
	3) When using remote state, root module outputs can be accessed by other configurations via a **terraform remote state data source**
* Output values are similar to return values in programming languages.
* **Outputs are only rendered when Terraform applies your plan, ** not`terraform plan`**.

```
output "instance_ip_addr" {
  value = aws_instance.server.private_ip
}
# The expression refers to the private_ip attribute exposed by an aws_instance resource defined elsewhere in this module (not shown)
```

* In a root module, this name is displayed to the user; in a child module, it can be used to access the output's value.

* **Custom Condition Checks**
	* Capturing assumptions, documentation and ease of understanding as well as diagnosing issues

```
output "api_base_url" {
  value = "https://${aws_instance.example.private_dns}:8433/"

# This example precondition checks whether the EC2 instance has an encrypted volume {
    condition     = data.aws_ebs_volume.example.encrypted
    error_message = "The server's root volume is not encrypted."
  }
}
```

* **Output Arguments**
	* `description` = "Describe the purpose of each value"
	* `sensitive = true|false` = "Redact/mark values in `terraform plan|apply`"
	* `dependes_on = X` = "Parent module accesses an output value exported by one of its child modules, specify dependencies of that output value allows Terraform to correctly determine the dependencies defined in different modules. **Should be used as a LAST RESORT**"

```
output "instance_ip_addr" {
  value       = aws_instance.server.private_ip
  description = "The private IP address of the main server instance."

  depends_on = [
    # Security group rule must be created before this IP address could
    # actually be used, otherwise the services will be unreachable.
    aws_security_group_rule.local_access,
  ]
}
```

## **Local Values:** (AKA *Function's temporary local variables'* or `locals`)
* A local value assigns a name to an expression, so you can use the name multiple times within a module instead of repeating the expression.
* Since you can reference the local multiple times, you reduce duplication in your code
* Local values can be helpful to avoid repeating the same values or expressions multiple times in a configuration, but if overused they can also make a configuration hard to read by future maintainers by hiding the actual values used.
	* Local values behave like function definitions in traditional programming languages
	* Terraform's locals do not change values during our between Terraform runs `plan|apply|destroy`
	* Unlike variable values, local values can use dynamic expressions and resource arguments.
	* A local value can only be accessed in expressions within the module where it was declared.

```
locals {
  name_suffix = "${var.resource_tags["project"]}-${var.resource_tags["environment"]}"
  service_name = "forum"
  owner        = "Community Team"
  # Ids for multiple sets of EC2 instances, merged together
  instance_ids = concat(aws_instance.blue.*.id, aws_instance.green.*.id)
  # Common tags to be assigned to all resources
  common_tags = {
    Service = local.service_name
    Owner   = local.owner
}

resource "aws_instance" "example" {
  # ...

  tags = local.common_tags
}
```

[Customize Terraform Configuration with Variables](https://developer.hashicorp.com/terraform/tutorials/configuration-language/variables)  
  
[Output Data from Terraform](https://developer.hashicorp.com/terraform/tutorials/configuration-language/outputs)

<br>
</details>

<details>
<summary><b>Describe secure secret injection best practice</b></summary>
<br>

* Hashicorp Vault as a Terraform provider allows read from, write to and configure Vault
* Remember to use `sensitive=true` to redact secrets being persisted in Terraforms `terraform.tfstate` and `plan` files
* The vault provider requests a short-lived TTL token (`max_lease_ttl_seconds`) (20 mins = default) which means Vault will revoke any issued credentials after that time but is unable to retract any static secrets such as those stored in Vault's `generic` secret backend

* The Vault provider supports the following Vault authentication engines to authenticate to Vault:
	* `Userpass` = Username and Password `auth_login_userpass` (HTTP API)
	* `AWS` = AWS Authentication `auth_login_aws`
	* `TLS Certificate` = TLS Certificate authentication `auth_login_cert`
	* `GCP` = Google Cloud Auth Engine `auth_login_gcp`
	* `Kerberos` = Kerberos Auth Engine `auth_login_kerberos`
	* `Radius` = Radius Auth Engine `auth_login_radius`
	* `OCI` = Oracle Cloud Infrastructure Auth Engine `auth_login_oci`
	* `OIDC` = OIDC/JWT Auth Method (requires web browser on host machine) `auth_login_oidc`
	* `JWT` = JWT Auth Engine `auth_login_jwt`
	* `Azure` = Azure Auth Engine `auth_login_azure`
	* `Generic` = Path-based authenticate `auth_login`

<br>
</details>

<details>
<summary><b>Understand the use of collection and structural types	</b></summary>
<br>

* A _complex_ type is a type that groups multiple values into a single value:
	* `Collection Types` = Grouping similar values
		* Type of a value is called its element type
		* All elements of a collection must always be of the same type
		* `list(...)|list(any)` = Sequence of values identified by consecutive whole numbers >=0
		* `map(...)map(any)` = Collection of values where each is identified by a string label
			* Maps can be made with braces ({}) and colons (:) or equals signs (=): { "foo": "bar", "bar": "baz" } OR { foo = "bar", bar = "baz" }.
			* A newline between key/value pairs is sufficient in multi-line maps.
		* `set(...)` = Collection of unique values that do not have any secondary identifiers or ordering
	* `Structural Types` = Grouping dissimilar values
		* Structural types require a schema as an argument to specify which types are allowed for which elements
			* `object(...)` = Collection of named attributes that each have their own type
				* Schema = `{ <KEY> = <TYPE>, <KEY> = <TYPE>, ... }`
			* `tuple(...)` = A sequence of elements identified by consecutive whole numbers >=0, where each element has its own type
				* Schema = `[<TYPE>, <TYPE>, ...]`
<br>
</details>

<details>
<summary><b>Create and differentiate resource and data configuration</b></summary>
<br>

* **Resources:** (AKA **Managed Resources**)
	* Each Resource block within Terraform Language describes one or more infrastructure obects. Each Resource is associated with a single resource type which determines the kind of infrastructure object it manages and arguments the resource supports.
		* A resource block declares that you want a particular infrastructure object to exist with the given settings.
		* The infrastructure object represented by the `resource` block is stored within `terraform.tfstate` which allows it to be updated and destroyed in response to future changes.
		* For `resource` blocks which already exist within the `terraform.tfstate`, Terraform compares the actual configuration of the object with the arguments given in the configuration to identify if an update is needed.
		* `terraform apply`'ing a configuration will:
			* `create` resources that exist in the configuration but not associated with real world infrastructure state
			* `destroy` resources that exist in the state but no longer in the configuration
			* `update` in-place resources who's arguments have changed
			* `destroy` & `update` resources who's arguments have changed but which cannot be updated in-place due to remote API (`provider`) limitations
	* Each Resource Type is implemented by a provider (Terraform plugin for IaC configuration and management)
		* A Terraform module must specify which providers it requires to manager resources as well as authenticate to that provider
		* Terraform can sometimes automatically determine which provider to use based on a resource type's name
	* `resource {` blocks document the syntax to declare resources
		* The resource type and name together serve as an identifier for a given resource and so must be unique within a module.
		* **Resource names must start with a letter or underscore, and may contain only letters, digits, underscores, and dashes.**
	* **Resource Behavior** defines how Terraform handles resource declarations when applying a configuration
		* You can use `precondition` and `postcondition` blocks to specify assumptions and guarantees about how the resource operates.
	* Meta-arguments can be used with every resource type
		* `depends_on` = Specifying hidden dependencies
		* `count` = Creating multiple resource instances
		* `for_each` = Create multiple instances according to a `map` or set of strings
		* `provider` = Selecting a non-default provider configuration
		* `lifecycle` = Lifecycle customizations
		* `provisioner` = Taking extra actions after resource creation
	* Provisioners documents configuring post-creation actions for a resource using `provisioner` and `connection` blocks
		* Provisioners are non-declarative and thus potentially unpredictable **LAST RESORT**

```
resource "aws_instance" "web" {
  ami           = "ami-a1b2c3d4"
  instance_type = "t2.micro"
}
```

* **Accessing Resource Attributes**:
	* Resource Attributes are achieved through Terraform modules accessing `expressions` which is information about resources to help configure other resources.
	* Use the `<RESOURCE TYPE>.<NAME>.<ATTRIBUTE>` syntax to reference a resource attribute in an expression.Use the `<RESOURCE TYPE>.<NAME>.<ATTRIBUTE>` syntax to reference a resource attribute in an expression.
		* Simple example is an instance ID number

* **Resource Dependencies**:
	* Most resources in a configuration don't have any particular relationship, and Terraform can make changes to several unrelated resources in parallel.
	* Most resource `dependencies` are handled automatically.
	* Terraform analyses any `expressions` within a `resource` block to find references to other objects, and treats those references as implicit ordering requirements when creating, updating, or destroying resources.
	* For dependencies that cannot be recognized implicitly in configuration, you can use `depends_on` meta argument to explicitly define a dependency as well as `replace_triggered_by` to add dependencies between otherwise independent resources which forces Terraform to replace the parent resource when there is a change to a referenced resource or resource attribute

* **Local-Only Resources**:
	* Sometimes referred to as glue to help connect together other resources, examples:
		* Generating private keys
		* Issuing self-signed TLS certificates
		* Generating random ID's
	* Obviously Local Resources only exist in `terraform.tfstate` and not real-world infrastructure

### * **Managed Resources vs Data Resources**:
	* `Managed Resources` cause Terraform to create, update, and delete infrastructure objects
	* `Data Resources` cause Terraform only to _read_ objects.

* **Data Sources:**:
	* Data Source is accessed via a special kind of resource known as a data resource, must be unique and locally inta-Module relevant. The Data Source are also `provider`-relevant.
	* Data Source `meta-arguments` are not provider-relevant and generic across all data sources.
	* Each data resource is associated with a single data source, which determines the kind of object (or objects) it reads and what query constraint arguments are available.
	* Declated using a `data "aws_ami" "example" {` block, prompts Terraform to read from a given data source ("aws_ami") and export the result under the local given name ("example")
	* The name is used to refer to this resource from elsewhere in the same Terraform module, but has no significance outside of the scope of a module.

* Terraform reads data resources during the `plan`ning phase when possible and defers reading resources until the apply phase to preserve the order of operations
* Same as Managed Resources, Terraform also supports `Local-only Data Sources` which are Terraform-local, only exist temporarily during a Terraform operation and are re-calculated each time a new plan is created:
	* Rendering Templates
	* Reading Local Files
	* Rendering AWS IAM Policies

<br>
</details>

<details>
<summary><b>Use resource addressing and resource parameters to connect resources together</b></summary>
<br>

* A **resource address** (`[module path][resource spec]`) is a string that identifies zero or more resource instances in your overall configuration.

* A **Resource Spec** addresses a specific resource instance in the selected module = `resource_type.resource_name[instance index]`:
	* `resource type` - Type of resource being addressed
	* `resource name` - User-defined name of the resource
	* `[instance index]` - (**Optional**) index to select an instance from a resource that has multiple instances

* A **Module Path** (`module.module_name[module index]`) addresses a module within the tree of modules 
	* (A Terraform module is a set of Terraform configuration files in a single directory.)
	* `module` - indicating a child module (non-root). Multiple module keywords in a path indicate nesting
	* `module.name` - User-defined name of the module
	* `[module index]` - (**Optional**) index to select an instance from a module call that has multiple instances
* To address all resources of a **particular** module instance, include the module index in the address, such as `module.foo[0]`
* An example of the module keyword delineating between two modules that have multiple instances:
	* `module.foo[0].module.bar["a"]`

* **Index values for Modules and Resources:**
	* `N` = Where `N` is a `0`-based numerical index into a resource with multiple instances specified by the `count` meta-argument
		* Omitting an index when addressing a resource where `count > 1` means that the address references all instances
	* `["INDEX]"` = Where `INDEX` is a alphanumerical key index into a resource with multiple instances specified by the `for_each` meta-argument
	* Example Terraform configuration:

```
resource "aws_instance" "web" {
  # ...
  count = 4
}
```

* Equals:
	* Address = `aws_instance.web[3]`
	* Last instance in the config = `aws_instance.web`

```
resource "aws_instance" "web" {
  # ...
  count = 4
}
```

* Equals:
	* Address (**example** instance only) = `aws_instance.web["example"]`

* **References to Named Values:**
	* 

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

### 9) Understand Terraform Cloud ☁️ and Enterprise capabilities

<details>
<summary><b>Describe the benefits of Sentinel, registry, and workspaces</b></summary>
<br>

```
code
```

<br>
</details>

<details>
<summary><b>Differentiate OSS and Terraform Cloud ☁️ workspaces</b></summary>
<br>

```
code
```

<br>
</details>
<details>
<summary><b>Summarize features of Terraform Cloud ☁️</b></summary>
<br>

```
code
```

</details>

📚 `EO-Exam Topics`

------------------------------------------------------------------------------------------------------------------------------------------------------

📖 `Terraform: Up and Running, 3rd Edition` Book Notes

[Terraform: Up and Running, 3rd Edition](https://www.oreilly.com/library/view/Terraform-up-and/9781098116736/)
	
- The goal of DevOps is to make software delivery vastly more efficient.
- There are four core values in the DevOps movement: (CAMS)
- The idea of IaC is that you write and execute code to define, deploy, update, and destroy your infrastructure.    

1) Culture
2) Automation
3) Measurement
4) Sharing

- The idea of immutable infrastructure is to destroy and re-deploy fresh updated version from a Server Template, rather than continuously update which is a main preventative measure to avoid configuration drift and allows for more flexibility when utilizing reusable code.

There are five broad categories of IaC tools: 

1) Ad hoc scripts (I.E build script, most commonly designed to run on a single local system)
- Code that works correctly no matter how many times you run it is called idempotent code.
2) Configuration management tools (Chef, Puppet, and Ansible (which is idemoptent by default and also lean towards a mutable infrastructure paradigm but can be forced to be immutable))
- Ansible being idempotent meaning an Ansible role will install Apache only if it isn’t installed already and will try to start the Apache web server only if it isn’t running already. 
3) Server templating tools (Docker, Packer and Vagrant) (Create the image of a server as a snapshot in time, including OS, applications, libraries, versions etc.)
- A virtual machine (VM) emulates an entire computer system, including the hardware and runs on a hypervisor
- A container emulates the user space of an operating system where the kernel and hardware are shared
4) Orchestration tools (Kubernetes, Marathon/Mesos, Amazon Elastic Container Service, Docker Swarm, Nomad)
5) Provisioning tools (Terraform, CloudFormation, OpenStack, Heat, Pulimi) are responsible for deploying IaC that supports the above and most commonly always takes an immutable paradigm approach

It is common to use a combination of Terraform and Ansible for example, `Terrible'.. wappp wahhhhppp

- Terraform is written in `HCL2` (HashiCorp Language) and config is JSON-format, stored as `.tf.` file extensions.
  - HCL2 was adopted in Terraform version >=0.12
- Terraform is open-source and written in Go programming language
- Terraform is a DSL (Domain-Specific Language), compared to GPL (General Purpose Language - I.E supports multiple programming language approaches) whereas the code may be more simplistic, but functionality is limited to that specific tool
- Terraform works by the terraform binary makes API calls on your behalf to one or more providers, such as AWS, Azure, Google Cloud, DigitalOcean, OpenStack, and more (AKA Providers)
- Terraform does not use a Master server for running IaC (compared to Chef for example) and is **Masterless** and only requires a client with Terraform installed and is also **Agentless** in theory

[Terraform Up and Running Book "Code Examples"](https://github.com/brikis98/terraform-up-and-running-code)
`git clone https://github.com/brikis98/terraform-up-and-running-code.git`

## Declarative vs Imperative:
* Imperative programming is like giving a chef step-by-step instructions on how to make a pizza. (Chef and Ansible encourage a procedural style in which you write code that specifies, step by step, how to achieve some desired end state)
- Vanilla JavaScript is an example of Imperative programming
* Declarative programming is like ordering a pizza without being concerned about the steps it takes to make the pizza. 
- React is a popular example of Declarative programming.

Imperative code focuses on writing an explicit sequence of commands to describe how you want the computer to do things using procedural scripts, and declarative code focuses on specifying the result of what you want; and by instructing Terraform to work out the end desired state of infrastructure based on what is currently spinning.
- **Procedural code does not fully capture the state of the infrastructure**

-  Compared to GitOps Configuration Management tools such as Jenkins or Ansible, Terraform uses a `Declarative Language` perspective
- I.E, when deploying a Terraform `main.tf`, Teraform Declarative nature will state what the end-goal/desired objective or state of the infrastructure is
  - I.E, if 15 EC2 instances are deployed on initial Terraform `Apply`, and later the configuration is updated to reflected 20 EC2 instances; Terraform is able to read the state and operations of the infrastructure and calculate that 5 additional instances are required which would reflect in the Terraform `Plan` and also does not remove the initial 15.
- However, an Imperative Language model such as Ansible would end up configuring a total of 35 instances (1 push of 15 EC2 instances and 1 push of 20 EC2 instances)

* Every time you run Terraform, it records information about what infrastructure it created in a Terraform state file (`terraform.tfstate` custom JSON-format file is stored in the current working root directory where Terraform actions are performed) and reflects a log output of Terraform history, the Terraform state is details about the deployment and changes made during its lifecycle. This may also contain plain-text secrets and sensitive output and therefore is recommended to be added to a `.gitignore` file when managing Terraform deployments via version control, such as Git.
* The output of the Terraform `plan` command is a diff between the code on your computer and the infrastructure deployed in the real world, as discovered via IDs in the state file.
* `terraform.tftstate` is a private API and should not be directly edited.
* Locking Terraform state is a requirement for shared `terraform.tfstate` to allow for collaborating teams to keep up-to-date code and eliminate configuration drift and is simply a configuration lock on the Terraform operation. In this case, Terraform State files should also always be shared.
* When re-configuring a Terraform state, I.E local to remote, Terraform will acknowledge, recognize and copy over the `terraform.tfstate` when you perform a `terraform init` to load the config and provider (supporting backend remote resource)
* Terraform workspaces allow you to store your Terraform state in multiple, separate, named workspaces and are **completely isolated**, but not recommended for production as state files are saved on the same remote location with the same authentication & authorization permissions. 
* Terraform starts with a single workspace called “default,” and if you never explicitly specify a workspace, the default workspace is the one you’ll use the entire time.
  * Workspaces are not visible within the configuration files or unless explicitly stated using Workspace commands in the terminal

Terraform outputs dependencies output in graph description language (GDL), called DOT. Recommended to better visualize this with tools such as [GraphvizOnline](https://graphviz.org/). An example dependency for an EC2 instance is a VPC, Security Group, Region etc.

- Terraform Config Tags/Variables:

`prevent_destroy`

* Terraform will error if a remote resource following provision with this tag, is attempted to be destroyed and prevents accidental destruction of remote infrastructure - I.E AWS S3 bucket storing the Terraform state

`aws_s3_bucket_versioning`

* S3 bucket will store versions of Terraform state to provide individual copies/files of the Terraform state over its lifecycle, aswell as a fallback reversion plan

`terraform_remote_state`

* You can use this data source to fetch the Terraform state file stored by another set of Terraform configurations.

- Example Terraform **Environment Variables** locally to hide sensitive credentials

`$ export TF_VAR_db_username="(YOUR_DB_USERNAME)"`

* Experiment with **Terraform Syntax, ARGS and Functions** by entering the **Terraform Console**: (**READ ONLY**)

`$ terraform console .... <---- I am read-only`

* Terraform `File Layout` provides a true, recommended method of isolation
  - Place configuration files in seperate file directories
  - Configure unique remote backend methods for control of RBAC

* When you run Terraform, it simply looks for files in the current directory with the .tf extension
  - Splitting components into separate folders prevents you from accidentally blowing up your entire infrastructure in one command, but it also prevents you from creating your entire infrastructure in one command.
  - I.E, when working multiple folders; a `terraform plan`/ `terraform apply` is required in each folder
  - A workaround for this is to use [**Terragrunt**](https://terragrunt.gruntwork.io/) using the `run-all-command` for **clean**, `DRY` code.

### Terraform Modules

* `**Terraform Modules** allow for clean, portable and easy re-use of IaC Terraform code:
  * Think of Terraform `Modules` as reusable functions with generic programming languages for the purpose of reusing blocks of code and put that code anywhere
  * Terraform Modules, similarly allow you to specify blocks of code within a Module and re-use that across multiple environments without having to actually copy and paste the IaC code
  * Modules are the key ingredient to writing reusable, maintainable, and testable Terraform code as well as sharing this with other teams (`reusable modules`)
    >* Terraform modules are simply any set of Terraform configuration files in a folder that removes erroneous configuration drift, duplication and provides ease for management
    >* Running `terraform apply` on a module refers to it as the `root` module
    >* **Providers should only be configured in Terraform root modules, not reusable modules**
    >* **Adding Terraform Providers requires another** `terraform init`, prior to `terraform plan / terraform apply` each time to take effect
    >1) `NAME` is a logical name for reference within configuration
    >2) `SOURCE` is the file dir/path to the module (Terraform supports other types of module sources, such as Git URLs, Mercurial URLs, and arbitrary HTTP URLs.)
    >3) `CONFIG` is the arguments/configuration items, specific to the module
  * Terraform Module `templatefile` function is that the filepath you use must be **relative**, and not **absolute**.. or use a **path reference** (`path.<TYPE> (module/root/cwd)`) (By default, Terraform interprets the path relative to the current working directory.)
  * When creating a module, you should always prefer using separate resources, (can be defined anywhere) as using both separate resources combined with inline blocks (can only be added within a module which creates that resource) will present erroneous configuration errors and configuration overlap

```
provider "aws" {
  region = "us-east-2"
}

module "webserver_cluster" {
  source = "../../../modules/services/webserver-cluster"
# VARIABLE EXAMPLES BELOW ..
  cluster_name           = "webservers-stage"
  db_remote_state_bucket = "(YOUR_BUCKET_NAME)"
  db_remote_state_key    = "stage/data-stores/mysql/terraform.tfstate"
}
```

* In a GPL, to make a function configurable, you can add input parameter(s) to that function
* This has the same use-case for Terraform modules which avoid hard-coded naming convention errors when using different cloud provider accounts or environments, by adding a `variables.tf` file within the Terraform Module folder
* Within the `main.tf` Terraform module configuration, the variable is referenced using name = "${var.x_y}-z"

```
variable "cluster_name" {
  description = "The name to use for all the cluster resources"
  type        = string
}
```

* Terraform `LOCAL Modules` values allow you to assign a name to any Terraform expression and to use that name throughout the module. 
* The names (`local.<NAME>) are only visible within the module and cannot be overriden from outside the module

locals {
  http_port    = 80
}

* `MODULE OUTPUTS` == `module.<MODULE_NAME>.<OUTPUT_NAME>` to access module output variables

### Terraform Module Versions

* The easiest way to create a versioned module is to put the code for the module in a separate Git repository and to set the source parameter to that repository’s URL (Terraform `source` parameter)
* Recommended to use Git Tag's for logical references for naming convention of Terraform Module versions
  * The ref parameter allows you to specify a particular Git commit via its sha1 hash, a branch name, or, as in this example, a specific Git tag.
  * **Use Semantic Versioning** (`MAJOR.MINOR.PATCH`) for GitOps tags

```
module "webserver_cluster" {
  source = "github.com/foo/modules//services/webserver-cluster?ref=v0.0.1"
cluster_name           = "webservers-stage"
  db_remote_state_bucket = "(YOUR_BUCKET_NAME)"
  db_remote_state_key    = "stage/data-stores/mysql/terraform.tfstate"

  instance_type = "t2.micro"
  min_size      = 2
  max_size      = 2
}
```

* PROGRESS - Page 246
	
....

📖 EO `Terraform: Up and Running, 3rd Edition` Book Notes
	
------------------------------------------------------------------------------------------------------------------------------------------------------

🧪 `lab`

# [Apply Terraform Configuration](https://developer.hashicorp.com/terraform/tutorials/cli/apply?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS)



🧪 `EO-lab`

------------------------------------------------------------------------------------------------------------------------------------------------------

🧪 `lab`

# [Perform CRUD Operations with Providers](https://developer.hashicorp.com/terraform/tutorials/providers/provider-use) AKA `HashiCups`
## [terraform-provider-hashicups](https://github.com/hashicorp/terraform-provider-hashicups)




🧪 `EO-lab`

------------------------------------------------------------------------------------------------------------------------------------------------------

🧪 `lab`

# [Store Remote State](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-remote)
* Migrate Terraform `local` backend configuration to remote Terraform Cloud ☁️ workspace




🧪 `EO-lab`

------------------------------------------------------------------------------------------------------------------------------------------------------

🧪 `lab`

# [An Ultimate Terraform Hands-on Labs](https://collabnix.github.io/terraform/)
## [View on GitHub](https://github.com/collabnix/terraform)




🧪 `EO-lab`

------------------------------------------------------------------------------------------------------------------------------------------------------

💾 `EOF`
