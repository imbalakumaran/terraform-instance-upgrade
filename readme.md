# 🚀 How You Upgrade the Same Instance

### Step 1 – Take Backup (AMI)

```bash
aws ec2 create-image \
  --instance-id i-xxxxxxxx \
  --name "backup-before-upgrade"

aws ec2 describe-images --image-ids ami-08c91547283009813 \
  --query 'Images[*].State' --output text
```

---

### Step 2 – Change Only This Line

```hcl
instance_type = "t3.large"
```

---

### Step 3 – Run Terraform

```bash
terraform init
terraform import aws_instance.server i-03b0561544a15a4bb
terraform plan
terraform apply

aws ec2 describe-instances \
  --instance-ids i-03b0561544a15a4bb \
  --query 'Reservations[*].Instances[*].[InstanceType,State.Name]' \
  --output table

sed -i.bak 's/t3\.medium/t3.xlarge/g' terraform.tfvars
```

You should see:

```
~ instance_type = "t3.medium" -> "t3.large"
```

```
terraform state show aws_instance.server
```


# ✅ Final Local Structure (Minimal & Clean)

```bash
terraform-ec2/
│
├── provider.tf
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── terraform.tfstate
└── terraform.tfstate.backup
```

---

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:RunInstances",
                "ec2:StartInstances",
                "ec2:StopInstances",
                "ec2:RebootInstances",
                "ec2:Describe*",
                "ec2:CreateTags",
                "ec2:ModifyInstanceAttribute",
                "ec2:MonitorInstances",
                "ec2:UnmonitorInstances",
                "ec2:CreateKeyPair",
                "ec2:ImportKeyPair",
                "ec2:DescribeKeyPairs",
                "ec2:CreateSecurityGroup",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:RevokeSecurityGroupIngress",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:DescribeSecurityGroups",
                "ec2:AllocateAddress",
                "ec2:AssociateAddress",
                "ec2:DescribeAddresses",
                "ec2:DescribeVpcs",
                "ec2:DescribeVpcAttribute",
                "ec2:DescribeSubnets",
                "ec2:DescribeInternetGateways",
                "ec2:DescribeRouteTables",
                "ec2:DescribeNetworkInterfaces",
                "ec2:CreateNetworkInterface",
                "ec2:CreateImage",
                "ec2:AttachNetworkInterface"
            ],
            "Resource": "*"
        }
    ]
}
```