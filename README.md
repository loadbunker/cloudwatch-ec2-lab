# Monitoring and Troubleshooting EC2 Instances Using Amazon CloudWatch

## Overview
This lab demonstrates how to monitor an Amazon EC2 instance, generate a CPU load, observe CloudWatch metrics, and create a CPU‑utilization alarm.

## Prerequisites
- An AWS account with permission to launch EC2 instances and access CloudWatch.
- AWS Management Console access.
- A local machine with an SSH client (PowerShell, Git Bash, WSL, etc.).

## Step‑by‑Step Instructions

1. **Launch an EC2 instance**
   - Open the EC2 console → *Launch Instance*.
   - Choose **Amazon Linux 2 AMI** and an eligible instance type (e.g., `t3.micro`).
   - **Key Pair:** create a new key pair (e.g., `monitor‑lab‑key.pem`) and download it.
   - **Security group:** add an inbound rule for **SSH (TCP 22)** from your IP address.
   - Review and **Launch** the instance.

2. **Open CloudWatch**
   - In the AWS console’s top navigation, select **CloudWatch**.

3. **Navigate to EC2 metrics**
   - In the left menu click **Metrics → Classic metrics**.
   - Choose the **`AWS/EC2`** namespace → **Per‑Instance Metrics**.

4. **Select your instance**
   - Find the row with your instance’s **InstanceId** and tick the boxes for:
     - `CPUUtilization`
     - `NetworkIn`
     - `NetworkOut`
   - Click **View graphed metrics**.

5. **Connect via SSH**
   ```powershell
   # Adjust the path to where you saved the key file
   icacls "C:\Users\MASF\Downloads\monitor‑lab‑key.pem" /inheritance:r /grant:r "%USERNAME%:R"
   ssh -i "C:\Users\MASF\Downloads\monitor‑lab‑key.pem" ec2-user@<PUBLIC_IP>
   ```
   Replace `<PUBLIC_IP>` with the instance’s public IPv4 address.

6. **Generate a CPU load**
   ```bash
   yes > /dev/null &
   ```
   Verify the process is running with `ps -ef | grep yes`.

7. **Observe the CPU spike**
   - Return to the CloudWatch metric page and refresh. You should see **CPUUtilization** rise above 70%.

8. **Stop the load**
   ```bash
   pkill yes
   ```
   Watch the CPU graph return to its normal low value.

9. **Create a CPU alarm**
   - CloudWatch → *Alarms* → **Create alarm**.
   - Select the same **CPUUtilization** metric for your instance.
   - Set **Threshold**: `Greater/Equal 70` for **3 consecutive 1‑minute periods**.
   - (Optional) Add an SNS notification or enable **Recover this instance**.
   - Provide an alarm name, e.g., `HighCPU-Instance01`, and click **Create alarm**.

## Clean‑up (to avoid charges)
- Terminate the EC2 instance.
- Delete the CloudWatch alarm.
- Remove the key pair from the console if no longer needed.

---
*Feel free to modify or extend these steps for deeper monitoring (e.g., enabling detailed monitoring, collecting logs, or automating with CloudFormation).*