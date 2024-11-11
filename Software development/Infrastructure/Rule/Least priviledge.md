## Porsche
[[AWS]] least priviledge rule at the time I work at [[emotive]] to support [[Porsche]]

### Rules

#### PA_FULLADMIN

- Trust relationships
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"Federated": "arn:aws:iam::722424416689:saml-provider/idp.cloud.vwgroup.com"
			},
			"Action": "sts:AssumeRoleWithSAML",
			"Condition": {
				"StringEquals": {
					"SAML:aud": "<https://signin.aws.amazon.com/saml>"
				}
			}
		},
		{
			"Effect": "Allow",
			"Principal": {
				"AWS": "arn:aws:iam::722424416689:root"
			},
			"Action": "sts:AssumeRole",
			"Condition": {
				"StringEquals": {
					"sts:ExternalId": "e6fb0b3eb664133bfa78a0a1c696e50a"
				},
				"ArnLike": {
					"aws:PrincipalArn": "arn:aws:iam::722424416689:role/gitc/gitc-hyb-awstoken-pki-manager*"
				}
			}
		}
	]
}
```
    
- AWS Managed Policy: AdministratorAccess

#### PA_DEVELOPER

- Trust relationships
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"Federated": "arn:aws:iam::722424416689:saml-provider/idp.cloud.vwgroup.com"
			},
			"Action": "sts:AssumeRoleWithSAML",
			"Condition": {
				"StringEquals": {
					"SAML:aud": "<https://signin.aws.amazon.com/saml>"
				}
			}
		},
		{
			"Effect": "Allow",
			"Principal": {
				"AWS": "arn:aws:iam::722424416689:root"
			},
			"Action": "sts:AssumeRole",
			"Condition": {
				"StringEquals": {
					"sts:ExternalId": "e6fb0b3eb664133bfa78a0a1c696e50a"
				},
				"ArnLike": {
					"aws:PrincipalArn": "arn:aws:iam::722424416689:role/gitc/gitc-hyb-awstoken-pki-manager*"
				}
			}
		}
	]
}
```
    
- GITC-InstancePurchaseLimits
    
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": [
                    "ec2:AcceptReservedInstancesExchangeQuote",
                    "ec2:CancelReservedInstancesListing",
                    "ec2:CreateReservedInstancesListing",
                    "ec2:GetReservedInstancesExchangeQuote",
                    "ec2:ModifyReservedInstances",
                    "ec2:PurchaseReservedInstancesOffering",
                    "ec2:PurchaseScheduledInstances"
                ],
                "Resource": "*",
                "Effect": "Deny",
                "Sid": "DenyEc2ReservedInstances"
            }
        ]
    }
    ```
    
- GITC-MarketplaceSubscribe
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"aws-marketplace:ViewSubscriptions",
				"aws-marketplace:ListPrivateMarketplaceProducts",
				"aws-marketplace:ListPrivateMarketplaceRequests",
				"aws-marketplace:DescribePrivateMarketplaceProducts",
				"aws-marketplace:DescribePrivateMarketplaceRequests",
				"aws-marketplace:DescribePrivateMarketplaceStatus",
				"aws-marketplace:CreatePrivateMarketplaceRequests",
				"aws-marketplace:Subscribe"
			],
			"Resource": "*",
			"Effect": "Allow",
			"Sid": "MarketplaceSubscriberAllow"
		},
		{
			"Action": [
				"aws-marketplace:Unsubscribe",
				"aws-marketplace:AssociateProductsWithPrivateMarketplace",
				"aws-marketplace:CreatePrivateMarketplace",
				"aws-marketplace:CreatePrivateMarketplaceProfile",
				"aws-marketplace:DescribePrivateMarketplaceProfile",
				"aws-marketplace:DescribePrivateMarketplaceSettings",
				"aws-marketplace:DisassociateProductsFromPrivateMarketplace",
				"aws-marketplace:StartPrivateMarketplace",
				"aws-marketplace:StopPrivateMarketplace",
				"aws-marketplace:UpdatePrivateMarketplaceProfile",
				"aws-marketplace:UpdatePrivateMarketplaceSettings",
				"aws-marketplace:MeterUsage",
				"aws-marketplace:BatchMeterUsage",
				"aws-marketplace:ResolveCustomer",
				"aws-marketplace-management:*"
			],
			"Resource": "*",
			"Effect": "Deny",
			"Sid": "MarketplaceSubscriberDeny"
		}
	]
}
```
    
