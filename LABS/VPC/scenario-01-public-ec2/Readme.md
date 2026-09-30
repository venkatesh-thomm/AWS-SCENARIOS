

#### VPC Scenario 1 — Public Subnet → EC2 → Internet

## Objective

Create a public subnet and launch an EC2 instance that can communicate with the internet.

## Architecture

```text
                         Internet
                            │
                            │
                    ┌───────▼────────┐
                    │ Internet       │
                    │ Gateway (IGW)   │
                    └───────┬────────┘
                            │
                     0.0.0.0/0
                            │
                    ┌───────▼────────┐
                    │ Public Route   │
                    │ Table          │
                    └───────┬────────┘
                            │
                    ┌───────▼────────┐
                    │ Public Subnet  │
                    │ 10.0.1.0/24    │
                    └───────┬────────┘
                            │
                    ┌───────▼────────┐
                    │ EC2 Instance   │
                    │ Public IP      │
                    └────────────────┘
```

## Components

* VPC
* Internet Gateway
* Public Subnet
* Route Table
* Route Table Association
* Security Group
* EC2 Instance
* Nginx

## Key Configuration

The public route table contains:

```text
0.0.0.0/0 → Internet Gateway
```

The EC2 instance has a public IPv4 address and the Security Group allows HTTP traffic on port `80`.

## Deployment

```bash
terraform init
terraform validate
terraform plan
terraform apply
```

## Test

```bash
curl http://$(terraform output -raw public_ip)
```

Expected:

```text
VPC Scenario 1 - Public EC2 Internet Test
```

## Troubleshooting Test

The `0.0.0.0/0 → Internet Gateway` route was intentionally removed.

Result:

```text
curl: (28) Failed to connect
```

The route was restored using Terraform:

```bash
terraform apply
```

Connectivity was then verified successfully.

Is Nginx running?

SSH into EC2: 
>sudo systemctl status nginx
 
 
Is port 80 listening?
> sudo ss -lntp | grep :80

This gives you a systematic troubleshooting approach instead of randomly checking resources.

## Key Learning

A subnet becomes public when its route table has a route to an Internet Gateway.

For an EC2 instance to be internet accessible:

* Public IP is required.
* Route table must have `0.0.0.0/0 → IGW`.
* Security Group must allow the required traffic.

## Cleanup

```bash
terraform destroy
```

This is enough for the project README. We can keep the **detailed troubleshooting/interview explanations outside the README**.
