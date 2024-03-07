# Efficient AWS Cost Management through Stale Resource Detection

## Problem :
Sometimes, developers create EC2 instances with volumes attached to them by default. For backup purposes, these developers also create snapshots. However, when they no longer need the EC2 instance and decide to terminate it, they sometimes forget to delete the snapshots created for backup. As a result, they continue to incur costs for these unused snapshots, even though they are not actively using them.

## Solution :
We're using AWS to save money on storage costs. We made a Smart Lambda function that looks at our snapshots and our EC2 instances. If Lambda finds a snapshot that isn't connected to any active EC2 instances, it deletes it to save us money. This helps us keep our AWS costs down.

## Note :
There are many similar problems like this. For instance, we might attach an Elastic IP to our EC2 instance but forget to delete the Elastic IP after terminating the EC2 instance. In such a case, the Elastic IP continues to incur costs for us.

## Steps :
### Step 1:
1. Log into your AWS Console.<br>
2. Navigate to the EC2 Console.<br>
3. In the Instances section, select 'Instances,' and then click on 'Launch Instance'.<br>

<img width="1792" alt="1" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/a299a43c-ff62-48af-a7c0-2c2544864f65">

4. Next, navigate to the 'Elastic Block Store' section and select 'Volumes'.<br>

<img width="1792" alt="2" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/531f33f2-a8e6-42c8-868a-ae4d485ac853">

5. You will notice that a default volume has already been created for us.<br>
6. Next, click on 'Snapshots,' and then click the 'Create Snapshot' button. It will prompt you with a page that looks like this.<br>

<img width="1013" alt="6" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/89b9ca73-0f88-4e45-aef0-bee82e3931e8">

7. In Volume ID section choose your default Volume ID created when we create instance.<br>
8. Next, click 'Next,' provide a name for your Snapshot, and then scroll down and click 'Create Snapshot'.<br>

<img width="1792" alt="3" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/ddf22787-edb0-4863-9030-77b01c3ded8d">

### Step 2 :
1. After creating a Snapshot, navigate to the Lambda Console..<br>
2. You will see some options in the user interface, such as 'Create Function'.<br>
3. Click on 'Functions'.<br>

![Lambda](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/0e0b8165-2894-4159-9d4b-d8f966126e53)

4. Select 'Author from Scratch,' then enter the Function name, and choose the latest Python version.<br>
5. Scroll down and click 'Create Function'.<br>
6. After creating the function, scroll down, and you will see something like the image below.<br>

<img width="1013" alt="7" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/c8df4e69-e57b-4894-9464-a49cf103b299">

7. Click on the 'Code' section.<br>
8. Next, clear the existing code and replace it with the 'identify_stale_snapshots.py' code.<br>

<img width="1013" alt="8" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/ab733d7e-e153-4050-9050-1885f240d85b">

9. Click 'Deploy' to save your changes, and then click 'Test.' It will prompt a page that looks like the one given below.<br>

<img width="1012" alt="9" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/4d581524-f72b-425e-b246-fc8c71ea9124">

10. Please configure the settings as displayed above and then scroll down. Next, click on 'Create Event'.
11. Once you've created the event, proceed to the IAM Console(Identity and Access Management) and then navigate policies section to create a new policy.

<img width="1014" alt="10" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/4a73594b-b4ce-4b42-974d-b99f86254f5c">

12. Select the service as 'EC2'
<img width="983" alt="11" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/32e8dc4b-ba69-47e5-ade7-2d44ad3c7ef5">

13. In the 'Actions' section, grant permissions for the following actions: DescribeInstances, DescribeVolumes, DescribeSnapshots, DeleteSnapshots.
14. After Creating the Policies , Navigate to the Lambda Sections.
15. Next, go to the page of the Lambda function you've created. In the "Permissions" section, click on the role name.

![lambda4](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/3a6160ad-9b81-4aba-b052-03c3a1191df8)

16. Click on 'Add Permissions' and then select 'Attach Policy.'

<img width="1013" alt="12" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/7cf6094a-5794-4365-86c5-b710ebb69217">

17. Choose the correct policy you created.

<img width="1014" alt="13" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/800825f5-f3be-43d6-a6ae-70834c3c4c2d">

18. Then scroll down and click 'Add Permissions'.
19. After that, you can go to the Lambda function page and run the code; it will display some outputs as shown below.

<img width="1014" alt="14" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/b418d62a-a4a3-4b50-8195-8cf9dfb662d0">

### Step 3 :
1. You can terminate the EC2 instance to test our Lambda function.
2. Navigate to the EC2 console and then terminate the EC2 instance.
3. Return to the Lambda console to test the code; go to the Lambda Function page.
4. Under the Code section, click 'Test code', it will display an output like this.

<img width="1013" alt="15" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/b66a9e61-72fb-4dee-a8f9-04145cc95f88">

5. As expected, our Lambda function deleted the snapshot because it was associated with a volume that couldn't be found.

## Additional notes:
We can use CloudWatch to automatically trigger the Lambda function every hour, day, minute, or second. However, this may result in higher costs because our Lambda execution time increases when triggered automatically. Nevertheless, manually triggering this function is a better choice because it allows us to trigger it when needed.

## CloudWatch or EventBridge Implementation :
### Steps :
1. Navigate to CloudWatch Console.

<img width="1013" alt="16" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/471164aa-b98a-49ab-8293-5e4ff2660336">
<img width="1012" alt="17" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/1ed74760-f72b-446a-afc1-1a3d3d93f8aa">
<img width="1014" alt="18" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/5a76e31e-38cd-42f4-8a35-fa27dc8d9466">
<img width="1012" alt="19" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/f23f1760-0f84-4d58-a69c-e09adbd64201">

2. Next, on the following page, configure the schedule pattern as follows:

<img width="1012" alt="20" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/39ae3eda-2e8f-4355-8356-660c0391b9df">

3. Scroll Down and then Click Next.

<img width="1014" alt="21" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/6267c907-797d-44df-9128-bfadc897a82c">
<img width="1013" alt="22" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/eada93c5-9503-4f17-b73d-fdaf31b12f14">

4. Scroll Down and then Click Next.
5. On the next page, choose 'None' for the 'Action after Schedule' option.

<img width="1010" alt="23" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/119ed317-8997-4878-911c-78cde2ffd47f">
<img width="1014" alt="24" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/6a495a05-4fbf-44fe-896c-2be9432a0361">
<img width="1013" alt="25" src="https://github.com/KadlagV-05/AWS-Cost-Optimization/assets/118795133/0879b197-1209-4e49-a7b8-21a4f4fffc5e">

6. You have successfully created the scheduler, which will trigger the Lambda function every hour.
7. However, please note that this setup will incur some costs since the function is triggered continuously every hour. Alternatively, we can configure it to run on specific days and times as needed.