- GITC-PrivilegeEscalationLimits
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"iam:CreateSAMLProvider",
				"iam:DeleteSAMLProvider",
				"iam:UpdateSAMLProvider"
			],
			"Resource": [
				"arn:aws:iam::722424416689:saml-provider/*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToSamlProviders"
		},
		{
			"Action": [
				"iam:CreateOpenIDConnectProvider",
				"iam:DeleteOpenIDConnectProvider",
				"iam:UpdateOpenIDConnectProviderThumbprint",
				"iam:AddClientIDToOpenIDConnectProvider",
				"iam:RemoveClientIDFromOpenIDConnectProvider"
			],
			"Resource": [
				"arn:aws:iam::722424416689:oidc-provider/*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToOidcProviders"
		},
		{
			"Action": [
				"iam:UpdateAccountPasswordPolicy",
				"iam:DeleteAccountPasswordPolicy"
			],
			"Resource": "*",
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcPasswordPolicy"
		}
	]
}
```
    
- GITC-SecurityAuditLimits
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"logs:DeleteLogGroup",
				"logs:DeleteLogStream"
			],
			"Resource": [
				"arn:aws:logs:*:722424416689:log-group:CloudTrail/*",
				"arn:aws:logs:*:722424416689:log-group:CloudTrail/*:*:*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcCloudWatchLogGroups"
		},
		{
			"Action": [
				"sns:DeleteTopic",
				"sns:RemovePermission",
				"sns:Unsubscribe",
				"sns:DeleteSubscription"
			],
			"Resource": [
				"arn:aws:sns:*:722424416689:gitc*",
				"arn:aws:sns:*:722424416689:Oa*",
				"arn:aws:sns:*:722424416689:*CloudTrailAlerts",
				"arn:aws:sns:*:722424416689:cfs*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcSnsTopics"
		},
		{
			"Action": [
				"events:*"
			],
			"Resource": [
				"arn:aws:events:*:722424416689:rule/gitc*",
				"arn:aws:events:*:722424416689:rule/custodian-gitc*",
				"arn:aws:events:*:722424416689:rule/cfs*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcCloudWatchEvents"
		},
		{
			"Action": [
				"secretsmanager:GetResourcePolicy",
				"secretsmanager:GetSecretValue",
				"secretsmanager:DescribeSecret",
				"secretsmanager:PutResourcePolicy",
				"secretsmanager:DeleteResourcePolicy",
				"secretsmanager:RestoreSecret",
				"secretsmanager:PutSecretValue",
				"secretsmanager:DeleteSecret",
				"secretsmanager:RotateSecret",
				"secretsmanager:UpdateSecretVersionStage",
				"secretsmanager:UpdateSecret"
			],
			"Resource": [
				"arn:aws:secretsmanager:*:722424416689:secret:/gitc*",
				"arn:aws:secretsmanager:*:722424416689:secret:/cfs*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcsecretsmanager"
		}
	]
}
```
    
- AWS Managed Policy: AdministratorAccess

#### PA_KMSADMIN

