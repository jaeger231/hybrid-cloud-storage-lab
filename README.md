# **Hybrid Cloud Storage and Data Migration with AWS Storage Gateway**

## **Project Overview**
This lab demonstrates how to set up a hybrid cloud storage solution using **AWS Storage Gateway (S3 File Gateway)** to migrate data from an **on-premises instance to Amazon S3 across two regions**.

### **Key Objectives**
- Set up **S3 buckets** in different AWS regions.
- Enable **versioning** for data protection.
- Configure an **S3 File Gateway** to act as a hybrid storage interface.
- **Mount the file share** on an on-premises Linux instance.
- **Migrate data** from the on-premises instance to Amazon S3.


## **Architecture Diagram**

![arch design](https://github.com/user-attachments/assets/8fb732c4-0524-4358-bdfa-6b05462a9086)


## **Tools and Technologies Used**
- **Amazon Web Services (AWS):** S3, S3 File Gateway, EC2, IAM
- **Linux Instance:** For mounting the file share
- **AWS CLI:** For configuring and interacting with AWS services
- **NFS Protocol:** For mounting the file share
- **Bash/Shell Scripts:** For automation


## **Prerequisites**
✅ An AWS account with necessary permissions
✅ A running Linux instance (Amazon Linux 2 / Ubuntu)
✅ AWS CLI installed and configured

---
## **Implementation Steps**

### **1. Create S3 Buckets in Different Regions**
```bash
aws s3api create-bucket --bucket my-primary-bucket-us-east-1 --region us-east-1 --create-bucket-configuration LocationConstraint=us-east-1
aws s3api create-bucket --bucket my-secondary-bucket-us-west-2 --region us-west-2 --create-bucket-configuration LocationConstraint=us-west-2
```
Enable versioning:
```bash
aws s3api put-bucket-versioning --bucket my-primary-bucket-us-east-1 --versioning-configuration Status=Enabled
aws s3api put-bucket-versioning --bucket my-secondary-bucket-us-west-2 --versioning-configuration Status=Enabled
```

---
### **2. Set Up S3 File Gateway**
1. Navigate to **AWS Storage Gateway** in AWS Console.
2. Set up an **S3 File Gateway** and configure it to connect to the S3 buckets.
3. Download and deploy the **gateway** on a Linux instance.

---
### **3. Create and Configure a File Share**
- Choose **NFS file share**.
- Link the file share to the **S3 File Gateway**.

---
### **4. Launch a Linux Instance**
```bash
aws ec2 run-instances --image-id ami-xxxxxxxxxx --count 1 --instance-type t2.micro --key-name MyKeyPair --security-group-ids sg-xxxxxxxx --subnet-id subnet-xxxxxxxx
```
Install NFS utilities:
```bash
sudo yum install -y nfs-utils   # For Amazon Linux 2
sudo apt update && sudo apt install -y nfs-common  # For Ubuntu
```

---
### **5. Mount the File Share on Linux Instance**
```bash
sudo mount -t nfs -o nolock,hard <File_Gateway_IP>:/<File_Share_Name> /mnt/nfs/s3
```
Verify the mount:
```bash
df -h | grep s3
```

---
### **6. Migrate Data to S3**
```bash
cp /path/to/local/data/* /mnt/nfs/s3/
```
Verify file upload:
```bash
aws s3 ls s3://my-primary-bucket-us-east-1/
```

---
## **Challenges Encountered**
**NFS Mount Issues:** One of the challenges I encountered was ensuring the correct IP for the NFS mount. Initially, I mistakenly used the wrong gateway IP, which caused the mount to fail. After correcting it to the File Gateway's IP, the mount worked as expected.

**File Versioning:** Ensuring versioning was enabled across both S3 buckets helped protect data during migration, especially if there was a need to roll back changes.

**Data Migration Challenges:** Connecting to the on-premises Linux instance and ensuring proper permissions for migrating data to the mounted file share presented some difficulties. I had to troubleshoot issues related to file permissions and ownership to successfully migrate data to S3.

---
## **Lessons Learned**
AWS Storage Gateway provides a simple solution to integrate on-premises storage with cloud storage.

Understanding the importance of versioning in S3 is crucial for protecting data during migration.

NFS file share is an efficient and seamless method for integrating cloud storage into on-premises environments.

Proper permissions and correct file ownership settings are critical when migrating data

---
## **Conclusion**
This lab successfully demonstrates how to configure and use AWS Storage Gateway (S3 File Gateway) for migrating data between on-premises systems and Amazon S3. By following the steps in this lab, I was able to efficiently set up hybrid cloud storage and migrate data, which is a critical skill for managing data in a hybrid cloud environment.



