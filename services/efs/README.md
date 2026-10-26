# Amazon EFS

[Back to index](../../README.md)

Amazon EFS provides shared file storage for Linux applications that require a POSIX-compatible file system.

## Notes

### Shared uploads across an Auto Scaling fleet

A CMS running on an Auto Scaling fleet of EC2 instances needs every instance to access the same uploaded documents. Storing those files on an EBS volume attached to an individual instance does not provide the shared file system required by the fleet.

EFS supports simultaneous access from thousands of EC2 instances, along with POSIX file permissions and file locking used by applications such as WordPress and Magento. A Regional EFS file system stores data across multiple Availability Zones, allowing instances in other AZs to continue accessing it after an AZ failure. This resilience statement applies to Regional EFS, not the separate One Zone storage option.

### Selecting shared storage

| Requirement | Service |
| --- | --- |
| Linux/POSIX shared files across an EC2 fleet | Amazon EFS |
| Windows shared files over SMB | Amazon FSx for Windows File Server |
| Object storage and static content | Amazon S3 |
| Low-latency block storage attached to an instance | Amazon EBS |

### Windows file access from on premises and AWS

For Windows servers that must access the same file system from a data center and EC2 while retaining private connectivity, use Amazon FSx for Windows File Server. Install an SMB client on the on-premises servers and mount the same FSx file system on both those servers and the Windows EC2 instances. An existing AWS Direct Connect connection provides private access from the data center to the VPC; the source scenario uses `us-east-1`.

```text
On-premises Windows servers
          |
          | SMB over Direct Connect
          v
Amazon FSx for Windows File Server
          ^
          | SMB
          |
Windows EC2 instances in the VPC
```

A Storage Gateway is not required simply because this architecture spans on-premises and cloud resources: the Windows clients can mount FSx directly over the private network.