- Trust relationships
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"Federated": "arn:aws:iam::722424416689:saml-provider/idp.cloud.vwgroup.com"
			},
			"Action": "sts:AssumeRoleWithSAML",
			"Condition": {
				"StringEquals": {
					"SAML:aud": "<https://signin.aws.amazon.com/saml>"
				}
			}
		},
		{
			"Effect": "Allow",
			"Principal": {
				"AWS": "arn:aws:iam::722424416689:root"
			},
			"Action": "sts:AssumeRole",
			"Condition": {
				"StringEquals": {
					"sts:ExternalId": "e6fb0b3eb664133bfa78a0a1c696e50a"
				},
				"ArnLike": {
					"aws:PrincipalArn": "arn:aws:iam::722424416689:role/gitc/gitc-hyb-awstoken-pki-manager*"
				}
			}
		}
	]
}
```
    
- GITC-kmsadmin-policy
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"kms:Create*",
				"kms:Describe*",
				"kms:Enable*",
				"kms:List*",
				"kms:Put*",
				"kms:Update*",
				"kms:Revoke*",
				"kms:Disable*",
				"kms:Get*",
				"kms:Delete*",
				"kms:TagResource",
				"kms:UntagResource",
				"kms:ScheduleKeyDeletion",
				"kms:CancelKeyDeletion"
			],
			"Resource": [
				"arn:aws:kms:eu-west-1:722424416689:key/*"
			],
			"Effect": "Allow"
		}
	]
}
```

#### PA_OPERATOR

- Trust relationships
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"Federated": "arn:aws:iam::722424416689:saml-provider/idp.cloud.vwgroup.com"
			},
			"Action": "sts:AssumeRoleWithSAML",
			"Condition": {
				"StringEquals": {
					"SAML:aud": "<https://signin.aws.amazon.com/saml>"
				}
			}
		},
		{
			"Effect": "Allow",
			"Principal": {
				"AWS": "arn:aws:iam::722424416689:root"
			},
			"Action": "sts:AssumeRole",
			"Condition": {
				"StringEquals": {
					"sts:ExternalId": "e6fb0b3eb664133bfa78a0a1c696e50a"
				},
				"ArnLike": {
					"aws:PrincipalArn": "arn:aws:iam::722424416689:role/gitc/gitc-hyb-awstoken-pki-manager*"
				}
			}
		}
	]
}
```
    
- GITC-MarketplaceReadOnly
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"iam:CreateSAMLProvider",
				"iam:DeleteSAMLProvider",
				"iam:UpdateSAMLProvider"
			],
			"Resource": [
				"arn:aws:iam::722424416689:saml-provider/*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToSamlProviders"
		},
		{
			"Action": [
				"iam:CreateOpenIDConnectProvider",
				"iam:DeleteOpenIDConnectProvider",
				"iam:UpdateOpenIDConnectProviderThumbprint",
				"iam:AddClientIDToOpenIDConnectProvider",
				"iam:RemoveClientIDFromOpenIDConnectProvider"
			],
			"Resource": [
				"arn:aws:iam::722424416689:oidc-provider/*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToOidcProviders"
		},
		{
			"Action": [
				"iam:UpdateAccountPasswordPolicy",
				"iam:DeleteAccountPasswordPolicy"
			],
			"Resource": "*",
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcPasswordPolicy"
		}
	]
}
```
    
