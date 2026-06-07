# AWS_EKS
Kubernetes


# EKS Deployment

pre-requisites

# Powershell
```
choco install eksctl
aws configure
```

> <img width="725" height="331" alt="image" src="https://github.com/user-attachments/assets/67a18282-6fd8-45a7-b57c-8d1c658210a6" />

Create Cluster:
```bash
eksctl create cluster --name sample-cluster-3 --region ap-south-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3
```
> <img width="729" height="354" alt="image" src="https://github.com/user-attachments/assets/9ba76965-ac8c-4bde-8234-e53ff657c2bd" />
```
PS C:\Users\abhis\Harika\K8s\Class> eksctl create cluster --name sample-cluster-3 --region ap-south-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3
2026-06-07 11:18:16 [ℹ]  eksctl version 0.226.0
2026-06-07 11:18:16 [ℹ]  using region ap-south-1
2026-06-07 11:18:16 [ℹ]  setting availability zones to [ap-south-1a ap-south-1b ap-south-1c]
2026-06-07 11:18:16 [ℹ]  subnets for ap-south-1a - public:192.168.0.0/19 private:192.168.96.0/19
2026-06-07 11:18:16 [ℹ]  subnets for ap-south-1b - public:192.168.32.0/19 private:192.168.128.0/19
2026-06-07 11:18:16 [ℹ]  subnets for ap-south-1c - public:192.168.64.0/19 private:192.168.160.0/19
2026-06-07 11:18:16 [ℹ]  nodegroup "standard-workers" will use "" [AmazonLinux2023/1.34]
2026-06-07 11:18:16 [!]  Auto Mode will be enabled by default in an upcoming release of eksctl. This means managed node groups and managed networking add-ons will no longer be created by default. To maintain current behavior, explicitly set 'autoModeConfig.enabled: false' in your cluster configuration. Learn more: https://eksctl.io/usage/auto-mode/
2026-06-07 11:18:16 [ℹ]  using Kubernetes version 1.34
2026-06-07 11:18:16 [ℹ]  creating EKS cluster "sample-cluster-3" in "ap-south-1" region with managed nodes
2026-06-07 11:18:16 [ℹ]  will create 2 separate CloudFormation stacks for cluster itself and the initial managed nodegroup
2026-06-07 11:18:16 [ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=ap-south-1 --cluster=sample-cluster-3'
2026-06-07 11:18:16 [ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "sample-cluster-3" in "ap-south-1"
2026-06-07 11:18:16 [ℹ]  CloudWatch logging will not be enabled for cluster "sample-cluster-3" in "ap-south-1"
2026-06-07 11:18:16 [ℹ]  you can enable it with 'eksctl utils update-cluster-logging --enable-types={SPECIFY-YOUR-LOG-TYPES-HERE (e.g. all)} --region=ap-south-1 --cluster=sample-cluster-3'
2026-06-07 11:18:16 [ℹ]  default addons metrics-server, vpc-cni, kube-proxy, coredns were not specified, will install them as EKS addons
2026-06-07 11:18:16 [ℹ]  
2 sequential tasks: { create cluster control plane "sample-cluster-3", 
    2 sequential sub-tasks: { 
        2 sequential sub-tasks: { 
            1 task: { create addons },
            wait for control plane to become ready,
        },
        create managed nodegroup "standard-workers",
    } 
}
2026-06-07 11:18:16 [ℹ]  building cluster stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:18:16 [ℹ]  deploying stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:18:46 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:19:16 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:20:17 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:21:17 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:22:17 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:23:17 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:24:17 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:25:17 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:26:18 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-cluster"
2026-06-07 11:26:19 [!]  recommended policies were found for "vpc-cni" addon, but since OIDC is disabled on the cluster, eksctl cannot configure the requested permissions; the recommended way to provide IAM permissions for "vpc-cni" addon is via pod identity associations; after addon creation is completed, add all recommended policies to the config file, under `addon.PodIdentityAssociations`, and run `eksctl update addon`
2026-06-07 11:26:19 [ℹ]  creating addon: vpc-cni
2026-06-07 11:26:20 [ℹ]  successfully created addon: vpc-cni
2026-06-07 11:26:20 [ℹ]  creating addon: kube-proxy
2026-06-07 11:26:21 [ℹ]  successfully created addon: kube-proxy
2026-06-07 11:26:21 [ℹ]  creating addon: coredns
2026-06-07 11:26:21 [ℹ]  successfully created addon: coredns
2026-06-07 11:28:22 [ℹ]  building managed nodegroup stack "eksctl-sample-cluster-3-nodegroup-standard-workers"
2026-06-07 11:28:23 [ℹ]  deploying stack "eksctl-sample-cluster-3-nodegroup-standard-workers"
2026-06-07 11:28:23 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-nodegroup-standard-workers"
2026-06-07 11:28:53 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-nodegroup-standard-workers"
2026-06-07 11:29:52 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-nodegroup-standard-workers"
2026-06-07 11:31:50 [ℹ]  waiting for CloudFormation stack "eksctl-sample-cluster-3-nodegroup-standard-workers"
2026-06-07 11:31:50 [ℹ]  waiting for the control plane to become ready
2026-06-07 11:31:51 [✔]  saved kubeconfig as "C:\\Users\\abhis\\.kube\\config"
2026-06-07 11:31:51 [ℹ]  no tasks
2026-06-07 11:31:51 [✔]  all EKS cluster resources for "sample-cluster-3" have been created
2026-06-07 11:31:51 [ℹ]  nodegroup "standard-workers" has 2 node(s)
2026-06-07 11:31:51 [ℹ]  node "ip-192-168-25-250.ap-south-1.compute.internal" is ready
2026-06-07 11:31:51 [ℹ]  node "ip-192-168-47-101.ap-south-1.compute.internal" is ready
2026-06-07 11:31:51 [ℹ]  waiting for at least 2 node(s) to become ready in "standard-workers"
2026-06-07 11:31:51 [ℹ]  nodegroup "standard-workers" has 2 node(s)
2026-06-07 11:31:51 [ℹ]  node "ip-192-168-25-250.ap-south-1.compute.internal" is ready
2026-06-07 11:31:51 [ℹ]  node "ip-192-168-47-101.ap-south-1.compute.internal" is ready
2026-06-07 11:31:51 [✔]  created 1 managed nodegroup(s) in cluster "sample-cluster-3"
2026-06-07 11:31:51 [ℹ]  creating addon: metrics-server
2026-06-07 11:31:51 [ℹ]  successfully created addon: metrics-server
2026-06-07 11:31:53 [ℹ]  kubectl command should work with "C:\\Users\\abhis\\.kube\\config", try 'kubectl get nodes'
2026-06-07 11:31:53 [✔]  EKS cluster "sample-cluster-3" in "ap-south-1" region is ready
```


