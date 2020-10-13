## Quick course notes - *Terraform Learnings*

What is Terraform. 

- Infrastruture management tool made by HashiCorp.
- Provision, manage and maintain cloud resources like servers, networks, storage etc. 
- All in one centralized set of code. 

Terraform is for managing the base infrastruture itself, and is not reponsible for what's inside it. 

- There is no need to worry about Terraform making a change you dont expect, when it compiles the plan Terraform will attempt and have a change to decide of what exactly you want to happen. 

- Terraform takes in the infrastruture as code and compares it to the state fo what actually exists, and then essentially writing a step by step script that will make the changes. 

- Data struture used in Terraform : Graph. **Directed Acyclic Graph**.

<img width="800" alt="Screen Shot 2020-10-13 at 3 01 07 PM" src="https://user-images.githubusercontent.com/51350594/95910053-1760a600-0d65-11eb-869d-59c6ad27e132.png">

### Terraform Plan

- The **Plan** step is critical because it figures out what needs to be done and in what order. Then, Terraform uses the provider to actually apply the plan and make the changes the needed. 

<img width="800" alt="Screen Shot 2020-10-13 at 2 57 53 PM" src="https://user-images.githubusercontent.com/51350594/95910055-17f93c80-0d65-11eb-96f5-8a556f7ee5ce.png">

> $ terraform plan
![Screen Shot 2020-10-13 at 3 05 07 PM](https://user-images.githubusercontent.com/51350594/95910343-8a6a1c80-0d65-11eb-85d6-47461e33d829.png)

- Plan to destroy: Shows the plan on what all resources will be destroyed
>> $ terraform plan -destroy

- **-out** parameter: Outputs the plan in a new file *.plan* that contains the plan which will be applied. The output file is in **binary format**

### Terraform State

- Refers to the state of the resources in Terraform. 
- Important to realise that is used to refer two different things: 1) Reality of your infrastructure, what is the state of the resources in AWS, IP address, instance types, bucket names, etc 2) The local representation the Terraform keeps, i.e the state file. 
- The local **.tfstate** file simple tells that this is everything Terraform knows and thinks is the current state up in the AWS.  
- Invertigate the terraform state using the command:
> $ terraform state
    
This gives us the option to list resources in the state, to move an item, to push/pull, remove etc. 

### Terraform Graph
- Terraform builds a graph as a part of its plan.
- It helps to get insight what actually is going to happen by exporting that graph and rendering it visually. 
> $ terraform graph
- The output is actually a DOT file. We get to visualize the DOT file into a graph by using Graphviz website. http://www.webgraphviz.com/
- Won't add it in here, but the results are amazing. 

### Terraform Apply
- Now that the resources are created, we will destroy the plan but first outputting the plan in to plan file. 
$ terraform plan -destroy -out=first_plan.plan
- Now we apply the plan to destroy.
> $ terraform apply 

---------------------------------------------
## Resources
- Building blocks of terraform code
- Define the 'what' of your instrastructure, and Terraform figures out the 'how'
- Different settings for providers

A Typical Terraform file consists of:

- Provider Definition: 

    -  Provider isnt a resource, its what gives you access to the resource

        provider "aws" {
        profile = "default"
        region  = "us-east-1"
        }

-  Resources:
    - Format: 
    > *resource* *resource_type*(defined by provider) *resource_name*(Terraform will use to recognize)

        resource "aws_s3_bucket" "tf_course" {
        bucket = "first-tf-bucket"
        acl    = "private"
        } 

### Resource Types:
- AWS security group can function as firewall. 
- Here, the **ingress** block specifies what port and protocols to allow in, and the cidr block that defines the what range of IP addresses to allow in.
- Whereas, the **egress** block defines what outbound traffic is allowed. (here, it is any port and any protocol)

        resource "aws_security_group" "allow_tls" {
            ingress {
                from_port  = 443
                to_port    = 443
                protocol   = "tcp"
                cidr_block = "1.2.3.4/32"
            }
            egress {
                from_port  = 0
                to_port    = 0
                protocol   = "-1"
            }
        }