- GITC-PrivilegeEscalationLimits
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"iam:CreateSAMLProvider",
				"iam:DeleteSAMLProvider",
				"iam:UpdateSAMLProvider"
			],
			"Resource": [
				"arn:aws:iam::722424416689:saml-provider/*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToSamlProviders"
		},
		{
			"Action": [
				"iam:CreateOpenIDConnectProvider",
				"iam:DeleteOpenIDConnectProvider",
				"iam:UpdateOpenIDConnectProviderThumbprint",
				"iam:AddClientIDToOpenIDConnectProvider",
				"iam:RemoveClientIDFromOpenIDConnectProvider"
			],
			"Resource": [
				"arn:aws:iam::722424416689:oidc-provider/*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToOidcProviders"
		},
		{
			"Action": [
				"iam:UpdateAccountPasswordPolicy",
				"iam:DeleteAccountPasswordPolicy"
			],
			"Resource": "*",
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcPasswordPolicy"
		}
	]
}
```
    
- GITC-SecurityAuditLimits
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"logs:DeleteLogGroup",
				"logs:DeleteLogStream"
			],
			"Resource": [
				"arn:aws:logs:*:722424416689:log-group:CloudTrail/*",
				"arn:aws:logs:*:722424416689:log-group:CloudTrail/*:*:*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcCloudWatchLogGroups"
		},
		{
			"Action": [
				"sns:DeleteTopic",
				"sns:RemovePermission",
				"sns:Unsubscribe",
				"sns:DeleteSubscription"
			],
			"Resource": [
				"arn:aws:sns:*:722424416689:gitc*",
				"arn:aws:sns:*:722424416689:Oa*",
				"arn:aws:sns:*:722424416689:*CloudTrailAlerts",
				"arn:aws:sns:*:722424416689:cfs*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcSnsTopics"
		},
		{
			"Action": [
				"events:*"
			],
			"Resource": [
				"arn:aws:events:*:722424416689:rule/gitc*",
				"arn:aws:events:*:722424416689:rule/custodian-gitc*",
				"arn:aws:events:*:722424416689:rule/cfs*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcCloudWatchEvents"
		},
		{
			"Action": [
				"secretsmanager:GetResourcePolicy",
				"secretsmanager:GetSecretValue",
				"secretsmanager:DescribeSecret",
				"secretsmanager:PutResourcePolicy",
				"secretsmanager:DeleteResourcePolicy",
				"secretsmanager:RestoreSecret",
				"secretsmanager:PutSecretValue",
				"secretsmanager:DeleteSecret",
				"secretsmanager:RotateSecret",
				"secretsmanager:UpdateSecretVersionStage",
				"secretsmanager:UpdateSecret"
			],
			"Resource": [
				"arn:aws:secretsmanager:*:722424416689:secret:/gitc*",
				"arn:aws:secretsmanager:*:722424416689:secret:/cfs*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcsecretsmanager"
		}
	]
}
```
    
- AWS Managed Policy: SupportUser
    
- AWS Managed Policy: ViewOnlyAccess
    
- AWS Managed Policy: SecurityAudit
    
- AWS Managed Policy: AmazonDynamoDBReadOnlyAccess
    
- AWS Managed Policy: AWSCodeCommitReadOnly
    
- AWS Managed Policy: AWSStepFunctionsReadOnlyAccess
    
- AWS Managed Policy: AWSServiceCatalogEndUserFullAccess
    
- GITC-metadata-data-permission
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"dax:ListTables",
				"ds:ListAuthorizedApplications",
				"ds:DescribeRoles",
				"ec2:GetEbsEncryptionByDefault",
				"ecr:Describe*",
				"securityhub:DescribeHub",
				"securityhub:GetFindings",
				"apigateway:GET",
				"acm-pca:GetCertificateAuthorityCertificate",
				"acm-pca:ListCertificateAuthorities",
				"acm-pca:DescribeCertificateAuthority",
				"iotanalytics:List*",
				"iotanalytics:Describe*",
				"iotevents:Describe*",
				"iotevents:List*",
				"iotsitewise:List*",
				"iotsitewise:Describe*",
				"codepipeline:ListPipelineExecutions",
				"ssm:GetInventory",
				"lambda:GetFunction",
				"logs:Get*",
				"logs:FilterLogEvents",
				"cognito-idp:Get*"
			],
			"Resource": [
				"*"
			],
			"Effect": "Allow",
			"Sid": "AllowExtraReadListPermission"
		},
		{
			"Action": [
				"support:*"
			],
			"Resource": [
				"*"
			],
			"Effect": "Allow",
			"Sid": "AllowSupportCase"
		}
	]
}
```
    
- GITC-opeartion-permission
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"ec2:StartInstances",
				"ec2:StopInstance",
				"autoscaling:SuspendProcesses",
				"autoscaling:ResumeProcesses",
				"ecs:StartTask",
				"ecs:StopTask"
			],
			"Resource": "*",
			"Effect": "Allow",
			"Sid": "AllowStartStop"
		},
		{
			"Action": [
				"cognito-idp:AdminAddUserToGroup",
				"cognito-idp:AdminRemoveUserFromGroup",
				"cognito-idp:AdminListGroupsForUser",
				"cognito-idp:AdminUpdateUserAttributes",
				"cognito-idp:AdminGetUser"
			],
			"Resource": "*",
			"Effect": "Allow",
			"Sid": "AllowReadWrite"
		}
	]
}
```

