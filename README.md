# Fake Shop


## Variável de Ambiente
DB_HOST	=> Host do banco de dados PostgreSQL.

DB_USER => Nome do usuário do banco de dados PostgreSQL.

DB_PASSWORD	=> Senha do usuário do banco de dados PostgreSQL.

DB_NAME	=>	Nome do banco de dados PostgreSQL.

DB_PORT	=>	Porta de conexão com o banco de dados PostgreSQL.


AWS COnfig
----------

### IAM
Create User group
 - name: aula-admins
 - policies: AdministratorAccess

Create User
 - name: aula
 - user groups: aula-admins
  
User aula config
 - set default region to us-east-1

Create Role
 - Trustedentity type: AWS service
 - Use case: EKS - Cluster
 - Permissions policies: AmazonEKSClusterPolicy
 - Name: eks-cluster
 
Create Role
 - Trustedentity type: AWS service
 - Use case: EC2
 - Permissions policies: AmazonEKSWorkerNodePolicy, AmazonEKS_CNI_Policy, AmazonEC2ContainerRegistryReadOnly
 - Name: eks-worker
 
### Cloud Formation
 Create stack
 - Choose an existing template
 - Template source: Prepare template: Amazon S3 URL
 - AmazonS3 URL: https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/amazon-eks-vpc-private-subnets.yaml
 - Name: aula-eks
 
### EKS
 Create EKS Cluster
 - Custom configuration, auto mode off
 - Name: aula-eks	
 - IAM role: eks-cluster
 - VPC: aula-eks-VPC
 - subnets: all
 - Control plane log: used on production

#### Cluster aula-eks
  Compute
   Add Node Groups
   - Name: default
   - IAM Role: eks-worker
   - instance types: t3.medium
   - Node group scaling configuration: desired 2, minimun 2, maximum 6
   - Node group network configuration: subnets private ones

On terminal
-----------

`aws eks update-kubeconfig --name aula-eks`
`kubectl get nodes`
`kubectl apply -f k8s/deployment.yaml`
`kubectl get pod`

`kubectl get service`
- access fakeshop EXTERNAL-IP