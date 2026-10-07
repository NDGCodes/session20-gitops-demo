# AWS VPC and EKS

Based on the supplied Terraform modules. Fixed multiline HCL syntax and changed EKS 1.31 to 1.34, which is in standard support as of the lab date. [AWS version lifecycle](https://docs.aws.amazon.com/eks/latest/userguide/kubernetes-versions.html).

**AWS execution is pending your run.** This creates an EKS cluster, two `t3.medium` workers, a NAT gateway, and public/private subnets. These incur charges; destroy the lab after evidence collection.

## CloudShell

1. AWS console → Mumbai → CloudShell.
2. Upload `session20-terraform-aws.zip` through Actions → Upload file.
3. Run:

```bash
cd ~
unzip session20-terraform-aws.zip
cd taskboard-terraform
export PATH="/tmp/session18-terraform-bin:$HOME/bin:$PATH"
export TF_DATA_DIR=/tmp/taskboard-terraform-data
mkdir -p "$TF_DATA_DIR"
terraform version
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
```

Run separately. Enter `yes` only after reviewing the plan.

If Terraform is missing:

```bash
curl -fL https://releases.hashicorp.com/terraform/1.14.0/terraform_1.14.0_linux_amd64.zip -o /tmp/taskboard-terraform.zip
mkdir -p /tmp/taskboard-terraform-bin
unzip -o /tmp/taskboard-terraform.zip -d /tmp/taskboard-terraform-bin
export PATH="/tmp/taskboard-terraform-bin:$PATH"
```

## Verify and screenshots

```bash
terraform output
terraform state list
aws eks update-kubeconfig --region ap-south-1 --name taskboard-eks
kubectl get nodes
```

Capture init/validate, plan, successful apply, outputs/state, EKS cluster and worker nodes, VPC/subnets/NAT, and Kubernetes nodes.

Application deployment also requires an EBS CSI driver with IAM permissions, metrics-server for HPA, and an Ingress controller. These add-ons are not installed by the supplied Terraform. Do not apply the app chart to EKS until they are configured; the local Helm deployment is already being verified separately.

## Destroy

After removing any Kubernetes-created load balancers and storage, run from this directory:

```bash
terraform plan -destroy
terraform destroy
```

Enter `yes`; capture the final successful destroy output. Preserve state and use the same AWS identity and TF_DATA_DIR throughout.