#### PA_OPERATOR

- Trust relationships
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"Federated": "arn:aws:iam::722424416689:saml-provider/idp.cloud.vwgroup.com"
			},
			"Action": "sts:AssumeRoleWithSAML",
			"Condition": {
				"StringEquals": {
					"SAML:aud": "<https://signin.aws.amazon.com/saml>"
				}
			}
		},
		{
			"Effect": "Allow",
			"Principal": {
				"AWS": "arn:aws:iam::722424416689:root"
			},
			"Action": "sts:AssumeRole",
			"Condition": {
				"StringEquals": {
					"sts:ExternalId": "e6fb0b3eb664133bfa78a0a1c696e50a"
				},
				"ArnLike": {
					"aws:PrincipalArn": "arn:aws:iam::722424416689:role/gitc/gitc-hyb-awstoken-pki-manager*"
				}
			}
		}
	]
}
```
    
- GITC-MarketplaceReadOnly
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"iam:CreateSAMLProvider",
				"iam:DeleteSAMLProvider",
				"iam:UpdateSAMLProvider"
			],
			"Resource": [
				"arn:aws:iam::722424416689:saml-provider/*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToSamlProviders"
		},
		{
			"Action": [
				"iam:CreateOpenIDConnectProvider",
				"iam:DeleteOpenIDConnectProvider",
				"iam:UpdateOpenIDConnectProviderThumbprint",
				"iam:AddClientIDToOpenIDConnectProvider",
				"iam:RemoveClientIDFromOpenIDConnectProvider"
			],
			"Resource": [
				"arn:aws:iam::722424416689:oidc-provider/*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToOidcProviders"
		},
		{
			"Action": [
				"iam:UpdateAccountPasswordPolicy",
				"iam:DeleteAccountPasswordPolicy"
			],
			"Resource": "*",
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcPasswordPolicy"
		}
	]
}
```
    
- GITC-PrivilegeEscalationLimits
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"iam:CreateSAMLProvider",
				"iam:DeleteSAMLProvider",
				"iam:UpdateSAMLProvider"
			],
			"Resource": [
				"arn:aws:iam::722424416689:saml-provider/*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToSamlProviders"
		},
		{
			"Action": [
				"iam:CreateOpenIDConnectProvider",
				"iam:DeleteOpenIDConnectProvider",
				"iam:UpdateOpenIDConnectProviderThumbprint",
				"iam:AddClientIDToOpenIDConnectProvider",
				"iam:RemoveClientIDFromOpenIDConnectProvider"
			],
			"Resource": [
				"arn:aws:iam::722424416689:oidc-provider/*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToOidcProviders"
		},
		{
			"Action": [
				"iam:UpdateAccountPasswordPolicy",
				"iam:DeleteAccountPasswordPolicy"
			],
			"Resource": "*",
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcPasswordPolicy"
		}
	]
}
```
    
- GITC-SecurityAuditLimits
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"logs:DeleteLogGroup",
				"logs:DeleteLogStream"
			],
			"Resource": [
				"arn:aws:logs:*:722424416689:log-group:CloudTrail/*",
				"arn:aws:logs:*:722424416689:log-group:CloudTrail/*:*:*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcCloudWatchLogGroups"
		},
		{
			"Action": [
				"sns:DeleteTopic",
				"sns:RemovePermission",
				"sns:Unsubscribe",
				"sns:DeleteSubscription"
			],
			"Resource": [
				"arn:aws:sns:*:722424416689:gitc*",
				"arn:aws:sns:*:722424416689:Oa*",
				"arn:aws:sns:*:722424416689:*CloudTrailAlerts",
				"arn:aws:sns:*:722424416689:cfs*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcSnsTopics"
		},
		{
			"Action": [
				"events:*"
			],
			"Resource": [
				"arn:aws:events:*:722424416689:rule/gitc*",
				"arn:aws:events:*:722424416689:rule/custodian-gitc*",
				"arn:aws:events:*:722424416689:rule/cfs*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcCloudWatchEvents"
		},
		{
			"Action": [
				"secretsmanager:GetResourcePolicy",
				"secretsmanager:GetSecretValue",
				"secretsmanager:DescribeSecret",
				"secretsmanager:PutResourcePolicy",
				"secretsmanager:DeleteResourcePolicy",
				"secretsmanager:RestoreSecret",
				"secretsmanager:PutSecretValue",
				"secretsmanager:DeleteSecret",
				"secretsmanager:RotateSecret",
				"secretsmanager:UpdateSecretVersionStage",
				"secretsmanager:UpdateSecret"
			],
			"Resource": [
				"arn:aws:secretsmanager:*:722424416689:secret:/gitc*",
				"arn:aws:secretsmanager:*:722424416689:secret:/cfs*"
			],
			"Effect": "Deny",
			"Sid": "DenyChangesToGitcsecretsmanager"
		}
	]
}
```
    
