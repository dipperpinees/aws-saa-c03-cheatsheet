# Conversion Report

Converted on 2026-10-26 from the local Notion HTML export.

## Coverage

| Item | Source | Output handling |
| --- | ---: | --- |
| HTML pages | 98 | Every page has a destination below |
| Root index | 1 | Rebuilt as the repository README |
| Top-level service/topic pages | 74 | Consolidated into 72 folders; duplicate Glue and RAM pages merged |
| Nested pages | 23 | 21 question pages integrated into Notes; two substantive topics kept as child pages |
| Local images | 214 | 13 useful illustrations retained; 201 text screenshots transcribed or consolidated into knowledge |
| Externally hosted illustrations | 3 | Retrieved, stored locally, and retained |
| Published service/topic READMEs | — | 74: 72 main pages and two child pages |
| Published illustrations | — | 16 original PNG/SVG files |

All authored study prose, headings, captions, and navigation are English. Useful original illustrations retain their original pixels, with their meaning explained in English Markdown. Practice questions, choices, and duplicate explanations have been consolidated into factual knowledge. Original HTML and ZIP exports remain on disk and are excluded from Git.

## Conversion method

Structured text, lists, tables, code, and links were extracted from the HTML. Vietnamese prose was translated into English and edited alongside the source. Screenshot text was extracted with English/Vietnamese OCR, then reconciled with written explanations and visible source answer indications. Incorrect distractors were excluded from factual notes. Image-only pages were expanded from their screenshots.

Source page IDs identify their original HTML filenames. Image IDs identify individual extraction records; the page IDs and original image filenames below locate their source context. Original source-language filenames are intentionally not used as output folder names.

## Source scope and limitations

This is a conversion of study notes, not a comprehensive update or verification against current AWS documentation. Pricing examples, quotas, instance-family lists, product availability, and historical exam scenarios retain their source context unless an unambiguous correction was needed to make the conversion intelligible. Historical purchasing examples should be read as source examples rather than current availability claims.

Source contradictions or unresolved interpretation issues identified during editorial review are listed below.

No source page or image remains unprocessed, and no unreadable screenshot or unknown answer selection remains unresolved after OCR and visual review. The following source-quality issues affect how the resulting notes should be interpreted:

| Service or topic | Source issue | Conversion handling |
| --- | --- | --- |
| [EC2](../services/ec2/README.md), [Aurora](../services/aurora/README.md), [RDS](../services/rds/README.md), [SQS](../services/sqs/README.md), and other quota/pricing tables | The export mixes service generations, historical limits, pricing examples, and older feature availability. | Numerical details are retained with source/version context. The notes do not certify current purchasing limits, prices, or availability. |
| [Kinesis](../services/kinesis/README.md) | The same source gives Data Streams record limits of **10 MiB** and **1 MB**. | Both are recorded as a direct source conflict; a current numerical resolution was not invented. |
| [Lambda](../services/lambda/README.md) | The Managed Instances section makes unverified GPU, Graviton4, cold-start, Reserved Instance, and **15% fee** claims. | Preserved in an explicitly source-described section, without presenting them as verified current capabilities. The async payload figure is also labeled historical. |
| [Lambda VPC networking](../services/lambda/README.md) | A screenshot labels a **/27** range as a VPC, while its address arithmetic concerns a subnet. | The label is flagged; the useful subnet arithmetic, **32 total / 27 usable IPv4 addresses**, is preserved. |
| [Amazon EBS](../services/ebs/README.md) | A **1 TB io1 / 64,000 IOPS** scenario conflicts with the source's **50:1 IOPS-to-GiB** rule. | The conflict is stated; LVM aggregation is explained without endorsing that exact volume configuration. |
| [Amazon S3](../services/s3/README.md) | A scenario chooses Standard-IA for millions of **10 KB** files as the most cost-effective option. The source also supplies a **128 KB** minimum billable object size. | Both conditions are retained; the selection is not generalized without accounting for minimum billable size and retrieval costs. The seven-year archive condition remains. |
| [Systems Manager](../services/systems-manager/README.md) | The selected patching runbook is paired with an ALB using **IP-type targets**, without establishing compatibility. | The source workflow and registration-mode caveat are explicit. A Maintenance Window alone is not described as deregistering targets. |
| [Comprehend](../services/comprehend/README.md) | A selected PHI-extraction answer does not implement the scenario's separate continuous S3 object-API monitoring requirement. | The extraction workflow and the unfulfilled audit requirement are distinguished. PHI detection is not claimed to audit S3 activity. |
| [Certificate Manager](../services/certificate-manager/README.md) | Expiration warnings, renewal-state events, metric checks, and certificate-listing APIs are conflated. | Expiration and renewal events are separated; scheduled metric evaluation and certificate detail retrieval are made explicit. The source event identifier/timing remains attributed. |
| [CloudHSM](../services/cloudhsm/README.md) | The source describes zeroization after three invalid administrator logins and an older FIPS certification. | The destructive outcome is retained as a device-generation/source-specific scenario, not universal current behavior. |
| [IAM](../services/iam/README.md) | A federated per-user-prefix example assumes `${aws:username}` is available to assumed-role sessions. | The context-key limitation is explicit; the source variable is not presented as a working universal federation policy. A permissions boundary is also distinguished from policy-attachment controls. |
| [KMS and S3 encryption](../services/kms/README.md) | Annual SSE-S3 key rotation is treated as a configurable customer guarantee, and key-use auditing is confused with object-access auditing. | The annual statement is attributed to the source, and customer controls, KMS key auditing, and S3 object-API auditing are separated. |
| [System Design](../services/system-design/README.md) | A selected answer says App Runner containerizes an existing Windows application; another proposes a legacy SCT/Snowball/DMS MongoDB migration. | Managed deployment is separated from containerization/runtime migration. The offline migration remains a historical workflow requiring supported-engine and availability checks. |
| [VPC](../services/vpc/README.md) | Prose requires peering before PrivateLink, while the screenshot presents them as alternatives; a Direct Connect example mistakes creating a gateway for enabling encryption. | Peering and PrivateLink are explained as alternatives. Encryption requires an actual configured IPsec VPN; creating a gateway alone is insufficient. |
| [DynamoDB](../services/dynamodb/README.md) | A blanket cross-Region strong-consistency restriction conflicts with the same source's MRSC section. | The restriction is scoped to eventual global-table replication; MRSC is explicitly distinguished. Historical transaction/table limits remain labeled. |
| [RDS](../services/rds/README.md) and [Aurora](../services/aurora/README.md) | Traditional Multi-AZ standby, readable replicas/clusters, cross-Region recovery, and older Aurora modes are mixed together. | Regional availability, readable replicas, and cross-Region recovery are separated; older mode/version claims are labeled. Recovery examples are not guarantees. |
| [Data Firehose](../services/data-firehose/README.md) | One embedded diagram actually shows Data Streams consumers; prose confuses Lambda transformation with Firehose delivery destinations. | The diagram is accurately labeled and described. Lambda's transformation-hook role is explicitly distinguished from a Data Streams consumer. |
| [QLDB](../services/qldb/README.md), [Pinpoint](../services/pinpoint/README.md), [Snowball](../services/snowball/README.md), and legacy service examples | The source does not consistently specify lifecycle or present-day eligibility. | Descriptions retain source context; availability for new deployments is not asserted. |