Configuring the kubectl with eks cluster:
```bash
aws eks --region ap-south-1 update-kubeconfig --name sample-cluster-3
kubectl get nodes
kubectl get namespaces
```

> <img width="773" height="295" alt="image" src="https://github.com/user-attachments/assets/0d735254-58ef-4362-9ccb-72cf0fb5337d" />

AWS UI
> <img width="1626" height="940" alt="image" src="https://github.com/user-attachments/assets/92b8c9f0-5c8d-4d6c-9ad2-6f3d4ce53b22" />


cloudformation
> <img width="1399" height="466" alt="image" src="https://github.com/user-attachments/assets/27f4f0ae-c325-460b-aa4c-134bee2c1536" />

> <img width="1546" height="893" alt="image" src="https://github.com/user-attachments/assets/db4ef1a1-f48e-459d-93cc-96837c181a48" />

> <img width="1675" height="931" alt="image" src="https://github.com/user-attachments/assets/8529f907-694b-4717-bd58-cad612df0bab" />

> <img width="1411" height="965" alt="image" src="https://github.com/user-attachments/assets/d257e108-e5b1-451f-94f6-ef13290480b0" />

> <img width="1684" height="970" alt="image" src="https://github.com/user-attachments/assets/72fbe6b2-f09d-4aa2-b506-0258c5e496e8" />

> <img width="1740" height="935" alt="image" src="https://github.com/user-attachments/assets/4ceb4ade-0cdd-4750-ba60-3c74dc570d2c" />

