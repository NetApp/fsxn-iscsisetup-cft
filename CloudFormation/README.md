# AWS RDS FSx ONTAP Automation using AWS CloudFormation

This CloudFormation script can be used to automate the integration of Netapp Fsx Ontap with Custom RDS (Relational Database Service) to allow Fsx Ontap to be used as a backend persistent storage for the database.

This template also integrates the Netapp Snapcenter software with Fsx Ontap to allow restore and backup of the data from Fsx.

## Scripts

- **rds_fsx.yaml**: CloudFormation script to mount FSx ONTAP volume on AWS RDS Custom Instance.

- **rds_fsx_with_snapcenter.yaml**: CloudFormation script to mount FSx ONTAP volume on AWS RDS Custom Instance with NetApp SnapCenter Installation and Setup.


## Pre-requisites

- AWS CloudFormation Module should be installed on the host you run this script from

- Custom RDS Instance and SnapCenter server instance with IAM role that includes "ssm:SendCommand", "ssm:ListCommandInvocations" and "ssm:GetCommandInvocation" permissions along with the default permissions required for RDS (https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-sqlserver.html#custom-setup-sqlserver.iam-vpc)

- Custom RDS Instance, SnapCenter server Instance and Fsx Ontap should be able to communicate with each other (networking and security groups need to ensure the same)

- S3 Bucket with packages.zip, SnapCenter installation file and a pem file uploaded to it
  
  Note: packages.zip file can be created using the commands given below on a Linux machine:

  ```
  mkdir packages
  cd packages
  pip install requests -t .
  pip install netapp-ontap -t .
  rm -rf *dist-info
  cd ..
  zip -r packages.zip packages
  ```


## Parameters

| Name | Type | Description |
| --- | --- | --- |
| `Prefix` | String | (Required) LUN and volume name Prefix. |
| `LunSizeData` | Number | (Required) Size of LUN for data volume. |
| `LunSizeLog` | Number | (Required) Size of LUN for log volume. |
| `LunSizeSnapInfo` | Number | (Required) Size of LUN for RDS Log volume. |
| `LogFolderDriveLetter` | String | (Required) Drive letter to be used for storing log folder in RDS Custom Instance (e.g. G,H,I etc.) |
| `OsType` | String | (Required) OS Type - windows or linux. |
| `PrivateSubnet1ID` | AWS::EC2::Subnet::Id | (Required) ID of Subnet1 in Availability Zone 1. |
| `PrivateSubnet2ID` | AWS::EC2::Subnet::Id | (Required) ID of Subnet2 in Availability Zone 2. |
| `StorageCapacityOntap` | Number | (Required) Storage capacity of the file system being created, in gibibytes if "UseNewOntap" is true. Valid values are 1024 GiB - 196608 GiB. Consider choosing a higher value for greater capacity. |
| `ThroughputCapacityOntap` | Number | (Required) Throughput of the Amazon FSx ONTAP file system. Valid values are 128 - 2048 if UseNewOntap is true. Consider choosing a higher value for better performance. |
| `VPCID` | AWS::EC2::VPC::Id | (Required) ID of the AWS VPC. |
| `SecurityGroupId` | AWS::EC2::SecurityGroup::Id | (Optional) ID of the Security Group. |
| `FsxAdminPassword` | String | (Required) Password for "fsxadmin" user in FSx File System. |
| `AwsAccessKey` | String | (Required) AWS Access Key. |
| `AwsSecretKey` | String | (Required) AWS Secret Key. |
| `UseNewOntap` | String | (Required) If a new ONTAP setup is required. (true/false). |
| `FsxFileSystemId` | String | (Required) ONTAP FSx File System ID if "UseNewOntap" is false. |
| `FsxSvmId` | String | (Required) ONTAP FSx SVM ID if "UseNewOntap" is false. |
| `CustomRdsEC2InstanceId` | String | (Required) EC2 Instance ID of the Custom RDS Instance. |
| `CustomRdsEC2InstanceIP` | String | (Required) Enter the EC2 Instance IP of the Custom RDS Instance. |
| `S3BucketName` | String | (Required) S3 bucket name which contains SnapCenter installer file, pem file and pkgs.tar file. |
| `S3FileKey` | String | (Required) File Key for the SnapCenter Server installer file from the S3 bucket. |
| `S3PemFile` | String | (Required) AWS pem key (Format - key-name.pem). |
| `WindowsAmiId` | String | (Required) Windows AMI ID for SnapCenter server. |
| `SnapHostType` | String | (Required) Type of instance you want for the SnapCenter host (min. 8GB RAM required) |


## Procedure

1. Clone the GitHub repository.
   ```
   git clone https://github.com/NetApp/fsxn-iscsisetup-cft.git
   ```

2. Login to AWS console and upload the pre-requisites mentioned in the CloudFormation template to your S3 bucket.

3. Navigate to CloudFormation service.

4. Click on "Create Stack" > "with New Resources".

5. Select the "Template is Ready" option.

6. Upload the CFT template from the repository in the "Upload a template file" section.
    ```
    rds_fsx.yaml
    ```
    or
    ```
    rds_fsx_with_snapcenter.yaml
    ```

7. Click on "Next".

8. Provide a "Stack Name" to the stack and fill the variables in the "Parameters" section.

9. Click on "Next" till you reach Step 4.

10. Mark the Acknowledgement checkboxes and click on "Create Stack".


## License
By accessing, downloading, installing or using the content in this repository, you agree the terms of the License laid out in License file.

Note that there are certain restrictions around producing and/or sharing any derivative works with the content in this repository. Please make sure you read the terms of the License before using the content. If you do not agree to all of the terms, do not access, download or use the content in this repository.

Copyright: 2022 NetApp Inc.

## Author Information

- [Dhruv Tyagi](mailto:dhruv.tyagi@netapp.com) - NetApp Solutions Engineering Team
- [Vedant Sethia](mailto:vedant.sethia@netapp.com) - NetApp Solutions Engineering Team

