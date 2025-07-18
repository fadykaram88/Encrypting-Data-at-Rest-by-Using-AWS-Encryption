# Project 10: Encrypting Data at Rest by Using AWS Encryption Options

## Task 1: Uploading an Object and Seeing Its Default Encryption

1. First, download this picture (Clock).
2. Search for **S3** in the AWS console.
3. Create an **S3 bucket**:
   - Give it a unique name.
   - Enable **Server-side encryption** with **Amazon S3 managed keys (SSE-S3)**.
4. Choose **Upload** → **Add files** → select the clock picture.
   - Expand **Permissions** tab.
   - Choose **Grant public-read access**.
   - Click **Upload**, then **Close**.
5. To open the picture:
   - Choose the uploaded file.
   - Click **Open**.

---

## Task 2: Creating an AWS KMS Key

6. Search for **KMS** → Choose **Key Management Service** → **Customer managed keys** → **Create Key**:
   - **Key type**: Symmetric → Next
   - **Alias**: `MyKMSKey` → Next
   - In **Key administrators**, search for `voclabs` → Next
   - In **Key users**, search for `voclabs` → Next → Next → Finish

> **Note**: Who or what is `voclabs`?  
> 📌 _Search about it and tell me in the comments and don't be lazy!_

---

## Task 3: Creating and Attaching Encrypted Data Volume on an EC2 Instance

Here we will create an **encrypted EBS volume** using the **KMS key** created in the last task, then attach it to the EC2 instance.

7. Search for **EC2** → **Instances** → **Launch Instance**
   - Put it in **Lab VPC** (see Project 3).
   - Place in **public subnet**.
   - Use **key pair**: `Vockey`.
   - Use **security group**: includes two inbound rules:
     - Port **22**, Protocol: TCP, Source: 0.0.0.0/0
     - Port **80**, Protocol: TCP, Source: 0.0.0.0/0  
     - Group Name: `LabSG`
   - Outbound rule:
     - Port Range: **ALL**
     - Protocol: **ALL**
     - Destination: 0.0.0.0/0  
     - Group Name: `LabSG`
   - Place instance in **Availability Zone**: `us-east-1c`

8. In left sidebar, open **Volumes** → **Create Volume**:
   - Volume Type: **default**
   - Size: **1 GB**
   - Availability Zone: `us-east-1c`
   - Encryption: **Encrypt this volume** → Choose `MyKMSKey`
   - Click **Create Volume**

9. Choose the green link of the volume → **Actions** → **Attach Volume**:
   - **Instance**: the one you just created
   - **Device name**: `/dev/sdf`
   - Click **Attach Volume**

---

## Task 4: What If We Disable the Key?

10. Search for **KMS** → **Customer managed keys** → Choose your key → **Key Actions** → **Disable**
11. Go to **EC2** → **Volumes** → select your volume → **Actions** → **Detach Volume** → Refresh → **Attach Volume** again  
    ⚠️ You will see an error:
    > "Volume vol-... cannot be attached as the encrypted volume was unable to access the KMS key."

12. Search for **CloudTrail** in the console → choose **Event history**
13. Click the link for `DisableKey`
14. In **Event Record** → details show that the KMS key was disabled

15. Let's re-enable the `MyKMSKey`:
16. Search for **KMS** → **Customer managed key** → Select `MyKMSKey` → **Key Actions** → **Enable**
17. Go back to **EC2** → **Volumes** → select the volume → **Actions** → **Attach Volume**
    - Choose the instance
    - Device name: `/dev/sdf` → Attach

18. Go back to **CloudTrail** to see the full KMS key history.

---

## Final Note

19. I hope you understood the idea behind this project.
20. 👋 Goodbye!