```
{
  "AWSTemplateFormatVersion": "2010-09-09",
  "Description": "EKS cluster (dedicated VPC: true, dedicated IAM: true) [created and managed by eksctl]",
  "Mappings": {
    "ServicePrincipalPartitionMap": {
      "aws": {
        "EC2": "ec2.amazonaws.com",
        "EKS": "eks.amazonaws.com",
        "EKSFargatePods": "eks-fargate-pods.amazonaws.com",
        "IRA": "rolesanywhere.amazonaws.com",
        "SSM": "ssm.amazonaws.com"
      },
      "aws-cn": {
        "EC2": "ec2.amazonaws.com.cn",
        "EKS": "eks.amazonaws.com",
        "EKSFargatePods": "eks-fargate-pods.amazonaws.com"
      },
      "aws-iso": {
        "EC2": "ec2.c2s.ic.gov",
        "EKS": "eks.amazonaws.com",
        "EKSFargatePods": "eks-fargate-pods.amazonaws.com"
      },
      "aws-iso-b": {
        "EC2": "ec2.sc2s.sgov.gov",
        "EKS": "eks.amazonaws.com",
        "EKSFargatePods": "eks-fargate-pods.amazonaws.com"
      },
      "aws-iso-e": {
        "EC2": "ec2.amazonaws.com",
        "EKS": "eks.amazonaws.com",
        "EKSFargatePods": "eks-fargate-pods.amazonaws.com"
      },
      "aws-iso-f": {
        "EC2": "ec2.amazonaws.com",
        "EKS": "eks.amazonaws.com",
        "EKSFargatePods": "eks-fargate-pods.amazonaws.com"
      },
      "aws-us-gov": {
        "EC2": "ec2.amazonaws.com",
        "EKS": "eks.amazonaws.com",
        "EKSFargatePods": "eks-fargate-pods.amazonaws.com",
        "IRA": "rolesanywhere.amazonaws.com",
        "SSM": "ssm.amazonaws.com"
      }
    }
  },
  "Resources": {
    "ClusterSharedNodeSecurityGroup": {
      "Type": "AWS::EC2::SecurityGroup",
      "Properties": {
        "GroupDescription": "Communication between all nodes in the cluster",
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/ClusterSharedNodeSecurityGroup"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "ControlPlane": {
      "Type": "AWS::EKS::Cluster",
      "Properties": {
        "AccessConfig": {
          "AuthenticationMode": "API_AND_CONFIG_MAP",
          "BootstrapClusterCreatorAdminPermissions": true
        },
        "BootstrapSelfManagedAddons": false,
        "KubernetesNetworkConfig": {
          "IpFamily": "ipv4"
        },
        "Name": "sample-cluster-3",
        "ResourcesVpcConfig": {
          "EndpointPrivateAccess": false,
          "EndpointPublicAccess": true,
          "SecurityGroupIds": [
            {
              "Ref": "ControlPlaneSecurityGroup"
            }
          ],
          "SubnetIds": [
            {
              "Ref": "SubnetPublicAPSOUTH1A"
            },
            {
              "Ref": "SubnetPublicAPSOUTH1B"
            },
            {
              "Ref": "SubnetPublicAPSOUTH1C"
            },
            {
              "Ref": "SubnetPrivateAPSOUTH1A"
            },
            {
              "Ref": "SubnetPrivateAPSOUTH1B"
            },
            {
              "Ref": "SubnetPrivateAPSOUTH1C"
            }
          ]
        },
        "RoleArn": {
          "Fn::GetAtt": [
            "ServiceRole",
            "Arn"
          ]
        },
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/ControlPlane"
            }
          }
        ],
        "Version": "1.34"
      }
    },
    "ControlPlaneSecurityGroup": {
      "Type": "AWS::EC2::SecurityGroup",
      "Properties": {
        "GroupDescription": "Communication between the control plane and worker nodegroups",
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/ControlPlaneSecurityGroup"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "IngressDefaultClusterToNodeSG": {
      "Type": "AWS::EC2::SecurityGroupIngress",
      "Properties": {
        "Description": "Allow managed and unmanaged nodes to communicate with each other (all ports)",
        "FromPort": 0,
        "GroupId": {
          "Ref": "ClusterSharedNodeSecurityGroup"
        },
        "IpProtocol": "-1",
        "SourceSecurityGroupId": {
          "Fn::GetAtt": [
            "ControlPlane",
            "ClusterSecurityGroupId"
          ]
        },
        "ToPort": 65535
      }
    },
    "IngressInterNodeGroupSG": {
      "Type": "AWS::EC2::SecurityGroupIngress",
      "Properties": {
        "Description": "Allow nodes to communicate with each other (all ports)",
        "FromPort": 0,
        "GroupId": {
          "Ref": "ClusterSharedNodeSecurityGroup"
        },
        "IpProtocol": "-1",
        "SourceSecurityGroupId": {
          "Ref": "ClusterSharedNodeSecurityGroup"
        },
        "ToPort": 65535
      }
    },
    "IngressNodeToDefaultClusterSG": {
      "Type": "AWS::EC2::SecurityGroupIngress",
      "Properties": {
        "Description": "Allow unmanaged nodes to communicate with control plane (all ports)",
        "FromPort": 0,
        "GroupId": {
          "Fn::GetAtt": [
            "ControlPlane",
            "ClusterSecurityGroupId"
          ]
        },
        "IpProtocol": "-1",
        "SourceSecurityGroupId": {
          "Ref": "ClusterSharedNodeSecurityGroup"
        },
        "ToPort": 65535
      }
    },
    "InternetGateway": {
      "Type": "AWS::EC2::InternetGateway",
      "Properties": {
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/InternetGateway"
            }
          }
        ]
      }
    },
    "NATGateway": {
      "Type": "AWS::EC2::NatGateway",
      "Properties": {
        "AllocationId": {
          "Fn::GetAtt": [
            "NATIP",
            "AllocationId"
          ]
        },
        "SubnetId": {
          "Ref": "SubnetPublicAPSOUTH1A"
        },
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/NATGateway"
            }
          }
        ]
      }
    },
    "NATIP": {
      "Type": "AWS::EC2::EIP",
      "Properties": {
        "Domain": "vpc",
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/NATIP"
            }
          }
        ]
      }
    },
    "NATPrivateSubnetRouteAPSOUTH1A": {
      "Type": "AWS::EC2::Route",
      "Properties": {
        "DestinationCidrBlock": "0.0.0.0/0",
        "NatGatewayId": {
          "Ref": "NATGateway"
        },
        "RouteTableId": {
          "Ref": "PrivateRouteTableAPSOUTH1A"
        }
      }
    },
    "NATPrivateSubnetRouteAPSOUTH1B": {
      "Type": "AWS::EC2::Route",
      "Properties": {
        "DestinationCidrBlock": "0.0.0.0/0",
        "NatGatewayId": {
          "Ref": "NATGateway"
        },
        "RouteTableId": {
          "Ref": "PrivateRouteTableAPSOUTH1B"
        }
      }
    },
    "NATPrivateSubnetRouteAPSOUTH1C": {
      "Type": "AWS::EC2::Route",
      "Properties": {
        "DestinationCidrBlock": "0.0.0.0/0",
        "NatGatewayId": {
          "Ref": "NATGateway"
        },
        "RouteTableId": {
          "Ref": "PrivateRouteTableAPSOUTH1C"
        }
      }
    },
    "PrivateRouteTableAPSOUTH1A": {
      "Type": "AWS::EC2::RouteTable",
      "Properties": {
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/PrivateRouteTableAPSOUTH1A"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "PrivateRouteTableAPSOUTH1B": {
      "Type": "AWS::EC2::RouteTable",
      "Properties": {
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/PrivateRouteTableAPSOUTH1B"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "PrivateRouteTableAPSOUTH1C": {
      "Type": "AWS::EC2::RouteTable",
      "Properties": {
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/PrivateRouteTableAPSOUTH1C"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "PublicRouteTable": {
      "Type": "AWS::EC2::RouteTable",
      "Properties": {
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/PublicRouteTable"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "PublicSubnetRoute": {
      "Type": "AWS::EC2::Route",
      "Properties": {
        "DestinationCidrBlock": "0.0.0.0/0",
        "GatewayId": {
          "Ref": "InternetGateway"
        },
        "RouteTableId": {
          "Ref": "PublicRouteTable"
        }
      },
      "DependsOn": [
        "VPCGatewayAttachment"
      ]
    },
    "RouteTableAssociationPrivateAPSOUTH1A": {
      "Type": "AWS::EC2::SubnetRouteTableAssociation",
      "Properties": {
        "RouteTableId": {
          "Ref": "PrivateRouteTableAPSOUTH1A"
        },
        "SubnetId": {
          "Ref": "SubnetPrivateAPSOUTH1A"
        }
      }
    },
    "RouteTableAssociationPrivateAPSOUTH1B": {
      "Type": "AWS::EC2::SubnetRouteTableAssociation",
      "Properties": {
        "RouteTableId": {
          "Ref": "PrivateRouteTableAPSOUTH1B"
        },
        "SubnetId": {
          "Ref": "SubnetPrivateAPSOUTH1B"
        }
      }
    },
    "RouteTableAssociationPrivateAPSOUTH1C": {
      "Type": "AWS::EC2::SubnetRouteTableAssociation",
      "Properties": {
        "RouteTableId": {
          "Ref": "PrivateRouteTableAPSOUTH1C"
        },
        "SubnetId": {
          "Ref": "SubnetPrivateAPSOUTH1C"
        }
      }
    },
    "RouteTableAssociationPublicAPSOUTH1A": {
      "Type": "AWS::EC2::SubnetRouteTableAssociation",
      "Properties": {
        "RouteTableId": {
          "Ref": "PublicRouteTable"
        },
        "SubnetId": {
          "Ref": "SubnetPublicAPSOUTH1A"
        }
      }
    },
    "RouteTableAssociationPublicAPSOUTH1B": {
      "Type": "AWS::EC2::SubnetRouteTableAssociation",
      "Properties": {
        "RouteTableId": {
          "Ref": "PublicRouteTable"
        },
        "SubnetId": {
          "Ref": "SubnetPublicAPSOUTH1B"
        }
      }
    },
    "RouteTableAssociationPublicAPSOUTH1C": {
      "Type": "AWS::EC2::SubnetRouteTableAssociation",
      "Properties": {
        "RouteTableId": {
          "Ref": "PublicRouteTable"
        },
        "SubnetId": {
          "Ref": "SubnetPublicAPSOUTH1C"
        }
      }
    },
    "ServiceRole": {
      "Type": "AWS::IAM::Role",
      "Properties": {
        "AssumeRolePolicyDocument": {
          "Statement": [
            {
              "Action": [
                "sts:AssumeRole",
                "sts:TagSession"
              ],
              "Effect": "Allow",
              "Principal": {
                "Service": [
                  {
                    "Fn::FindInMap": [
                      "ServicePrincipalPartitionMap",
                      {
                        "Ref": "AWS::Partition"
                      },
                      "EKS"
                    ]
                  }
                ]
              }
            }
          ],
          "Version": "2012-10-17"
        },
        "ManagedPolicyArns": [
          {
            "Fn::Sub": "arn:${AWS::Partition}:iam::aws:policy/AmazonEKSClusterPolicy"
          },
          {
            "Fn::Sub": "arn:${AWS::Partition}:iam::aws:policy/AmazonEKSVPCResourceController"
          }
        ],
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/ServiceRole"
            }
          }
        ]
      }
    },
    "SubnetPrivateAPSOUTH1A": {
      "Type": "AWS::EC2::Subnet",
      "Properties": {
        "AvailabilityZone": "ap-south-1a",
        "CidrBlock": "192.168.96.0/19",
        "Tags": [
          {
            "Key": "kubernetes.io/role/internal-elb",
            "Value": "1"
          },
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/SubnetPrivateAPSOUTH1A"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "SubnetPrivateAPSOUTH1B": {
      "Type": "AWS::EC2::Subnet",
      "Properties": {
        "AvailabilityZone": "ap-south-1b",
        "CidrBlock": "192.168.128.0/19",
        "Tags": [
          {
            "Key": "kubernetes.io/role/internal-elb",
            "Value": "1"
          },
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/SubnetPrivateAPSOUTH1B"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "SubnetPrivateAPSOUTH1C": {
      "Type": "AWS::EC2::Subnet",
      "Properties": {
        "AvailabilityZone": "ap-south-1c",
        "CidrBlock": "192.168.160.0/19",
        "Tags": [
          {
            "Key": "kubernetes.io/role/internal-elb",
            "Value": "1"
          },
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/SubnetPrivateAPSOUTH1C"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "SubnetPublicAPSOUTH1A": {
      "Type": "AWS::EC2::Subnet",
      "Properties": {
        "AvailabilityZone": "ap-south-1a",
        "CidrBlock": "192.168.0.0/19",
        "MapPublicIpOnLaunch": true,
        "Tags": [
          {
            "Key": "kubernetes.io/role/elb",
            "Value": "1"
          },
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/SubnetPublicAPSOUTH1A"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "SubnetPublicAPSOUTH1B": {
      "Type": "AWS::EC2::Subnet",
      "Properties": {
        "AvailabilityZone": "ap-south-1b",
        "CidrBlock": "192.168.32.0/19",
        "MapPublicIpOnLaunch": true,
        "Tags": [
          {
            "Key": "kubernetes.io/role/elb",
            "Value": "1"
          },
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/SubnetPublicAPSOUTH1B"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "SubnetPublicAPSOUTH1C": {
      "Type": "AWS::EC2::Subnet",
      "Properties": {
        "AvailabilityZone": "ap-south-1c",
        "CidrBlock": "192.168.64.0/19",
        "MapPublicIpOnLaunch": true,
        "Tags": [
          {
            "Key": "kubernetes.io/role/elb",
            "Value": "1"
          },
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/SubnetPublicAPSOUTH1C"
            }
          }
        ],
        "VpcId": {
          "Ref": "VPC"
        }
      }
    },
    "VPC": {
      "Type": "AWS::EC2::VPC",
      "Properties": {
        "CidrBlock": "192.168.0.0/16",
        "EnableDnsHostnames": true,
        "EnableDnsSupport": true,
        "Tags": [
          {
            "Key": "Name",
            "Value": {
              "Fn::Sub": "${AWS::StackName}/VPC"
            }
          }
        ]
      }
    },
    "VPCGatewayAttachment": {
      "Type": "AWS::EC2::VPCGatewayAttachment",
      "Properties": {
        "InternetGatewayId": {
          "Ref": "InternetGateway"
        },
        "VpcId": {
          "Ref": "VPC"
        }
      }
    }
  },
  "Outputs": {
    "ARN": {
      "Value": {
        "Fn::GetAtt": [
          "ControlPlane",
          "Arn"
        ]
      },
      "Export": {
        "Name": {
          "Fn::Sub": "${AWS::StackName}::ARN"
        }
      }
    },
    "CertificateAuthorityData": {
      "Value": {
        "Fn::GetAtt": [
          "ControlPlane",
          "CertificateAuthorityData"
        ]
      }
    },
    "ClusterSecurityGroupId": {
      "Value": {
        "Fn::GetAtt": [
          "ControlPlane",
          "ClusterSecurityGroupId"
        ]
      },
      "Export": {
        "Name": {
          "Fn::Sub": "${AWS::StackName}::ClusterSecurityGroupId"
        }
      }
    },
    "ClusterStackName": {
      "Value": {
        "Ref": "AWS::StackName"
      }
    },
    "Endpoint": {
      "Value": {
        "Fn::GetAtt": [
          "ControlPlane",
          "Endpoint"
        ]
      },
      "Export": {
        "Name": {
          "Fn::Sub": "${AWS::StackName}::Endpoint"
        }
      }
    },
    "FeatureNATMode": {
      "Value": "Single"
    },
    "SecurityGroup": {
      "Value": {
        "Ref": "ControlPlaneSecurityGroup"
      },
      "Export": {
        "Name": {
          "Fn::Sub": "${AWS::StackName}::SecurityGroup"
        }
      }
    },
    "ServiceRoleARN": {
      "Value": {
        "Fn::GetAtt": [
          "ServiceRole",
          "Arn"
        ]
      },
      "Export": {
        "Name": {
          "Fn::Sub": "${AWS::StackName}::ServiceRoleARN"
        }
      }
    },
    "SharedNodeSecurityGroup": {
      "Value": {
        "Ref": "ClusterSharedNodeSecurityGroup"
      },
      "Export": {
        "Name": {
          "Fn::Sub": "${AWS::StackName}::SharedNodeSecurityGroup"
        }
      }
    },
    "SubnetsPrivate": {
      "Value": {
        "Fn::Join": [
          ",",
          [
            {
              "Ref": "SubnetPrivateAPSOUTH1A"
            },
            {
              "Ref": "SubnetPrivateAPSOUTH1B"
            },
            {
              "Ref": "SubnetPrivateAPSOUTH1C"
            }
          ]
        ]
      },
      "Export": {
        "Name": {
          "Fn::Sub": "${AWS::StackName}::SubnetsPrivate"
        }
      }
    },
    "SubnetsPublic": {
      "Value": {
        "Fn::Join": [
          ",",
          [
            {
              "Ref": "SubnetPublicAPSOUTH1A"
            },
            {
              "Ref": "SubnetPublicAPSOUTH1B"
            },
            {
              "Ref": "SubnetPublicAPSOUTH1C"
            }
          ]
        ]
      },
      "Export": {
        "Name": {
          "Fn::Sub": "${AWS::StackName}::SubnetsPublic"
        }
      }
    },
    "VPC": {
      "Value": {
        "Ref": "VPC"
      },
      "Export": {
        "Name": {
          "Fn::Sub": "${AWS::StackName}::VPC"
        }
      }
    }
  }
}
```

>