Corrections to clear translation errors and unambiguous scope mistakes are incorporated directly, including incorrect answer polarity, networking direction, policy effects, and service-role distinctions. This is editorial reconciliation of the supplied notes, not a substitute for a current AWS service audit.

### Source attribution and image language

The IAM source explicitly credits **[Tutorials Dojo](https://tutorialsdojo.com/)**; that attribution is retained in the IAM notes. The three external illustration URLs below also identify their original Tutorials Dojo sources. Other local illustrations retain the context provided by the export; no additional ownership attribution was invented.

The two retained SVG illustrations contain original source-language labels. Their substantive meanings and comparisons are available in English Markdown beside the images. Original image bytes were deliberately preserved, as agreed in the design.


## Page destinations

A question-page destination means its unique knowledge was integrated into that service's main text or Notes; it does not mean the quiz was retained. The two AWS Glue pages and the RAM/AWS RAM pages share their service destination.

| Source page ID | Service or topic | Source role | Destination |
| --- | --- | --- | --- |
| `3391f8ae654080569f06f7e1c81de6b3` | Active Directory | Service/topic page | [Read notes](../services/active-directory/README.md) |
| `3391f8ae65408043ba6ecaee84b87183` | Active Directory | Question insights → Notes | [Read notes](../services/active-directory/README.md) |
| `3391f8ae654080098cb9ce7c9d3443e8` | Amazon API Gateway | Service/topic page | [Read notes](../services/api-gateway/README.md) |
| `3391f8ae654080bcb945e95f0909ab90` | Amazon API Gateway | Question insights → Notes | [Read notes](../services/api-gateway/README.md) |
| `3571f8ae654080bca4c7f90ae5540ddf` | Amazon Data Lifecycle Manager | Service/topic page | [Read notes](../services/data-lifecycle-manager/README.md) |
| `3521f8ae6540808e8c9ac45ed34e8f21` | Amazon ElastiCache | Service/topic page | [Read notes](../services/elasticache/README.md) |
| `33b1f8ae65408058a9f2c543e3acf2ee` | Amazon EMR | Service/topic page | [Read notes](../services/emr/README.md) |
| `33e1f8ae654080f1badec3d701ac0d77` | Amazon GuardDuty | Service/topic page | [Read notes](../services/guardduty/README.md) |
| `3581f8ae6540801a94f7ffa93432b84b` | Amazon Keyspaces | Service/topic page | [Read notes](../services/keyspaces/README.md) |
| `3551f8ae654080caa75ee53624a8a15c` | Amazon Quantum Ledger Database | Service/topic page | [Read notes](../services/qldb/README.md) |
| `33e1f8ae654080798e6bcb7b97a3e7d5` | Amazon Rekognition | Service/topic page | [Read notes](../services/rekognition/README.md) |
| `33d1f8ae65408051a60fcbc4fe3e7b50` | Amazon Route 53 | Service/topic page | [Read notes](../services/route-53/README.md) |
| `33d1f8ae6540806fb04ddcb0928ae0db` | Amazon Route 53 | Question insights → Notes | [Read notes](../services/route-53/README.md) |
| `3461f8ae654080149de8e328c62cb30c` | Application Load Balancer | Service/topic page | [Read notes](../services/alb/README.md) |
| `3531f8ae65408016b140dfcd216dc375` | Athena | Service/topic page | [Read notes](../services/athena/README.md) |
| `3391f8ae654080a88887db2a938015a1` | Aurora | Service/topic page | [Read notes](../services/aurora/README.md) |
| `3391f8ae654080d9a063f8decfde1265` | Aurora | Question insights → Notes | [Read notes](../services/aurora/README.md) |
| `3521f8ae65408015897bcf696d8634ec` | Aurora: Multi-AZ architecture | Nested topic | [Read notes](../services/aurora/aurora-multi-az/README.md) |
| `3541f8ae654080e5b846e9fb2099241d` | AWS Amplify | Service/topic page | [Read notes](../services/amplify/README.md) |
| `3421f8ae65408015a5ccd40f9dae9f8e` | AWS AppSync | Service/topic page | [Read notes](../services/appsync/README.md) |
| `3391f8ae65408047a6a0c90dec9390cb` | AWS Artifact | Service/topic page | [Read notes](../services/artifact/README.md) |
| `34f1f8ae654080c38ab0c2ac1b82b4ef` | AWS Batch | Service/topic page | [Read notes](../services/batch/README.md) |
| `3521f8ae65408074bca1d809ad4fe5f5` | AWS Certificate Manager | Service/topic page | [Read notes](../services/certificate-manager/README.md) |
| `3411f8ae654080f490edd367ddad5fb4` | AWS Config | Service/topic page | [Read notes](../services/config/README.md) |
| `3411f8ae654080aa9b46c5102f75069c` | AWS Config | Question insights → Notes | [Read notes](../services/config/README.md) |
| `3471f8ae6540804d9951c6b585dea833` | AWS DataSync | Service/topic page | [Read notes](../services/datasync/README.md) |
| `33b1f8ae654080c984afee5408c3ae23` | AWS Glue | Service/topic page | [Read notes](../services/glue/README.md) |
| `3421f8ae654080e4bf93ccf84f278965` | AWS Glue | Service/topic page | [Read notes](../services/glue/README.md) |
| `3471f8ae6540802e8794fb41df804e84` | AWS Health | Service/topic page | [Read notes](../services/health/README.md) |
| `3461f8ae654080b68b61f4d2efc39ac4` | AWS IAM Identity Center | Service/topic page | [Read notes](../services/iam-identity-center/README.md) |
| `3391f8ae6540801695bdfa4c161dbfda` | AWS Lake Formation | Service/topic page | [Read notes](../services/lake-formation/README.md) |
| `3461f8ae6540805c9be8fb4ea60e3675` | AWS Migration Service | Service/topic page | [Read notes](../services/migration-service/README.md) |
| `33f1f8ae6540806c8e1df900b0703d50` | AWS Organizations | Service/topic page | [Read notes](../services/organizations/README.md) |
| `33f1f8ae654080d095b3d4230d63ac33` | AWS Organizations | Question insights → Notes | [Read notes](../services/organizations/README.md) |
| `3571f8ae654080caa784e43e30007f08` | AWS Private 5G and AWS Wavelength | Service/topic page | [Read notes](../services/private-5g-and-wavelength/README.md) |
| `3391f8ae654080dca4a0f1faf6c42d2a` | AWS Resource Access Manager | Service/topic page | [Read notes](../services/resource-access-manager/README.md) |
| `3501f8ae6540806f8f8fe2b2e12d46d9` | AWS Resource Access Manager | Service/topic page | [Read notes](../services/resource-access-manager/README.md) |
| `3391f8ae654080e39993d658eb7cf787` | AWS Resource Access Manager | Question insights → Notes | [Read notes](../services/resource-access-manager/README.md) |
| `3411f8ae65408068bb54d9fc77881487` | AWS Secrets Manager | Service/topic page | [Read notes](../services/secrets-manager/README.md) |
| `33e1f8ae654080e49eb7ee29e6f610c4` | AWS Security Token Service | Service/topic page | [Read notes](../services/security-token-service/README.md) |
| `3391f8ae6540807cbcdcd6892d2be941` | AWS Solutions Architect Cheat Sheet | Root index | [Read notes](../README.md) |
| `34f1f8ae654080b0bbbedef18ec817ea` | AWS Systems Manager | Service/topic page | [Read notes](../services/systems-manager/README.md) |
| `35a1f8ae654080d8825fd20b51dde7ee` | AWS Transfer Family | Service/topic page | [Read notes](../services/transfer-family/README.md) |
| `3411f8ae654080c3afa5e73b6e3bd214` | CloudFormation | Service/topic page | [Read notes](../services/cloudformation/README.md) |
| `3411f8ae65408082b8f6c7b27a01027e` | CloudFormation | Question insights → Notes | [Read notes](../services/cloudformation/README.md) |
| `3391f8ae65408061a4e7d8a50310f79b` | CloudFront | Service/topic page | [Read notes](../services/cloudfront/README.md) |
| `34f1f8ae654080cd9e4ef20aed95516b` | CloudHSM | Service/topic page | [Read notes](../services/cloudhsm/README.md) |
| `3391f8ae654080fbbc4af2d7a6936801` | CloudTrail | Service/topic page | [Read notes](../services/cloudtrail/README.md) |
| `3391f8ae654080809b08f6ff63c8aef3` | CloudWatch | Service/topic page | [Read notes](../services/cloudwatch/README.md) |
| `3391f8ae654080a7964dedda641cf971` | CloudWatch | Question insights → Notes | [Read notes](../services/cloudwatch/README.md) |
| `3471f8ae654080a085efdeb4b5541970` | Comprehend | Service/topic page | [Read notes](../services/comprehend/README.md) |
| `3391f8ae65408016a0d7c62152342cdf` | Control Tower | Service/topic page | [Read notes](../services/control-tower/README.md) |
| `3391f8ae65408088b289d4e1ddc9bfc5` | Control Tower | Question insights → Notes | [Read notes](../services/control-tower/README.md) |
| `3421f8ae654080db9592ecb3bb24f78f` | Cost Management Services | Service/topic page | [Read notes](../services/cost-management/README.md) |
| `3411f8ae6540805aaec7e9f04d5a5a77` | Data Firehose | Service/topic page | [Read notes](../services/data-firehose/README.md) |
| `3411f8ae6540803db2a3d86101c6538d` | Data Firehose | Question insights → Notes | [Read notes](../services/data-firehose/README.md) |
| `33b1f8ae65408040b91bf47bc39cac30` | Database Migration Service | Service/topic page | [Read notes](../services/database-migration-service/README.md) |
| `3411f8ae65408074a5aae19570318577` | DDoS | Service/topic page | [Read notes](../services/ddos/README.md) |
| `3581f8ae6540808fb23ff9f9e0e38fce` | Decoupling | Service/topic page | [Read notes](../services/decoupling/README.md) |
| `3531f8ae654080669924d87d4f569145` | Disaster Recovery | Service/topic page | [Read notes](../services/disaster-recovery/README.md) |
| `3391f8ae65408065abb2c4d9158e4cf3` | DynamoDB | Service/topic page | [Read notes](../services/dynamodb/README.md) |
| `3391f8ae65408049b6a7f81acbc83efa` | DynamoDB | Question insights → Notes | [Read notes](../services/dynamodb/README.md) |
| `33c1f8ae6540804797bbc8d8dab62f36` | EBS | Service/topic page | [Read notes](../services/ebs/README.md) |
| `33c1f8ae654080c9b0f3ca8ae24bae24` | EBS | Question insights → Notes | [Read notes](../services/ebs/README.md) |
| `3411f8ae65408095a167c628c200acfb` | EC2 | Service/topic page | [Read notes](../services/ec2/README.md) |
| `3391f8ae6540800bad36d374b1dc18c4` | EC2 Auto Scaling | Service/topic page | [Read notes](../services/auto-scaling/README.md) |
| `3391f8ae654080e0a5faeca10bb6fee3` | EC2 Auto Scaling | Question insights → Notes | [Read notes](../services/auto-scaling/README.md) |
| `34b1f8ae65408054a1d3d0fb5d82e2c4` | ECS | Service/topic page | [Read notes](../services/ecs/README.md) |
| `3481f8ae654080fe8d6cc89c9c332b02` | EFA | Service/topic page | [Read notes](../services/efa/README.md) |
| `3391f8ae6540800fb327e02243150390` | EFS | Service/topic page | [Read notes](../services/efs/README.md) |
| `3391f8ae6540805595d6d65bdb62e0d2` | EFS | Question insights → Notes | [Read notes](../services/efs/README.md) |
| `34f1f8ae654080b79165d087a30ae674` | EKS | Service/topic page | [Read notes](../services/eks/README.md) |
| `3391f8ae6540805c8da1cdec4322a2d2` | FSx | Service/topic page | [Read notes](../services/fsx/README.md) |
| `3391f8ae654080918701f46ae8633c57` | FSx | Question insights → Notes | [Read notes](../services/fsx/README.md) |
| `3391f8ae654080408411f8a18de08f66` | IAM | Service/topic page | [Read notes](../services/iam/README.md) |
| `3391f8ae654080dc89b1f2816152a971` | IAM | Question insights → Notes | [Read notes](../services/iam/README.md) |
| `34e1f8ae6540806d8140c13ec7c61454` | Kinesis | Service/topic page | [Read notes](../services/kinesis/README.md) |
| `33f1f8ae6540808fa0a8de5a84b915cd` | KMS | Service/topic page | [Read notes](../services/kms/README.md) |
| `3391f8ae654080ae9282c39a56d75012` | Lambda | Service/topic page | [Read notes](../services/lambda/README.md) |
| `3391f8ae65408016baf0e08c74860ed6` | Lambda | Question insights → Notes | [Read notes](../services/lambda/README.md) |
| `34f1f8ae6540801e8f47e24c661eb8b0` | NACL | Service/topic page | [Read notes](../services/nacl/README.md) |
| `3521f8ae6540803abe46ebe01373f4bb` | Pinpoint | Service/topic page | [Read notes](../services/pinpoint/README.md) |
| `3391f8ae654080559e9dc33b97ca9fce` | RDS | Service/topic page | [Read notes](../services/rds/README.md) |
| `3391f8ae6540809db42fccba59aa32bb` | RDS | Question insights → Notes | [Read notes](../services/rds/README.md) |
| `3481f8ae654080f086f5d6b9e5ad13f5` | Redshift | Service/topic page | [Read notes](../services/redshift/README.md) |
| `3391f8ae654080c39e74d40ae205da9a` | S3 | Service/topic page | [Read notes](../services/s3/README.md) |
| `3391f8ae65408058ba58dc2a23fd344d` | S3 | Question insights → Notes | [Read notes](../services/s3/README.md) |
| `3541f8ae65408035966be04d0c3a93f6` | Security | Service/topic page | [Read notes](../services/security/README.md) |
| `3391f8ae6540801f8061ee4d364b08d1` | Security Hub | Service/topic page | [Read notes](../services/security-hub/README.md) |
| `3391f8ae65408025a526ffe5a7118fba` | Security Services | Service/topic page | [Read notes](../services/security-services/README.md) |
| `3541f8ae65408042b063fe1c9ce235f5` | Snowball | Service/topic page | [Read notes](../services/snowball/README.md) |
| `33e1f8ae654080d7a05be40d6038e74a` | SQS | Service/topic page | [Read notes](../services/sqs/README.md) |
| `34f1f8ae6540801b8580fb485d05ee3e` | Storage Gateway | Service/topic page | [Read notes](../services/storage-gateway/README.md) |
| `3551f8ae65408059967dc034c4671374` | System Design | Service/topic page | [Read notes](../services/system-design/README.md) |
| `3421f8ae6540808a9a23fe0262a7c587` | TLS Certificates | Service/topic page | [Read notes](../services/tls-certificates/README.md) |
| `3391f8ae654080de8d53e3b946aa5376` | VPC | Service/topic page | [Read notes](../services/vpc/README.md) |
| `3391f8ae6540802face2df644c7ac30a` | VPC | Question insights → Notes | [Read notes](../services/vpc/README.md) |
| `3411f8ae6540801697fbc1b660de24f2` | VPC: Public and private subnets | Nested topic | [Read notes](../services/vpc/public-and-private-subnets/README.md) |

## Retained illustrations

Original PNG and SVG bytes are preserved. Each illustration is embedded in its destination page with an English description or explanation. Third-party content remains from the source study materials; no ownership or license transfer is implied by conversion.

| Image ID | Illustration | Source page ID | Published asset |
| --- | --- | --- | --- |
| `0cda98210ed7` | AppSync pipeline resolver execution | `3421f8ae65408015a5ccd40f9dae9f8e` | [View image](../services/appsync/assets/appsync-pipeline-resolver.png) |
| `8e6a877c1c45` | Comprehend text analysis and search architecture | `3471f8ae654080a085efdeb4b5541970` | [View image](../services/comprehend/assets/comprehend-search-architecture.png) |
| `9d811725c9e9` | Kinesis Data Streams consumers | `3411f8ae6540803db2a3d86101c6538d` | [View image](../services/data-firehose/assets/kinesis-stream-consumers.png) |
| `7d975ff2d20b` | Amazon Data Firehose processing pipeline | `3411f8ae6540805aaec7e9f04d5a5a77` | [View image](../services/data-firehose/assets/data-firehose-processing.png) |
| `e0acb1f8a8b8` | Recovery point and recovery time objectives | `3531f8ae654080669924d87d4f569145` | [View image](../services/disaster-recovery/assets/recovery-objectives.png) |
| `029e89a3da3b` | EC2 placement groups | `3411f8ae65408095a167c628c200acfb` | [View image](../services/ec2/assets/ec2-placement-groups.svg) |
| `a392b5fa2301` | Redis cluster mode comparison | `3521f8ae6540808e8c9ac45ed34e8f21` | [View image](../services/elasticache/assets/redis-cluster-modes.svg) |
| `0ed9a7af20e1` | Enterprise identity federation with temporary AWS credentials | `3391f8ae654080dc89b1f2816152a971` | [View image](../services/iam/assets/identity-federation.png) |
| `760a67f5d0bf` | Public, private, and VPN subnet routing | `3391f8ae6540802face2df644c7ac30a` | [View image](../services/vpc/assets/subnet-routing.png) |
| `7cadf552c673` | Transit Gateway and Direct Connect architecture | `3391f8ae6540802face2df644c7ac30a` | [View image](../services/vpc/assets/transit-gateway-direct-connect.png) |
| `fcceb722419c` | Private service connectivity between VPCs | `3391f8ae6540802face2df644c7ac30a` | [View image](../services/vpc/assets/private-service-connectivity.png) |
| `e94ffd184395` | Site-to-Site VPN architecture | `3391f8ae6540802face2df644c7ac30a` | [View image](../services/vpc/assets/site-to-site-vpn.png) |
| `85f5f4d0e1d3` | VPC route tables and hybrid connectivity | `3391f8ae654080de8d53e3b946aa5376` | [View image](../services/vpc/assets/vpc-route-tables.png) |
| `c1ca233b204a` | DNS resolution and DNS hostname settings | `33d1f8ae6540806fb04ddcb0928ae0db` | [View image](../services/route-53/assets/dns-resolution-and-hostnames.png) |
| `7865310c0064` | Amazon VPC architecture | `3391f8ae654080de8d53e3b946aa5376` | [View image](../services/vpc/assets/vpc-architecture-1.jpg) |
| `8203c3bc4797` | Amazon VPC architecture | `3391f8ae654080de8d53e3b946aa5376` | [View image](../services/vpc/assets/vpc-architecture-2.jpg) |

### External illustration sources

These images were already referenced by the HTML export and were retrieved to make the repository self-contained.

- [DNS resolution and DNS hostname settings](https://media.tutorialsdojo.com/public/DrewDennis_DNSresolutionDNShostnames.png) → [local copy](../services/route-53/assets/dns-resolution-and-hostnames.png).
- [Amazon VPC architecture](https://td-mainsite-cdn.tutorialsdojo.com/wp-content/uploads/2018/12/AWSTrainingAmazonVPC1.jpg) → [local copy](../services/vpc/assets/vpc-architecture-1.jpg).
- [Amazon VPC architecture](https://td-mainsite-cdn.tutorialsdojo.com/wp-content/uploads/2018/12/AWSTrainingAmazonVPC2.jpg) → [local copy](../services/vpc/assets/vpc-architecture-2.jpg).

## Text screenshots converted into knowledge

These 201 local images are intentionally omitted from published assets because their useful contents are represented in English notes, tables, or scenario guidance. Duplicate knowledge was kept once. Incorrect answer choices and decorative quiz UI were excluded. Source page IDs provide the original context for each record.

| Image ID | Original image filename | Source page ID | Knowledge destination |
| --- | --- | --- | --- |
| `a58de4033845` | `image 1.png` | `3391f8ae65408043ba6ecaee84b87183` | [Read notes](../services/active-directory/README.md) |
| `7090ab0a9414` | `image.png` | `3391f8ae65408043ba6ecaee84b87183` | [Read notes](../services/active-directory/README.md) |
| `2fab5f5dc362` | `image 1.png` | `3461f8ae654080149de8e328c62cb30c` | [Read notes](../services/alb/README.md) |
| `cdc379df9111` | `image 2.png` | `3461f8ae654080149de8e328c62cb30c` | [Read notes](../services/alb/README.md) |
| `b7b706580fb4` | `image 3.png` | `3461f8ae654080149de8e328c62cb30c` | [Read notes](../services/alb/README.md) |
| `244b63cd5b35` | `image.png` | `3461f8ae654080149de8e328c62cb30c` | [Read notes](../services/alb/README.md) |
| `38681d2dd075` | `image 1.png` | `3391f8ae654080bcb945e95f0909ab90` | [Read notes](../services/api-gateway/README.md) |
| `80c326deefba` | `image 2.png` | `3391f8ae654080bcb945e95f0909ab90` | [Read notes](../services/api-gateway/README.md) |
| `954ded37af23` | `image 3.png` | `3391f8ae654080bcb945e95f0909ab90` | [Read notes](../services/api-gateway/README.md) |
| `17dca53db0a6` | `image.png` | `3391f8ae654080bcb945e95f0909ab90` | [Read notes](../services/api-gateway/README.md) |
| `a45c1d4bca71` | `image 1.png` | `3391f8ae654080e0a5faeca10bb6fee3` | [Read notes](../services/auto-scaling/README.md) |
| `6afa6cf302f8` | `image 2.png` | `3391f8ae654080e0a5faeca10bb6fee3` | [Read notes](../services/auto-scaling/README.md) |
| `bf2f8b37bb17` | `image 3.png` | `3391f8ae654080e0a5faeca10bb6fee3` | [Read notes](../services/auto-scaling/README.md) |
| `8c343a93c377` | `image 4.png` | `3391f8ae654080e0a5faeca10bb6fee3` | [Read notes](../services/auto-scaling/README.md) |
| `c78f5a562874` | `image.png` | `3391f8ae654080e0a5faeca10bb6fee3` | [Read notes](../services/auto-scaling/README.md) |
| `3d91dab07ee2` | `image.png` | `3391f8ae65408047a6a0c90dec9390cb` | [Read notes](../services/artifact/README.md) |
| `cf172ebcf4da` | `image 1.png` | `34f1f8ae654080c38ab0c2ac1b82b4ef` | [Read notes](../services/batch/README.md) |
| `1bca10f0c316` | `image.png` | `34f1f8ae654080c38ab0c2ac1b82b4ef` | [Read notes](../services/batch/README.md) |
| `a4f433950dfe` | `image.png` | `3521f8ae65408074bca1d809ad4fe5f5` | [Read notes](../services/certificate-manager/README.md) |
| `3f1f3cae4faa` | `image 1.png` | `3411f8ae654080aa9b46c5102f75069c` | [Read notes](../services/config/README.md) |
| `7647a53fb27b` | `image 2.png` | `3411f8ae654080aa9b46c5102f75069c` | [Read notes](../services/config/README.md) |
| `bb3b66900c01` | `image 3.png` | `3411f8ae654080aa9b46c5102f75069c` | [Read notes](../services/config/README.md) |
| `8b0873351215` | `image 4.png` | `3411f8ae654080aa9b46c5102f75069c` | [Read notes](../services/config/README.md) |
| `9d958324542a` | `image.png` | `3411f8ae654080aa9b46c5102f75069c` | [Read notes](../services/config/README.md) |
| `77fb6156ec54` | `image.png` | `3471f8ae6540804d9951c6b585dea833` | [Read notes](../services/datasync/README.md) |
| `29be7b3f9871` | `image.png` | `33b1f8ae654080c984afee5408c3ae23` | [Read notes](../services/glue/README.md) |
| `4dbdb7ece6ba` | `image.png` | `3421f8ae654080e4bf93ccf84f278965` | [Read notes](../services/glue/README.md) |
| `2456826e0302` | `image.png` | `3471f8ae6540802e8794fb41df804e84` | [Read notes](../services/health/README.md) |
| `33bab892f75c` | `image.png` | `3461f8ae654080b68b61f4d2efc39ac4` | [Read notes](../services/iam-identity-center/README.md) |
| `e0093e12c94c` | `image.png` | `3461f8ae6540805c9be8fb4ea60e3675` | [Read notes](../services/migration-service/README.md) |
| `1067ea25fea2` | `image 1.png` | `34f1f8ae654080b0bbbedef18ec817ea` | [Read notes](../services/systems-manager/README.md) |
| `305d7b36a7e7` | `image.png` | `34f1f8ae654080b0bbbedef18ec817ea` | [Read notes](../services/systems-manager/README.md) |
| `c77231da8477` | `image.png` | `33b1f8ae65408058a9f2c543e3acf2ee` | [Read notes](../services/emr/README.md) |
| `31b676f387c5` | `image 1.png` | `33e1f8ae654080f1badec3d701ac0d77` | [Read notes](../services/guardduty/README.md) |
| `dc1760f3a7c7` | `image.png` | `33e1f8ae654080f1badec3d701ac0d77` | [Read notes](../services/guardduty/README.md) |
| `dfc3ecaff04c` | `image.png` | `3421f8ae65408015a5ccd40f9dae9f8e` | [Read notes](../services/appsync/README.md) |
| `a984f823e6ba` | `image.png` | `3531f8ae65408016b140dfcd216dc375` | [Read notes](../services/athena/README.md) |
| `8ccd38aab553` | `image 1.png` | `3391f8ae654080d9a063f8decfde1265` | [Read notes](../services/aurora/README.md) |
| `8bccf8356547` | `image 2.png` | `3391f8ae654080d9a063f8decfde1265` | [Read notes](../services/aurora/README.md) |
| `9847db81c880` | `image 3.png` | `3391f8ae654080d9a063f8decfde1265` | [Read notes](../services/aurora/README.md) |
| `223684ea70ca` | `image 4.png` | `3391f8ae654080d9a063f8decfde1265` | [Read notes](../services/aurora/README.md) |
| `3914266c7f82` | `image 5.png` | `3391f8ae654080d9a063f8decfde1265` | [Read notes](../services/aurora/README.md) |
| `6b508eb1ada6` | `image 6.png` | `3391f8ae654080d9a063f8decfde1265` | [Read notes](../services/aurora/README.md) |
| `fd9329e726c1` | `image.png` | `3391f8ae654080d9a063f8decfde1265` | [Read notes](../services/aurora/README.md) |
| `1ce26576b69b` | `image.png` | `3421f8ae654080db9592ecb3bb24f78f` | [Read notes](../services/cost-management/README.md) |
| `3bb7cfe01d61` | `image 1.png` | `3411f8ae65408082b8f6c7b27a01027e` | [Read notes](../services/cloudformation/README.md) |
| `720b2b8f046b` | `image.png` | `3411f8ae65408082b8f6c7b27a01027e` | [Read notes](../services/cloudformation/README.md) |
| `ee4a8fa1a7a7` | `image 1.png` | `3391f8ae65408061a4e7d8a50310f79b` | [Read notes](../services/cloudfront/README.md) |
| `f93ab9b968f2` | `image 2.png` | `3391f8ae65408061a4e7d8a50310f79b` | [Read notes](../services/cloudfront/README.md) |
| `419ee3c88d57` | `image 3.png` | `3391f8ae65408061a4e7d8a50310f79b` | [Read notes](../services/cloudfront/README.md) |
| `c2a58b3d767e` | `image 4.png` | `3391f8ae65408061a4e7d8a50310f79b` | [Read notes](../services/cloudfront/README.md) |
| `56f18018a4ec` | `image 5.png` | `3391f8ae65408061a4e7d8a50310f79b` | [Read notes](../services/cloudfront/README.md) |
| `95e0b51217e7` | `image.png` | `3391f8ae65408061a4e7d8a50310f79b` | [Read notes](../services/cloudfront/README.md) |
| `b2fd378da87f` | `image.png` | `34f1f8ae654080cd9e4ef20aed95516b` | [Read notes](../services/cloudhsm/README.md) |
| `23ff092107cd` | `image 1.png` | `3391f8ae654080a7964dedda641cf971` | [Read notes](../services/cloudwatch/README.md) |
| `5d859d7e618d` | `image 2.png` | `3391f8ae654080a7964dedda641cf971` | [Read notes](../services/cloudwatch/README.md) |
| `994b9e424b37` | `image 3.png` | `3391f8ae654080a7964dedda641cf971` | [Read notes](../services/cloudwatch/README.md) |
| `ed13338397d8` | `image 4.png` | `3391f8ae654080a7964dedda641cf971` | [Read notes](../services/cloudwatch/README.md) |
| `ddaca6c0a20d` | `image 5.png` | `3391f8ae654080a7964dedda641cf971` | [Read notes](../services/cloudwatch/README.md) |
| `1054b0351654` | `image.png` | `3391f8ae654080a7964dedda641cf971` | [Read notes](../services/cloudwatch/README.md) |
| `839811e757bc` | `image 1.png` | `3471f8ae654080a085efdeb4b5541970` | [Read notes](../services/comprehend/README.md) |
| `7918f68cbea8` | `image.png` | `3471f8ae654080a085efdeb4b5541970` | [Read notes](../services/comprehend/README.md) |
| `6cd0b9814f24` | `image.png` | `3391f8ae65408088b289d4e1ddc9bfc5` | [Read notes](../services/control-tower/README.md) |
| `b476ca99af2f` | `image.png` | `3411f8ae65408074a5aae19570318577` | [Read notes](../services/ddos/README.md) |
| `37a464adc646` | `image 1.png` | `3411f8ae6540803db2a3d86101c6538d` | [Read notes](../services/data-firehose/README.md) |
| `313147ba4c13` | `image 3.png` | `3411f8ae6540803db2a3d86101c6538d` | [Read notes](../services/data-firehose/README.md) |
| `2d0431ead95a` | `image.png` | `3411f8ae6540803db2a3d86101c6538d` | [Read notes](../services/data-firehose/README.md) |
| `a89091266bc3` | `image 1.png` | `33b1f8ae65408040b91bf47bc39cac30` | [Read notes](../services/database-migration-service/README.md) |
| `a6ffa32683cf` | `image.png` | `33b1f8ae65408040b91bf47bc39cac30` | [Read notes](../services/database-migration-service/README.md) |
| `a7a86cd556ca` | `image 1.png` | `3531f8ae654080669924d87d4f569145` | [Read notes](../services/disaster-recovery/README.md) |
| `400f2568bcf4` | `image 1.png` | `3391f8ae65408049b6a7f81acbc83efa` | [Read notes](../services/dynamodb/README.md) |
| `6030176b76f0` | `image 2.png` | `3391f8ae65408049b6a7f81acbc83efa` | [Read notes](../services/dynamodb/README.md) |
| `360075121a88` | `image 3.png` | `3391f8ae65408049b6a7f81acbc83efa` | [Read notes](../services/dynamodb/README.md) |
| `adf5f93a9f76` | `image.png` | `3391f8ae65408049b6a7f81acbc83efa` | [Read notes](../services/dynamodb/README.md) |
| `d091e5009fca` | `image 1.png` | `33c1f8ae654080c9b0f3ca8ae24bae24` | [Read notes](../services/ebs/README.md) |
| `2befc01b7503` | `image 2.png` | `33c1f8ae654080c9b0f3ca8ae24bae24` | [Read notes](../services/ebs/README.md) |
| `259900cdfe36` | `image 3.png` | `33c1f8ae654080c9b0f3ca8ae24bae24` | [Read notes](../services/ebs/README.md) |
| `29106311d8f3` | `image 4.png` | `33c1f8ae654080c9b0f3ca8ae24bae24` | [Read notes](../services/ebs/README.md) |
| `b9398c8d8f47` | `image.png` | `33c1f8ae654080c9b0f3ca8ae24bae24` | [Read notes](../services/ebs/README.md) |
| `921a1f184b48` | `image 1.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `569a1863632e` | `image 10.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `4b57df6b7cbe` | `image 11.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `9ac5deef6492` | `image 12.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `53bce9704dd4` | `image 13.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `9a6524aee086` | `image 14.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `fa2d6e849dcf` | `image 15.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `a56c065dc8b9` | `image 2.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `f46a88ed3223` | `image 3.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `12a0818842a2` | `image 4.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `8d15ca49da8a` | `image 5.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `015a4ae4c729` | `image 6.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `03b34f2950f7` | `image 7.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `f7390ff31767` | `image 8.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `b9c792c9d039` | `image 9.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `90c7b1ffea88` | `image.png` | `3411f8ae65408095a167c628c200acfb` | [Read notes](../services/ec2/README.md) |
| `0052a83dcd01` | `image.png` | `34b1f8ae65408054a1d3d0fb5d82e2c4` | [Read notes](../services/ecs/README.md) |
| `822d090863f3` | `image 1.png` | `3481f8ae654080fe8d6cc89c9c332b02` | [Read notes](../services/efa/README.md) |
| `579774b8f30f` | `image.png` | `3481f8ae654080fe8d6cc89c9c332b02` | [Read notes](../services/efa/README.md) |
| `9eedebb4d792` | `image 1.png` | `3391f8ae6540805595d6d65bdb62e0d2` | [Read notes](../services/efs/README.md) |
| `62545b4a8723` | `image.png` | `3391f8ae6540805595d6d65bdb62e0d2` | [Read notes](../services/efs/README.md) |
| `acdf209ebfca` | `image 1.png` | `34f1f8ae654080b79165d087a30ae674` | [Read notes](../services/eks/README.md) |
| `d479f1ebb334` | `image 2.png` | `34f1f8ae654080b79165d087a30ae674` | [Read notes](../services/eks/README.md) |
| `892531b7d0f5` | `image 3.png` | `34f1f8ae654080b79165d087a30ae674` | [Read notes](../services/eks/README.md) |
| `37c3e280a93b` | `image.png` | `34f1f8ae654080b79165d087a30ae674` | [Read notes](../services/eks/README.md) |
| `d5c06e5516e8` | `image.png` | `3521f8ae6540808e8c9ac45ed34e8f21` | [Read notes](../services/elasticache/README.md) |
| `7e86465cdbf4` | `image 1.png` | `3391f8ae654080918701f46ae8633c57` | [Read notes](../services/fsx/README.md) |
| `e0dea086897c` | `image 2.png` | `3391f8ae654080918701f46ae8633c57` | [Read notes](../services/fsx/README.md) |
| `6f2bc7973af8` | `image.png` | `3391f8ae654080918701f46ae8633c57` | [Read notes](../services/fsx/README.md) |
| `75cb207ea426` | `image 2.png` | `3391f8ae654080dc89b1f2816152a971` | [Read notes](../services/iam/README.md) |
| `a09a6ce4fac1` | `image 3.png` | `3391f8ae654080dc89b1f2816152a971` | [Read notes](../services/iam/README.md) |
| `374b02e86f80` | `image 4.png` | `3391f8ae654080dc89b1f2816152a971` | [Read notes](../services/iam/README.md) |
| `1edf285b4924` | `image 5.png` | `3391f8ae654080dc89b1f2816152a971` | [Read notes](../services/iam/README.md) |
| `b5e7bc5018da` | `image.png` | `3391f8ae654080dc89b1f2816152a971` | [Read notes](../services/iam/README.md) |
| `219b441a24f7` | `image 1.png` | `33f1f8ae6540808fa0a8de5a84b915cd` | [Read notes](../services/kms/README.md) |
| `920afdb61a34` | `image 2.png` | `33f1f8ae6540808fa0a8de5a84b915cd` | [Read notes](../services/kms/README.md) |
| `18ecdb591773` | `image.png` | `33f1f8ae6540808fa0a8de5a84b915cd` | [Read notes](../services/kms/README.md) |
| `c33a7f318a07` | `image.png` | `34e1f8ae6540806d8140c13ec7c61454` | [Read notes](../services/kinesis/README.md) |
| `fc82414bb183` | `image.png` | `3391f8ae6540801695bdfa4c161dbfda` | [Read notes](../services/lake-formation/README.md) |
| `90c3035b20b4` | `image 1.png` | `3391f8ae65408016baf0e08c74860ed6` | [Read notes](../services/lambda/README.md) |
| `13e41f9fa05c` | `image 2.png` | `3391f8ae65408016baf0e08c74860ed6` | [Read notes](../services/lambda/README.md) |
| `b3d22b16648a` | `image 3.png` | `3391f8ae65408016baf0e08c74860ed6` | [Read notes](../services/lambda/README.md) |
| `49cf51587e6d` | `image 4.png` | `3391f8ae65408016baf0e08c74860ed6` | [Read notes](../services/lambda/README.md) |
| `0c1d0aa2a9be` | `image 5.png` | `3391f8ae65408016baf0e08c74860ed6` | [Read notes](../services/lambda/README.md) |
| `6c4dd4f2b9ef` | `image.png` | `3391f8ae65408016baf0e08c74860ed6` | [Read notes](../services/lambda/README.md) |
| `7de778a13f7b` | `image.png` | `34f1f8ae6540801e8f47e24c661eb8b0` | [Read notes](../services/nacl/README.md) |
| `7e852420e7ab` | `image 1.png` | `33f1f8ae654080d095b3d4230d63ac33` | [Read notes](../services/organizations/README.md) |
| `8d8b8a934be1` | `image 2.png` | `33f1f8ae654080d095b3d4230d63ac33` | [Read notes](../services/organizations/README.md) |
| `10110ed8c050` | `image.png` | `33f1f8ae654080d095b3d4230d63ac33` | [Read notes](../services/organizations/README.md) |
| `d0af4dfa6301` | `image.png` | `3521f8ae6540803abe46ebe01373f4bb` | [Read notes](../services/pinpoint/README.md) |
| `dab4445468be` | `image.png` | `3391f8ae654080e39993d658eb7cf787` | [Read notes](../services/resource-access-manager/README.md) |
| `050bf1189d11` | `image 1.png` | `3391f8ae6540809db42fccba59aa32bb` | [Read notes](../services/rds/README.md) |
| `3a8b8cf91aef` | `image 2.png` | `3391f8ae6540809db42fccba59aa32bb` | [Read notes](../services/rds/README.md) |
| `f02f5e89099b` | `image 3.png` | `3391f8ae6540809db42fccba59aa32bb` | [Read notes](../services/rds/README.md) |
| `dfaf9e200709` | `image 4.png` | `3391f8ae6540809db42fccba59aa32bb` | [Read notes](../services/rds/README.md) |
| `f3ab1a352284` | `image 5.png` | `3391f8ae6540809db42fccba59aa32bb` | [Read notes](../services/rds/README.md) |
| `c9d7f20c1ca9` | `image 6.png` | `3391f8ae6540809db42fccba59aa32bb` | [Read notes](../services/rds/README.md) |
| `0ab64a70326e` | `image.png` | `3391f8ae6540809db42fccba59aa32bb` | [Read notes](../services/rds/README.md) |
| `0598445d38a6` | `image 1.png` | `3481f8ae654080f086f5d6b9e5ad13f5` | [Read notes](../services/redshift/README.md) |
| `47d906ba4131` | `image.png` | `3481f8ae654080f086f5d6b9e5ad13f5` | [Read notes](../services/redshift/README.md) |
| `2a56f1b9a945` | `image 1.png` | `33d1f8ae6540806fb04ddcb0928ae0db` | [Read notes](../services/route-53/README.md) |
| `b9be233700a5` | `image 2.png` | `33d1f8ae6540806fb04ddcb0928ae0db` | [Read notes](../services/route-53/README.md) |
| `0d29f379ad9f` | `image 3.png` | `33d1f8ae6540806fb04ddcb0928ae0db` | [Read notes](../services/route-53/README.md) |
| `9a196457f1a4` | `image 4.png` | `33d1f8ae6540806fb04ddcb0928ae0db` | [Read notes](../services/route-53/README.md) |
| `edf2870166f5` | `image 5.png` | `33d1f8ae6540806fb04ddcb0928ae0db` | [Read notes](../services/route-53/README.md) |
| `5903b7d4c72d` | `image 6.png` | `33d1f8ae6540806fb04ddcb0928ae0db` | [Read notes](../services/route-53/README.md) |
| `97a540f65cbe` | `image.png` | `33d1f8ae6540806fb04ddcb0928ae0db` | [Read notes](../services/route-53/README.md) |
| `3f3d97dbc569` | `image 1.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `6532c4dae3bf` | `image 10.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `78d4e2cc7440` | `image 11.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `d5b300a3ec12` | `image 12.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `34bdc610eaf0` | `image 13.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `61158671e10b` | `image 14.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `5c116c552605` | `image 15.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `33a79a97e331` | `image 16.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `501660de1543` | `image 2.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `7323eff813a7` | `image 3.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `cf36b2b1a66d` | `image 4.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `483b118d26a2` | `image 5.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `5b98c7ee1b5f` | `image 6.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `3138016afb66` | `image 7.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `8d1002ab5d48` | `image 8.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `85ae522bd17b` | `image 9.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `dd7db7581042` | `image.png` | `3391f8ae65408058ba58dc2a23fd344d` | [Read notes](../services/s3/README.md) |
| `9f9f3c5f4283` | `image 1.png` | `33e1f8ae654080d7a05be40d6038e74a` | [Read notes](../services/sqs/README.md) |
| `e0e3b9339fca` | `image 2.png` | `33e1f8ae654080d7a05be40d6038e74a` | [Read notes](../services/sqs/README.md) |
| `b7ffa1a9b3db` | `image 3.png` | `33e1f8ae654080d7a05be40d6038e74a` | [Read notes](../services/sqs/README.md) |
| `9c64fcbbd99c` | `image 4.png` | `33e1f8ae654080d7a05be40d6038e74a` | [Read notes](../services/sqs/README.md) |
| `3caf0a68d6e6` | `image 5.png` | `33e1f8ae654080d7a05be40d6038e74a` | [Read notes](../services/sqs/README.md) |
| `8f7239b418a5` | `image.png` | `33e1f8ae654080d7a05be40d6038e74a` | [Read notes](../services/sqs/README.md) |
| `9723fb7e2f2a` | `image.png` | `3421f8ae6540808a9a23fe0262a7c587` | [Read notes](../services/tls-certificates/README.md) |
| `5ef9d12eed59` | `image.png` | `3411f8ae65408068bb54d9fc77881487` | [Read notes](../services/secrets-manager/README.md) |
| `ab2b9d233393` | `image 1.png` | `3541f8ae65408042b063fe1c9ce235f5` | [Read notes](../services/snowball/README.md) |
| `3c31e5cf03de` | `image.png` | `3541f8ae65408042b063fe1c9ce235f5` | [Read notes](../services/snowball/README.md) |
| `686b03346e4a` | `image 1.png` | `34f1f8ae6540801b8580fb485d05ee3e` | [Read notes](../services/storage-gateway/README.md) |
| `2118a3e2005e` | `image.png` | `34f1f8ae6540801b8580fb485d05ee3e` | [Read notes](../services/storage-gateway/README.md) |
| `ea87f689728b` | `image 1.png` | `3551f8ae65408059967dc034c4671374` | [Read notes](../services/system-design/README.md) |
| `6371c61aef23` | `image 2.png` | `3551f8ae65408059967dc034c4671374` | [Read notes](../services/system-design/README.md) |
| `684b54871ed4` | `image 3.png` | `3551f8ae65408059967dc034c4671374` | [Read notes](../services/system-design/README.md) |
| `578e43cc8dc6` | `image 4.png` | `3551f8ae65408059967dc034c4671374` | [Read notes](../services/system-design/README.md) |
| `6dbf0beb6768` | `image.png` | `3551f8ae65408059967dc034c4671374` | [Read notes](../services/system-design/README.md) |
| `96e558c62acc` | `image 1.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `e797c8ee2ab5` | `image 10.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `0b833fabe36f` | `image 12.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `6a1b6353f387` | `image 14.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `fe7c86ad51a0` | `image 15.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `f6b9c5e944a2` | `image 16.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `44f8e376844a` | `image 17.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `c64e8a3c9868` | `image 19.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `85e4da74c6de` | `image 2.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `c1a3be065c3a` | `image 20.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `6cac597f4e5a` | `image 21.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `5d632a57f00c` | `image 22.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `e8374dfe78c6` | `image 3.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `7cf5a32c3926` | `image 4.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `a0000b960965` | `image 5.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `9b310f939d03` | `image 7.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `9241fa749f80` | `image 8.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `adb99901c670` | `image 9.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `223c88a5f9bc` | `image.png` | `3391f8ae6540802face2df644c7ac30a` | [Read notes](../services/vpc/README.md) |
| `d2f6e9c875ce` | `image 1.png` | `3391f8ae654080de8d53e3b946aa5376` | [Read notes](../services/vpc/README.md) |
| `89d53db59589` | `image 2.png` | `3391f8ae654080de8d53e3b946aa5376` | [Read notes](../services/vpc/README.md) |

## Validation

- Source accounting: 98 HTML pages, 214 local images, and 3 externally hosted images.
- Repository structure: 72 service/topic folders and 74 study READMEs.
- Local Markdown references checked: 512.
- Retained illustrations linked and checked against original bytes: 16.
- Structured Markdown tables: 128.
- Automated audit errors: 0.

Independent content reviews covered all 72 services and both child pages; all actionable findings were corrected and rechecked. Automated length-reduction alerts for IAM Identity Center and Security Services were reviewed against their source packets and found to reflect removal of conversational repetition rather than lost substantive knowledge.