- AWS Managed Policy: ViewOnlyAccess
    
- AWS Managed Policy: SystemAdministrator
    
- AWS Managed Policy: SecurityAudit
    
- AWS Managed Policy: AmazonDynamoDBReadOnlyAccess
    
- AWS Managed Policy: AWSCodeCommitReadOnly
    
- AWS Managed Policy: AWSStepFunctionsReadOnlyAccess
    
- AWS Managed Policy: AWSServiceCatalogEndUserFullAccess
    
- GITC-metadata-data-permission
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Action": [
				"dax:ListTables",
				"ds:ListAuthorizedApplications",
				"ds:DescribeRoles",
				"ec2:GetEbsEncryptionByDefault",
				"ecr:Describe*",
				"securityhub:DescribeHub",
				"securityhub:GetFindings",
				"apigateway:GET",
				"acm-pca:GetCertificateAuthorityCertificate",
				"acm-pca:ListCertificateAuthorities",
				"acm-pca:DescribeCertificateAuthority",
				"iotanalytics:List*",
				"iotanalytics:Describe*",
				"iotevents:Describe*",
				"iotevents:List*",
				"iotsitewise:List*",
				"iotsitewise:Describe*",
				"codepipeline:ListPipelineExecutions",
				"ssm:GetInventory",
				"lambda:GetFunction",
				"logs:Get*",
				"logs:FilterLogEvents",
				"cognito-idp:Get*"
			],
			"Resource": [
				"*"
			],
			"Effect": "Allow",
			"Sid": "AllowExtraReadListPermission"
		},
		{
			"Action": [
				"support:*"
			],
			"Resource": [
				"*"
			],
			"Effect": "Allow",
			"Sid": "AllowSupportCase"
		}
	]
}
```

#### Other policy

- GITC-AdministrativePermissionsBoundary
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Resource": "*",
			"Effect": "Allow",
			"NotAction": [
				"organizations:LeaveOrganization",
				"organizations:DeclineHandshake",
				"organizations:CreateOrganization",
				"organizations:InviteAccountToOrganization",
				"organizations:RemoveAccountFromOrganization",
				"account:EnableRegion",
				"account:DisableRegion",
				"cloudtrail:StopLogging",
				"config:Delete*",
				"config:StopConfigurationRecorder",
				"guardduty:CreateMembers",
				"guardduty:InviteMembers",
				"guardduty:DisassociateFromMasterAccount",
				"guardduty:DeleteDetector",
				"securityhub:CreateMembers",
				"securityhub:InviteMembers",
				"securityhub:DisassociateFromMasterAccount",
				"securityhub:DisableSecurityHub",
				"securityhub:BatchDisableStandards",
				"securityhub:UpdateStandardsControl",
				"access-analyzer:DeleteAnalyzer",
				"detective:CreateMembers",
				"detective:DisassociateMembership",
				"inspector:Stop*",
				"inspector:Delete*"
			],
			"Sid": "DenyChangesToCoreAwsServices"
		}
	]
}
```
    
- GITC-IamEntitiesManagement
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Condition": {
				"StringEquals": {
					"iam:PermissionsBoundary": "arn:aws:iam::722424416689:policy/gitc/GITC-ServicePermissionsBoundary"
				}
			},
			"Action": [
				"iam:DeleteUserPermissionsBoundary",
				"iam:DeleteRolePermissionsBoundary"
			],
			"Resource": [
				"arn:aws:iam::722424416689:role/*",
				"arn:aws:iam::722424416689:user/*"
			],
			"Effect": "Deny",
			"Sid": "DenyDeletingPermissionsBoundary"
		},
		{
			"Condition": {
				"ArnNotLike": {
					"iam:PolicyArn": "arn:aws:iam::722424416689:policy/gitc/*"
				}
			},
			"Action": [
				"iam:CreatePolicy",
				"iam:CreatePolicyVersion",
				"iam:DeletePolicy",
				"iam:DeletePolicyVersion",
				"iam:SetDefaultPolicyVersion"
			],
			"Resource": [
				"arn:aws:iam::722424416689:policy/*"
			],
			"Effect": "Allow",
			"Sid": "AllowCreatingPolicies"
		},
		{
			"Condition": {
				"ArnNotLike": {
					"iam:PolicyArn": "arn:aws:iam::722424416689:policy/gitc/*"
				}
			},
			"Action": [
				"iam:Attach*Policy",
				"iam:Detach*Policy",
				"iam:Put*Policy",
				"iam:DeleteUserPolicy",
				"iam:DeleteRolePolicy",
				"iam:DeleteGroupPolicy",
				"iam:UpdateAssumeRolePolicy"
			],
			"Resource": [
				"arn:aws:iam::722424416689:user/*",
				"arn:aws:iam::722424416689:role/*",
				"arn:aws:iam::722424416689:group/*"
			],
			"Effect": "Allow",
			"Sid": "AllowAttachingPolicies"
		},
		{
			"Action": [
				"iam:CreateGroup",
				"iam:DeleteGroup",
				"iam:UpdateGroup",
				"iam:AddUserToGroup",
				"iam:RemoveUserFromGroup"
			],
			"Resource": [
				"arn:aws:iam::722424416689:group/*"
			],
			"Effect": "Allow",
			"Sid": "AllowCreatingGroups"
		},
		{
			"Condition": {
				"StringEquals": {
					"iam:PermissionsBoundary": "arn:aws:iam::722424416689:policy/gitc/GITC-ServicePermissionsBoundary"
				}
			},
			"Action": [
				"iam:CreateUser",
				"iam:PutUserPermissionsBoundary"
			],
			"Resource": "arn:aws:iam::722424416689:user/*",
			"Effect": "Allow",
			"Sid": "AllowCreatingUsersWithBoundary"
		},
		{
			"Action": [
				"iam:*AccessKey",
				"iam:*SSHPublicKey",
				"iam:*ServiceSpecificCredential",
				"iam:*SigningCertificate",
				"iam:DeleteLoginProfile"
			],
			"Resource": "arn:aws:iam::722424416689:user/*",
			"Effect": "Allow",
			"Sid": "AllowManagingUsers"
		},
		{
			"Action": [
				"iam:CreateLoginProfile",
				"iam:UpdateLoginProfile"
			],
			"Resource": "arn:aws:iam::722424416689:user/*",
			"Effect": "Deny",
			"Sid": "DenyLoginProfiles"
		},
		{
			"Action": [
				"iam:ListServerCertificates",
				"iam:*ServerCertificate"
			],
			"Resource": "*",
			"Effect": "Allow",
			"Sid": "AllowManagingCerts"
		},
		{
			"Action": [
				"iam:*MFADevice",
				"iam:*VirtualMFADevice"
			],
			"Resource": [
				"arn:aws:iam::722424416689:mfa/*",
				"arn:aws:iam::722424416689:user/*"
			],
			"Effect": "Allow",
			"Sid": "AllowMfaDevices"
		},
		{
			"Action": [
				"iam:DeleteUser",
				"iam:CreateRole",
				"iam:DeleteRole",
				"iam:CreateServiceLinkedRole",
				"iam:DeleteServiceLinkedRole",
				"iam:CreateInstanceProfile",
				"iam:DeleteInstanceProfile",
				"iam:AddRoleToInstanceProfile",
				"iam:RemoveRoleFromInstanceProfile",
				"iam:List*Tags",
				"iam:Tag*",
				"iam:Untag*",
				"iam:UpdateRoleDescription"
			],
			"Resource": [
				"arn:aws:iam::722424416689:user/*",
				"arn:aws:iam::722424416689:role/*",
				"arn:aws:iam::722424416689:role/aws-service-role/*",
				"arn:aws:iam::722424416689:instance-profile/*"
			],
			"Effect": "Allow",
			"Sid": "AllowManagingRoles"
		},
		{
			"Action": [
				"iam:ListRoles",
				"iam:PassRole"
			],
			"Resource": [
				"arn:aws:iam::722424416689:role/*",
				"arn:aws:iam::722424416689:role/aws-service-role/*",
				"arn:aws:iam::722424416689:instance-profile/*",
				"arn:aws:iam::722424416689:instance-profile/aws-service-role/*"
			],
			"Effect": "Allow",
			"Sid": "AllowPassingIamRolesToServices"
		},
		{
			"Action": [
				"iam:ListAccountAliases",
				"iam:CreateAccountAlias",
				"iam:DeleteAccountAlias"
			],
			"Resource": "*",
			"Effect": "Allow",
			"Sid": "AllowManagingAccountAlias"
		}
	]
}
```
    
- GITC-OrganizationPermissionsBoundary
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Resource": "*",
			"Effect": "Allow",
			"NotAction": [
				"organizations:LeaveOrganization",
				"organizations:DeclineHandshake",
				"organizations:CreateOrganization",
				"organizations:InviteAccountToOrganization",
				"organizations:RemoveAccountFromOrganization",
				"account:EnableRegion",
				"account:DisableRegion",
				"cloudtrail:StopLogging",
				"config:Delete*",
				"config:StopConfigurationRecorder",
				"guardduty:CreateMembers",
				"guardduty:InviteMembers",
				"guardduty:DisassociateFromMasterAccount",
				"guardduty:DeleteDetector",
				"securityhub:CreateMembers",
				"securityhub:InviteMembers",
				"securityhub:DisassociateFromMasterAccount",
				"securityhub:DisableSecurityHub",
				"securityhub:BatchDisableStandards",
				"securityhub:UpdateStandardsControl",
				"access-analyzer:DeleteAnalyzer",
				"detective:CreateMembers",
				"detective:DisassociateMembership",
				"inspector:Stop*",
				"inspector:Delete*"
			],
			"Sid": "DenyChangesToCoreAwsServices"
		}
	]
}
```
    
- GITC-ServicePermissionsBoundary
    
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Resource": "*",
			"Effect": "Allow",
			"NotAction": [
				"organizations:*",
				"billing:*",
				"cloudtrail:*",
				"guardduty:*",
				"securityhub:*",
				"inspector:*",
				"macie:*",
				"directconnect:*",
				"ec2:*Vpn*",
				"ec2:*CustomerGateway*",
				"ec2:Purchase*",
				"rds:Purchase*",
				"redshift:Purchase*",
				"es:Purchase*",
				"elasticache:Purchase*",
				"dynamodb:Purchase*",
				"aws-marketplace:*",
				"aws-marketplace-management:*"
			],
			"Sid": "AllowLimitedAccessToAwsServices"
		},
		{
			"Action": [
				"iam:*SAMLProvider",
				"iam:*OpenIDConnectProvider"
			],
			"Resource": "*",
			"Effect": "Deny",
			"Sid": "DenyChangesToIdentityProviders"
		},
		{
			"Action": [
				"iam:*AccountPasswordPolicy",
				"iam:*PermissionsBoundary"
			],
			"Resource": "*",
			"Effect": "Deny",
			"Sid": "DenyChangesToOtherIamObjects"
		}
	]
}
```