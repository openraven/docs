The documentation that follows provides guidance on how to remediate any security issues flagged by the Open Raven platform. You can find more information on rules and remediation via the [AWS CIS Benchmark](https://d1.awsstatic.com/whitepapers/compliance/AWS_CIS_Foundations_Benchmark.pdf) document. Most of the rules below include a number (i.e., "1.1," "2.2," etc.) for easy reference to this AWS CIS Benchmark document.

# **Identity and Access Management**

**Avoid the use of the "root" account (1.1)**

The "root" account has unrestricted access to all resources in the AWS account.

It is recommended that the use of this account be avoided.

**Ensure multi-factor authentication (MFA) is enabled for all IAM users that have a console password (1.2)**

Multi-Factor Authentication (MFA) adds an extra layer of protection on top of a username and password. With MFA enabled, when a user signs in to an AWS website, they will not only be prompted to enter their username and password but also an authentication code from their AWS linked MFA device.

It is recommended that MFA be enabled for all accounts that have a console password.

Perform the following to enable MFA via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the IAM console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/).

**Step 2.** In the navigation pane, click on _Users_.

**Step 3.** In the "User Name" list, choose the name of the intended MFA user.

**Step 4.** Select the "Security Credentials" tab, and then click on _Manage MFA Device_.

**Step 5.** In the "Manage MFA Device" wizard, click on _A virtual MFA device_, and then click on _Next Step_.

**Ensure credentials unused for 90 days or greater are disabled (1.3)**

AWS IAM users can access AWS resources using different types of credentials, such as passwords or access keys.

It is recommended that all credentials that have been unused for 90 or more days be removed or deactivated.

Perform the following to remove or deactivate credentials via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console at [https://console.aws.amazon.com/vpc/home](https://console.aws.amazon.com/vpc/home).

**Step 2.** Click on _Services_.

**Step 3.** Click on _IAM._

**Step 4.** Click on _Users._

**Step 5.** Click on _Security Credentials._

**Step 6.** As an Administrator, click on _Make Inactive_ for credentials that have not been used in 90 Days.

As an IAM User, click on _Make Inactive_ or _Delete_ for credentials that have not been used in 90 days.

**Ensure access keys are rotated every 90 days or less (1.4)**

Access keys consist of an access key ID and a secret access key, which are used together to sign programmatic requests that you make to AWS.

AWS users need their own access keys to make programmatic calls to AWS from the AWS Command Line Interface (AWS CLI), Tools for Windows PowerShell, the AWS SDKs, or direct HTTP calls using the APIs for individual AWS services.

It is recommended that all access keys be regularly rotated.

Perform the following to rotate access keys via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console at [https://console.aws.amazon.com/vpc/home](https://console.aws.amazon.com/vpc/home).

**Step 2.** Click on _Services._

**Step 3.** Click on _IAM._

**Step 4.** Click on _Users._

**Step 5.** Click on _Security Credentials._

**Step 6.** As an Administrator, click on _Make Inactive_ for keys that have not been rotated in 90 Days.

As an IAM User, click on _Make Inactive_ or _Delete_ for keys that have not been rotated or used in 90 Days.

**Step 7.** Click on _Create Access Key._

**Step 9.** Update programmatic call with new Access Key credentials.

Perform the following to rotate access keys via CLI:

Run the following command:

aws iam update-access-key aws iam create-access-key aws iam delete-access-key

**Password complexity (1.5 to 1.11)**

Password policies are, in part, used to enforce password complexity requirements. IAM password policies can be used to ensure that passwords comprise different character sets.

It is strongly recommended that your organization's password policy:

- Requires at least one uppercase letter (1.5)
- Requires at least one lowercase letter (1.6)
- Requires at least one symbol (1.7)
- Requires at least one number (1.8)
- Requires a minimum password length of 14 characters (1.9)
- Prevents password reuse (1.10)
- Expires after 90 days or less (1.11)

Perform the following to set the password policy to follow the rules above via the AWS management console:

**Step 1.** Log in to the AWS Console at [https://console.aws.amazon.com/vpc/home](https://console.aws.amazon.com/vpc/home) (with appropriate permissions to View Identity Access Management Account Settings).

**Step 2.** Go to "IAM Service" on the AWS Console.

**Step 3.** Click on _Account Settings_ on the left pane.

**Step 4.**

_**a) To set at least one uppercase letter**_

Check "Requires at least one uppercase letter" and click on _Apply Password Policy_.

_**b) To require at least one lowercase letter**_

Check "Requires at least one lowercase letter" and click on _Apply Password Policy_.

_**c) To require at least one symbol**_

Check "Require at least one non-alphanumeric character" and click on _Apply Password Policy_.

_**d) To require at least one number**_

Check "Require at least one number" and click on _Apply Password Policy_.

_**e) To set a minimum password length of 14 characters**_

Set "Minimum password length" to 14 or greater and click on _Apply Password Policy_.

_**f) To prevent password reuse**_

Check "Prevent password reuse" and set "Number of passwords to remember" to 24.

_**g) To expire passwords after 90 days**_

Check "Enable password expiration" and set "Password expiration period (in days)" to 90 or less.

Perform the following to set the password policy to follow the rules above via CLI:

**a) To set at least one uppercase letter**

Run the following command:

aws iam update-account-password-policy --require-uppercase-characters 

**b) To require at least one lowercase letter**

Run the following command:

aws iam update-account-password-policy --require-lowercase-characters

**c) To require at least one symbol**

Run the following command:

aws iam update-account-password-policy --require-symbols

**d) To require at least one number**

Run the following command:

aws iam update-account-password-policy --require-numbers 

**e) To set a minimum length of 14 characters**

Run the following command:

aws iam update-account-password-policy --minimum-password-length 14 

**f) To prevent password reuse**

Run the following command:

aws iam update-account-password-policy --password-reuse-prevention 24

**g) To expire passwords after 90 days**

Run the following command:

aws iam update-account-password-policy --max-password-age 90 

***Note:*** All commands starting with "aws iam update-account-password-policy" have to be combined into a single command in order for all of them to take effect.

**Ensure no root account access key exists (1.12)**

The root account is the most privileged user in an AWS account. AWS Access Keys provide programmatic access to a given AWS account.

It is recommended that all access keys associated with the root account be removed.

Perform the following to remove access keys associated with the root account via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console as Root and open the IAM console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/).

**Step 2.** Click on \<Root_Account_Name\> at the top right and select _Security Credentials_ from the drop-down list.

**Step 3.** On the pop-out screen, click on _Continue to Security Credentials._

**Step 4.** Click on _Access Keys_ (Access Key ID and Secret Access Key).

**Step 5.** Under the "Status" column, if there are any Keys that are Active:

- Click on _Make Inactive_ (i.e., temporarily disable Key if you think you may need it again).
- Click _Delete_ (i.e., delete the key for good. Deleted keys **cannot be recovered** ).

**MFA for the root account (1.13 and 1.14)**

The root account is the most privileged user in an AWS account. MFA adds an extra layer of protection on top of a username and password used to access this account.

With MFA enabled, when a user signs in to an AWS website, they will be prompted for their username and password as well as for an authentication code from their AWS MFA device.

For Level 2, it is recommended that the root account be protected with a hardware MFA.

Perform the following to establish virtual MFA/hardware MFA for the root account via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the IAM console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/).

***Note:*** To manage MFA devices for the root AWS account, you must use your root account credentials to sign in to AWS. You cannot manage MFA devices for the root account using other credentials.

**Step 2.** Click on _Dashboard_, and under "Security Status" expand "Activate MFA" on your root account.

**Step 3.** Click on _Activate MFA._

**Step 4.**

**a) Virtual MFA**

In the wizard, click on _A virtual MFA device_ and then click on _Next Step._

**b) Hardware MFA**

In the wizard, click on _A hardware MFA device_ and then click on _Next Step_.

**Step 5.**

**a) Virtual MFA**

IAM generates and displays configuration information for the virtual MFA device, including a QR code graphic. The graphic is a representation of the "secret configuration key" that is available for manual entry on devices that do not support QR codes.

**b) Hardware MFA**

In the "Serial Number" box, enter the serial number found on the back of the MFA device.

**Step 6.**

**a) Virtual MFA**

Open your virtual MFA application. For a list of apps that you can use for hosting virtual MFA devices, see "Virtual MFA Applications." If the virtual MFA application supports multiple accounts (multiple virtual MFA devices), choose the option to create a new account (a new virtual MFA device).

**b) Hardware MFA**

In the "Authentication Code 1" box, enter the six-digit number displayed by the MFA device. You might need to press the button on the front of the device to display the number. Wait 30 seconds while the device refreshes the code, and then enter the next six-digit number into the "Authentication Code 2" box. You might need to press the button on the front of the device again to display the second number.

**Step 7.**

**a) Virtual MFA**

Determine whether the MFA app supports QR codes, and then do one of the following:

- Use the app to scan the QR code. For example, you might choose the camera icon or choose an option similar to Scan code. Then, use the device's camera to scan the code.
- In the "Manage MFA Device" wizard, click on _Show secret key_ for manual configuration. Then, type the secret configuration key into your MFA application. When you are finished, the virtual MFA device starts generating one-time passwords. Now, a) Go to the "Manage MFA Device" wizard, b) In the "Authentication Code 1" box, type the one time password that currently appears in the virtual MFA device, c) Wait up to 30 seconds for the device to generate a new one-time password, d) Type the second one-time password into the "Authentication Code 2" box, d) Choose _Active Virtual MFA_.


**b) Hardware MFA**

Click on _Next Step_. The MFA device is now associated with the AWS account. The next time you use your AWS account credentials to sign in, you will need to type in a code from the hardware MFA device.

**Ensure security questions are registered in the AWS account (1.15)**

The AWS support portal allows account owners to establish security questions that can be used to authenticate individuals calling AWS customer service for support.

It is recommended that security questions be established.

Perform the following to register security questions in the AWS account via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console at [https://console.aws.amazon.com/vpc/home](https://console.aws.amazon.com/vpc/home) as a root user.

**Step 2.** Click on the \<Root_Account_Name\> from the top right of the console.

**Step 3.** From the drop-down menu, click _My Account._

**Step 4.** Scroll down to the "Configure Security Questions" section and click on _Edit._

**Step 5.** Click on each Question

- From the drop-down menu, select an appropriate question.
- Click on the _Answer_ section.
- Enter an appropriate answer.

Follow this process for all three questions.

**Step 6.** Click _Update_ when complete.

**Step 7.** Store the security questions and answers in a secure physical location.

**Ensure IAM policies are attached only to groups or roles (1.16)**

By default, IAM users, groups, and roles have no access to AWS resources. IAM policies allow certain privileges to be granted to users, groups, or roles.

It is recommended that IAM policies be applied directly to groups and roles but not users.

Perform the following to create an IAM group and assign a policy to it via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the IAM console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/).

**Step 2.** In the navigation pane, click on _Groups_ and then click on _Create New Group_.

**Step 3.** In the "Group Name" box, type the name of the group and then click on _Next Step._

**Step 4.** In the list of policies, select the checkbox for each policy that you want to apply to all members of the group. Then click on _Next Step_.

**Step 5.** Click on _Create Group_.

**Step 6.** Perform the following to add a user to a given group:

- Log in to the AWS Management Console and open the IAM console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/).
- In the navigation pane, click on _Groups_.
- Select the group you want to add a user to.
- Click on _Add Users To Group._
- Select the users to be added to the group.
- Click on _Add Users._

**Step 7.** Perform the following to remove a direct association between a user and policy:

- Log in to the AWS Management Console and open the IAM console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/).
- In the left navigation pane, click on _Users._
- For each user, a) Select the user, b) Select the "Permissions" tab, c) Expand "Managed Policies" d) Click on _Detach Policy_ for each policy, e) Expand "Inline Policies," f) Click on _Remove Policy_ for each policy.

**Maintain current contact details (1.17)**

Ensure email and telephone details for AWS accounts are current and map to **more than one individual** in your organization.

AWS will use these details to contact the account owner if any activity is judged to be in breach of the Acceptable Use Policy or determined to be indicative of likely security compromise by the AWS Abuse team.

Since an AWS account supports a number of contact details, it is strongly recommended that organizations maintain contact details for more than just a single individual. This is because circumstances may arise where that individual is unavailable.

Email contact details should point to a mail alias that forwards emails to multiple individuals within the organization. Where feasible, phone contact details should point to a PBX hunt group or another call-forwarding system.

This activity can only be performed via the AWS Console, with a user who has permission to read and write Billing information (aws-portal:\*Billing ):

**Step 1.** Log in to the AWS Management Console and open the Billing and Cost Management console at [https://console.aws.amazon.com/billing/home#/](https://console.aws.amazon.com/billing/home#/).

**Step 2.** In the navigation bar, choose your account name, and then click on _My Account_.

**Step 3.** On the "Account Settings" page, next to "Account Settings" click on _Edit_.

**Step 4.** Next to the field that you need to update, click on _Edit_.

**Step 5.** After you have entered your changes, click on _Save changes_.

**Step 6.** After you have made your changes, click on _Done_.

**Step 7.** To edit your contact information, under "Contact Information" click on _Edit_.

**Step 8.** For the fields that you want to change, type your updated information, and then click on _Update_.

**Ensure security contact information is registered (1.18)**

AWS gives customers the option to specify contact information for the account's security team. It is recommended that this information be provided.

Perform the following to establish security contact information via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console at [https://console.aws.amazon.com/vpc/home](https://console.aws.amazon.com/vpc/home).

**Step 2.** Click on your account name at the top right corner of the console.

**Step 3.** From the drop-down menu, click on _My Account_.

**Step 4.** Scroll down to the "Alternate Contacts" section.

**Step 5.** Enter the contact information in the "Security" section.

***Note:*** Consider specifying an internal email distribution list to ensure emails are regularly monitored by more than one individual.

**Ensure IAM instance roles are used for AWS resource access from instances (1.19)**

AWS access from within AWS instances can be done either by:

1. Encoding AWS keys into AWS API calls, or
2. Assigning the instance to a role that has an appropriate permissions policy for the required access.

"AWS Access" means accessing the APIs of AWS in order to access AWS resources or manage AWS account resources.

IAM roles can only be associated at the launch of an instance. To remediate an instance and add it to a role, you must create a new instance.

If the instance has no external dependencies on its current private IP or if public addresses are elastic IPs, via the AWS Management Console:

**Step 1.** In AWS IAM, create a new role. Assign a permissions policy if the needed permissions are already known.

**Step 2.** In the AWS console, launch a new instance with identical settings to the existing instance, and ensure that the newly created role is selected.

**Step 3.** Shut down both the existing instance and the new instance.

**Step 4.** Detach disks from both instances.

**Step 5.** Attach the existing instance disks to the new instance.

**Step 6.** Boot the new instance and you should have the same machine, but with the associated role.

***Note 1:*** If your environment has dependencies on a dynamically assigned PRIVATE IP address, you can create an AMI from the existing instance and destroy the old one. Then, when launching from the AMI, manually assign the previous private IP address.

***Note 2:*** If your environment has dependencies on a dynamically assigned PUBLIC IP address, there is no way to ensure the address is retained and assign it an instance role. Dependencies on dynamically assigned public IP addresses are a bad practice. If possible, you may wish to rebuild the instance with a new elastic IP address and make the investment to remediate affected systems while assigning the system to a role.

**Ensure a support role has been created to manage incidents with AWS Support (1.20)**

AWS provides a support center that can be used for incident notification and response, as well as technical support and customer service. Create an IAM Role to allow authorized users to manage incidents with AWS Support.

To create a support role to manage incidents with AWS support, use the Amazon unified command-line interface and perform the following:

**Step 1.** Create a trust relationship policy document that allows \<iam_user\> to manage AWS incidents, and save it locally as /tmp/TrustPolicy.json: { "Version": "2012-10-17", "Statement": [ { "Effect": "Allow", "Principal": { "AWS": "arn:aws:iam::321859670049:root" }, "Action": "sts:AssumeRole" } ] }

**Step 2.** Create the IAM role using the above trust policy: aws iam create-role --role-name \<aws_support_iam_role\> --assume-rolepolicy-document file:///tmp/TrustPolicy.json

**Step 3.** Attach 'AWSSupportAccess' managed policy to the created IAM role: aws iam attach-role-policy --policy-arn \<iam_policy_arn\> --role-name \<aws_support_iam_role\>

**Do not set up access keys during initial user setup for all IAM users that have a console password (1.21)**

AWS console marks the checkbox for creating access keys enabled by default. This results in many access keys being generated unnecessarily. In addition to unnecessary credentials, this also generates unnecessary management work when it comes to auditing and rotating these keys.

Perform the following to delete access keys that do not pass the audit via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console at [https://console.aws.amazon.com/vpc/home](https://console.aws.amazon.com/vpc/home).

**Step 2.** Click on _Services_.

**Step 3.** Click on _IAM._

**Step 4.** Click on _Users._

**Step 5.** Click on _Security Credentials._

**Step 6.** As an Administrator, click on _Delete_ for keys that were created at the same time as the user profile but have not been used.

As an IAM User, click on _Delete_ for keys that were created at the same time as the user profile but have not been used.

Perform the following to delete access keys that do not pass the audit via CLI:

Run the following command:

iam delete-access-key

**Ensure IAM policies that allow full administrative privileges are not created (1.22)**

IAM policies are the means by which privileges are granted to users, groups, or roles.

It is recommended and considered standard security advice to grant these based on the principle of least privilege. In other words, organizations should never grant users, groups, or roles permissions beyond the minimum required to perform a particular task.

Determine what users need to do and then craft policies that let the users perform only those tasks instead of allowing full administrative privileges.

Perform the following to detach the policy that grants full administrative privileges from users via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the IAM console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/).

**Step 2.** In the navigation pane, click on _Policies_ and then search for the policy name found in the audit step.

**Step 3.** Select the policy that needs to be deleted.

**Step 4.** In the policy action menu, click on _Detach_.

**Step 5.** Select all Users, Groups, and Roles that have this policy attached.

**Step 6.** Click on _Detach Policy_.

**Step 7.** In the policy action menu, click on _Detach._

Perform the following to detach the policy that grants full administrative privileges from users via the CLI:

**Step 1.** Lists all IAM users, groups, and roles that the specified managed policy is attached to. aws iam list-entities-for-policy --policy-arn \<policy_arn\> 

**Step 2.** Detach the policy from all IAM Users: aws iam detach-user-policy --user-name \<iam_user\> --policy-arn \<policy_arn\> 

**Step 3.** Detach the policy from all IAM Groups: aws iam detach-group-policy --group-name \<iam_group\> --policy-arn \<policy_arn\> 

**Step 4.** Detach the policy from all IAM Roles: aws iam detach-role-policy --role-name \<iam_role\> --policy-arn \<policy_arn\>

# **Logging**

**Ensure CloudTrail is enabled in all regions (2.1)**

Perform the following to enable global (Multi-region) CloudTrail logging via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the IAM console at [https://console.aws.amazon.com/cloudtrail](https://console.aws.amazon.com/cloudtrail).

**Step 2.** Click on _Trails_ on the left navigation pane.

**Step 3.** Click on _Get Started Now_, if presented:

- Click on _Add new trail._
- Enter a trail name in the "Trail name" box.
- Set the "Apply trail to all regions" option to "Yes.""
- Specify an S3 bucket name in the S3 bucket box.
- Click on _Create_.

**Step 4.** If one or more trails already exist, select the target trail to enable global logging.

**Step 5.** Select the edit icon (pencil) next to "Apply trail to all regions," click on _Yes_ and then click on _Save_.

**Step 6.** Select the edit icon (pencil) next to "Management Events," click on _All_ for setting "Read/Write Events," and then click on _Save_.

Perform the following to enable global (Multi-region) CloudTrail logging via CLI:

Run the following command: 

aws cloudtrail create-trail --name \<trail_name\> --bucket-name \<s3_bucket_for_cloudtrail\> --is-multi-region-trail aws cloudtrail update-trail --name \<trail_name\> --is-multi-region-trail 

***Note:*** Creating CloudTrail via CLI without providing any overriding options configures Management Events to set All types of Read/Writes by default.

**Ensure CloudTrail log file validation is enabled (2.2)**

CloudTrail log file validation creates a digitally signed digest file containing a hash of each log that CloudTrail writes to S3. These digest files can be used to determine whether a log file was changed, deleted, or unchanged after CloudTrail delivered the log.

It is recommended that file validation be enabled on all CloudTrails.

Perform the following to enable log file validation on a given trail via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the IAM console at [https://console.aws.amazon.com/cloudtrail](https://console.aws.amazon.com/cloudtrail).

**Step 2.** Click on _Trails_ on the left navigation pane.

**Step 3.** Click on the target trail.

**Step 4.** Within the S3 section, click on the edit icon (pencil).

**Step 5.** Click on _Advanced_

**Step 6.** Click on the _Yes_ radio button in the section "Enable log file validation."

**Step 7.** Click on _Save._

Perform the following to enable log file validation on a given trail via CLI:

Run the following command:

aws cloudtrail update-trail --name \<trail_name\> --enable-log-file-validation 

***Note:*** Periodic validation of logs using these digests can be performed by running the following command: aws cloudtrail validate-logs --trail-arn \<trail_arn\> --start-time \<start_time\> --end-time \<end_time\>

**Ensure the S3 bucket used to store CloudTrail logs is not publicly accessible (2.3)**

CloudTrail logs a record of every API call made in your AWS account. These logs are stored in an S3 bucket.

It is recommended that the bucket policy, or access control list (ACL), is applied to the S3 bucket that CloudTrail logs to prevent public access to the CloudTrail logs.

Perform the following to remove any public access that has been granted to the bucket via an ACL or S3 bucket policy via the AWS Management Console:

**Step 1.** Go to the Amazon S3 console at [https://console.aws.amazon.com/s3/home](https://console.aws.amazon.com/s3/home).

**Step 2.** Right-click on the bucket and click on _Properties._

**Step 3.** In the "Properties" pane, click on the _Permissions_ tab. The tab shows a list of permissions granted, with one row per permission granted, in the bucket ACL. Each row identifies the grantee and the permissions granted.

**Step 4.** Select the row that grants permission to "Everyone" or "Any Authenticated User."

**Step 5.** Uncheck all the permissions granted to "Everyone" or "Any Authenticated User" (click on _x_ to delete the row).

**Step 6.** Click _Save_ to save the ACL.

**Step 7.** If the "Edit bucket policy" button is present, click on it.

**Step 8.** Remove any Statement that has an Effect set to “Allow” and a Principal set to "" or {"AWS" : ""}.

**Ensure CloudTrail trails are integrated with CloudWatch Logs (2.4)**

AWS CloudTrail is a web service that records AWS API calls made in a given AWS account. The information recorded here includes the:

- Identity of the API caller
- Time of the API call
- Source IP address of the API caller
- Request parameters
- Response elements returned by the AWS service.

CloudTrail uses Amazon S3 for log file storage and delivery. In addition to capturing CloudTrail logs within a specified S3 bucket for long-term analysis, real-time analysis can be performed by configuring CloudTrail to send logs to CloudWatch Logs. For a trail that is enabled in all regions within an account, CloudTrail sends log files from all regions to a CloudWatch Logs log group.

It is recommended that CloudTrail logs be sent to CloudWatch Logs

Perform the following to integrate CloudTrail trails with CloudWatch Logs via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the CloudTrail console at [https://console.aws.amazon.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/).

**Step 2.** Under "All Buckets,"" click on the target bucket you want to evaluate.

**Step 3.** Click on _Properties_ on the top right of the console.

**Step 4.** Click on _Trails_ in the left menu.

**Step 5.** Click on each trail where no CloudWatch Logs are defined.

**Step 6.** Go to the "CloudWatch Logs" section and click on _Configure._

**Step 7.** Define a new or select an existing log group.

**Step 8.** Click on _Continue._

**Step 9.** Configure the IAM Role, which will deliver CloudTrail events to CloudWatch Logs.

**Step 10.** Create/Select an IAM Role and Policy Name.

**Step 11.** Click on _Allow_ to continue.

Perform the following to integrate CloudTrail trails with CloudWatch Logs via CLI:

Run the following command:

aws cloudtrail update-trail --name \<trail_name\> --cloudwatch-logs-log-grouparn \<cloudtrail_log_group_arn\> --cloudwatch-logs-role-arn \<cloudtrail_cloudwatchLogs_role_arn\>

**Ensure AWS Config is enabled in all regions (2.5)**

AWS Config is a web service that performs configuration management of supported AWS resources within your account and delivers log files to you.

The recorded information includes the:

- Configuration item (AWS resource).
- Relationships between configuration items (AWS resources).
- Any configuration changes between resources.

It is recommended that AWS Config is enabled in all regions.

Perform the following to implement AWS Config configuration via the AWS Management Console:

**Step 1.** Select the region you want to focus on in the top right of the console.

**Step 2.** Click on _Services._

**Step 3.** Click on _Config_

**Step 4.** Define which resources you want to record in the selected region.

**Step 5.** Choose to include global resources (IAM resources).

**Step 6.** Specify an S3 bucket in the same account or another managed AWS account

**Step 7.** Create an SNS Topic from the same AWS account or another managed AWS account

Perform the following to implement AWS Config configuration via CLI:

**Step 1.** Ensure there is an appropriate S3 bucket, SNS topic, and IAM role per the AWS Config Service prerequisites.

**Step 2.** Run this command to set up the configuration recorder aws configservice subscribe --s3-bucket my-config-bucket --sns-topic arn:aws:sns:us-east-1:012345678912:my-config-notice --iam-role arn:aws:iam::012345678912:role/myConfigRole 

**Step 3.** Run this command to start the configuration recorder: start-configuration-recorder --configuration-recorder-name \<value

**Ensure S3 bucket access logging is enabled on the CloudTrail S3 bucket (2.6)**

S3 Bucket Access Logging generates a log that contains access records for each request made to your S3 bucket.

An access log record contains details about the request, such as the:

- Request type
- Resources specified in the request
- Time and date that the request was processed.

It is recommended that bucket access logging be enabled on the CloudTrail S3 bucket.

Perform the following to enable S3 bucket logging via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the S3 console at [https://console.aws.amazon.com/s3](https://console.aws.amazon.com/s3).

**Step 2.** Under "All Buckets" click on the target S3 bucket.

**Step 3.** Click on _Properties_ in the top right of the console.

**Step 4.** Under Bucket: \<s3_bucket_for_cloudtrail\> click on _Logging._

**Step 5.** To configure bucket logging:

- Click on the _Enabled_ checkbox.
- Select the Target Bucket from the list.
- Enter a Target Prefix.
- Click _Save_.

**Ensure CloudTrail logs are encrypted at rest using KMS CMKs (2.7)**

AWS CloudTrail is a web service that records AWS API calls for an account and makes those logs available to users and resources in accordance with IAM policies.

AWS Key Management Service (KMS) is a managed service that helps create and control the encryption keys used to encrypt account data and uses Hardware Security Modules (HSMs) to protect the security of encryption keys.

CloudTrail logs can be configured to leverage server-side encryption (SSE) and KMS customer-created master keys (CMK) to further protect CloudTrail logs. It is recommended that CloudTrail be configured to use SSE-KMS.

Perform the following to configure CloudTrail to use SSE-KMS via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the CloudTrail console at [https://console.aws.amazon.com/cloudtrail](https://console.aws.amazon.com/cloudtrail).

**Step 2.** In the left navigation pane, click on _Trails._

**Step 3.** Click on a Trail.

**Step 4.** Under the S3 section, click on the edit button (pencil icon).

**Step 5.** Click on _Advanced_.

**Step 6.** Select an existing CMK from the KMS key Id drop-down menu.

***Note 1:*** Ensure the CMK is located in the same region as the S3 bucket.

***Note 2:*** You will need to apply a KMS Key policy on the selected CMK in order for CloudTrail as a service to encrypt and decrypt log files using the CMK provided. Steps are provided here for editing the selected CMK Key policy.

**Step 7.** Click on _Save_.

**Step 8.** You will see a notification message stating that you need to have decrypt permissions on the specified KMS key to decrypt log files.

**Step 9.** Click on _Yes._

Perform the following to configure CloudTrail to use SSE-KMS via CLI

Run the following command:

aws cloudtrail update-trail --name \<trail_name\> --kms-id \<cloudtrail_kms_key\> aws kms put-key-policy --key-id \<cloudtrail_kms_key\> --policy \<cloudtrail_kms_key_policy\>

**Ensure rotation for customer created CMKs is enabled (2.8)**

The AWS Key Management Service (KMS) allows customers to rotate the backing key. The backing key is key material stored within the KMS and is tied to the key ID of the Customer Created Customer Master Key (CMK). It is the backing key that is used to perform cryptographic operations such as encryption and decryption.

Automated key rotation currently retains all prior backing keys so that decryption of encrypted data can take place transparently.

It is recommended that CMK key rotation be enabled.

Perform the following to enable customer-created CMK rotation via the AWS Management Console:

**Step 1.** Log  in to the AWS Management Console and open the IAM console at [https://console.aws.amazon.com/iam](https://console.aws.amazon.com/iam).

**Step 2.** In the left navigation pane, click on _Encryption Keys_.

**Step 3.** Select a customer-created master key (CMK).

**Step 4.** Under the "Key Policy" section, move down to "Key Rotation.""

**Step 5.** Check the "Rotate this key every year" checkbox.

Perform the following to enable customer-created CMK rotation via CLI:

Run the following command:

aws kms enable-key-rotation --key-id \<kms_key_id\>

**Ensure VPC flow logging is enabled in all VPCs (2.9)**

VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC. After you've created a flow log, you can view and retrieve its data in Amazon CloudWatch Logs.

It is recommended that VPC Flow Logs be enabled for packet "Rejects" for VPCs.

Perform the following to determine if the VPC Flow logs feature is enabled via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console at [https://console.aws.amazon.com/vpc/home](https://console.aws.amazon.com/vpc/home).

**Step 2.** Click on _Services_ and then click on _VPC_.

**Step 3.** In the left navigation pane, click on _Your VPCs._

**Step 4.** Select a VPC.

**Step 5.** In the right pane, click on the "Flow Logs" tab.

**Step 6.** If no Flow Log exists, click on _Create Flow Log._

**Step 7.** For "Filter,"" select _Reject._

**Step 8.** Enter in a "Role" and "Destination Log Group.""

**Step 9.** Click on _Create Log Flow._

**Step 10.** Click on _CloudWatch Logs Group_

***Note:*** Setting the filter to "Reject" will dramatically reduce the accumulation of logging data while providing sufficient information for the purposes of breach detection, research, and remediation.

However, during periods of least privilege security group engineering, setting this filter to "All" can be very helpful in discovering the existing traffic flows required for the proper operation of an already running environment.

By default, CloudWatch Logs will store logs indefinitely unless a specific retention period is defined for the log group. When choosing the number of days to retain logs, keep in mind that, on average, it takes a typical organization 210 days (at the time of this writing) to realize they have been breached. Since additional time is required to research a breach, a minimum 365-day retention policy allows time for detection and research.

You may also wish to archive the logs to a cheaper storage service rather than simply deleting them. See the following AWS resource to manage CloudWatch Logs retention periods: [http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/SettingLogRetention.html](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/SettingLogRetention.html)

# **Monitoring**

Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to CloudWatch Logs and establishing corresponding metric filters and alarms.

It is strongly recommended that a metric filter and alarm be established for detecting:

- Unauthorized API calls (3.1)
- Console logins that are not protected by multi-factor authentication (MFA) (3.2)
- Root login attempts (3.3)
- Changes made to Identity and Access Management (IAM) policies (3.4)
- Changes to CloudTrail&#39;s configurations (3.5)
- Failed console authentication attempts (3.6)
- Customer-created CMKs which have changed state to disabled or scheduled deletion (3.7)
- Changes to S3 buckets (3.8)
- Changes to AWS Config configuration (3.9)
- Changes made to Security Groups (***Note:*** Security Groups are a stateful packet filter that controls ingress and egress traffic within a VPC.) (3.10)
- Changes made to NACLs (3.11)
- Changes to the network (3.12)
- Changes to route tables (3.13)
- Changes made to VPCs (***Note:*** It is possible to have more than 1 VPC within an account. In addition, it is also possible to create a peer connection between 2 VPCs, which enables network traffic to route between VPCs) (3.14)

Perform the following to set up the metric filter, alarm, SNS topic, and subscription via the AWS Management Console:

**Step 1.**

**a) Detect unauthorized API calls**

Create a metric filter based on the filter pattern provided which checks for unauthorized API calls and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<unauthorized_api_calls_metric\> --metric-transformations metricName= \<unauthorized_api_calls_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ ($.errorCode = "UnauthorizedOperation") || ($.errorCode = "AccessDenied") }' 

**b) Detect console logins that are not protected by multi-factor authentication (MFA)**

Create a metric filter based on the filter pattern provided which checks for AWS Management Console sign-in without MFA and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<no_mfa_console_signin_metric\> --metric-transformations metricName= \<no_mfa_console_signin_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ ($.eventName = "ConsoleLogin") && ($.additionalEventData.MFAUsed != "Yes") }' 

**c) Detect root login attempts**

Create a metric filter based on the filter pattern provided which checks for "Root'' account usage and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<root_usage_metric\> --metric-transformations metricName= \<root_usage_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filterpattern '{ $.userIdentity.type = "Root" && $.userIdentity.invokedBy NOT EXISTS && $.eventType != "AwsServiceEvent" }' 

**d) Detect changes made to Identity and Access Management (IAM) policies**

Create a metric filter based on filter pattern provided which checks for IAM policy changes and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<iam_changes_metric\> --metric-transformations metricName= \<iam_changes_metric\> ,metricNamespace='CISBenchmark',metricValue=1 -- filter-pattern '{($.eventName=DeleteGroupPolicy)||($.eventName=DeleteRolePolicy)||($.eventNa me=DeleteUserPolicy)||($.eventName=PutGroupPolicy)||($.eventName=PutRolePolic y)||($.eventName=PutUserPolicy)||($.eventName=CreatePolicy)||($.eventName=Del etePolicy)||($.eventName=CreatePolicyVersion)||($.eventName=DeletePolicyVersi on)||($.eventName=AttachRolePolicy)||($.eventName=DetachRolePolicy)||($.event Name=AttachUserPolicy)||($.eventName=DetachUserPolicy)||($.eventName=AttachGr oupPolicy)||($.eventName=DetachGroupPolicy)}' 

**e) Detect changes to CloudTrail's configurations**

Create a metric filter based on the filter pattern provided which checks for cloudtrail configuration changes and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<cloudtrail_cfg_changes_metric\> --metric-transformations metricName= \<cloudtrail_cfg_changes_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ ($.eventName = CreateTrail) || ($.eventName = UpdateTrail) || ($.eventName = DeleteTrail) || ($.eventName = StartLogging) || ($.eventName = StopLogging) }' 

**f) Detect failed console authentication attempts**

Create a metric filter based on the filter pattern provided which checks for AWS management Console Login Failures and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<console_signin_failure_metric\> --metric-transformations metricName= \<console_signin_failure_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ ($.eventName = ConsoleLogin) && ($.errorMessage = "Failed authentication") }' 

**g) Detect customer-created CMKs which have changed state to disabled or scheduled deletion**

Create a metric filter based on the filter pattern provided which checks for disabled or scheduled for deletion CMK's and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<disable_or_delete_cmk_changes_metric\> --metrictransformations metricName= \<disable_or_delete_cmk_changes_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{($.eventSource = kms.amazonaws.com) && (($.eventName=DisableKey)||($.eventName=ScheduleKeyDeletion)) }' 

**h) Detect changes to S3 buckets**

Create a metric filter based on the filter pattern provided which checks for S3 bucket policy changes and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<s3_bucket_policy_changes_metric\> --metric-transformations metricName= \<s3_bucket_policy_changes_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ ($.eventSource = s3.amazonaws.com) && (($.eventName = PutBucketAcl) || ($.eventName = PutBucketPolicy) || ($.eventName = PutBucketCors) || ($.eventName = PutBucketLifecycle) || ($.eventName = PutBucketReplication) || ($.eventName = DeleteBucketPolicy) || ($.eventName = DeleteBucketCors) || ($.eventName = DeleteBucketLifecycle) || ($.eventName = DeleteBucketReplication)) }' 

**i) Detect changes to AWS Config configuration**

Create a metric filter based on the filter pattern provided which checks for AWS Configuration changes and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<aws_config_changes_metric\> --metric-transformations metricName= \<aws_config_changes_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ ($.eventSource = config.amazonaws.com) && (($.eventName=StopConfigurationRecorder)||($.eventName=DeleteDeliveryChannel) ||($.eventName=PutDeliveryChannel)||($.eventName=PutConfigurationRecorder)) }' Note: You can choose your own metricName and metricNamespace strings. Using the same metricNamespace for all Foundations Benchmark metrics will group them together. 

**j) Detect changes made to Security Groups**

Create a metric filter based on the filter pattern provided which checks for security groups changes and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<security_group_changes_metric\> --metric-transformations metricName= \<security_group_changes_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ ($.eventName = AuthorizeSecurityGroupIngress) || ($.eventName = AuthorizeSecurityGroupEgress) || ($.eventName = RevokeSecurityGroupIngress) || ($.eventName = RevokeSecurityGroupEgress) || ($.eventName = CreateSecurityGroup) || ($.eventName = DeleteSecurityGroup) }' 

**k) Changes made to NACLs**

Create a metric filter based on the filter pattern provided which checks for NACL changes and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<nacl_changes_metric\> --metric-transformations metricName= \<nacl_changes_metric\> ,metricNamespace='CISBenchmark',metricValue=1 -- filter-pattern '{ ($.eventName = CreateNetworkAcl) || ($.eventName = CreateNetworkAclEntry) || ($.eventName = DeleteNetworkAcl) || ($.eventName = DeleteNetworkAclEntry) || ($.eventName = ReplaceNetworkAclEntry) || ($.eventName = ReplaceNetworkAclAssociation) }' 

**l) Changes to the network**

Create a metric filter based on the filter pattern provided which checks for network gateways changes and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<network_gw_changes_metric\> --metric-transformations metricName= \<network_gw_changes_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ ($.eventName = CreateCustomerGateway) || ($.eventName = DeleteCustomerGateway) || ($.eventName = AttachInternetGateway) || ($.eventName = CreateInternetGateway) || ($.eventName = DeleteInternetGateway) || ($.eventName = DetachInternetGateway) }' 

**m) Detect changes to route tables**

Create a metric filter based on the filter pattern provided which checks for route table changes and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<route_table_changes_metric\> --metric-transformations metricName= \<route_table_changes_metric\> ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ ($.eventName = CreateRoute) || ($.eventName = CreateRouteTable) || ($.eventName = ReplaceRoute) || ($.eventName = ReplaceRouteTableAssociation) || ($.eventName = DeleteRouteTable) || ($.eventName = DeleteRoute) || ($.eventName = DisassociateRouteTable) }'

**n) Detect changes made to VPCs**

Create a metric filter based on filter pattern provided which checks for VPC changes and the \<cloudtrail_log_group_name\> taken from audit step 1. aws logs put-metric-filter --log-group-name \<cloudtrail_log_group_name\> -- filter-name \<vpc_changes_metric\> --metric-transformations metricName= \<vpc_changes_metric\> ,metricNamespace='CISBenchmark',metricValue=1 -- filter-pattern '{ ($.eventName = CreateVpc) || ($.eventName = DeleteVpc) || ($.eventName = ModifyVpcAttribute) || ($.eventName = AcceptVpcPeeringConnection) || ($.eventName = CreateVpcPeeringConnection) || ($.eventName = DeleteVpcPeeringConnection) || ($.eventName = RejectVpcPeeringConnection) || ($.eventName = AttachClassicLinkVpc) || ($.eventName = DetachClassicLinkVpc) || ($.eventName = DisableVpcClassicLink) || ($.eventName = EnableVpcClassicLink) }' 

***Note:*** You can choose your own metricName and metricNamespace strings.

Using the same metricNamespace for all Foundations Benchmark metrics will group them together.

**Step 2.** Create an SNS topic that the alarm will notify aws sns create-topic --name \<sns_topic_name\> 

***Note:*** You can execute this command once and then reuse the same topic for all monitoring alarms.

**Step 3.** Create an SNS subscription to the topic created in step 2 aws sns subscribe --topic-arn \<sns_topic_arn\> --protocol \<protocol_for_sns\> - -notification-endpoint \<sns_subscription_endpoints\> 

***Note:*** You can execute this command once and then reuse the SNS subscription for all monitoring alarms.

**Step 4.** Create an alarm that is associated with the CloudWatch Logs Metric Filter created in step 1 and an SNS topic created in step 2 aws cloudwatch put-metric-alarm --alarm-name \<x1\> -- metric-name \<x2\> --statistic Sum --period 300 --threshold 1 --comparison-operator GreaterThanOrEqualToThreshold --evaluation-periods 1 --namespace 'CISBenchmark' --alarm-actions \<sns_topic_arn\>

Depending on the rule you chose in step 1, continue with that rule now, replacing \<x1\> and \<x2\> as per either:

**a) Detect unauthorized API calls**

\<x1\> = \<unauthorized_api_calls_alarm\>

\<x2\> = \<unauthorized_api_calls_metric\>

**b) Console logins that are not protected by multi-factor authentication (MFA)**

\<x1\> = \<no_mfa_console_signin_alarm\>

\<x2\> = \<no_mfa_console_signin_metric\>

**c) Detect root login attempts**

\<x1\> = \<root_usage_alarm\>

\<x2\> = \<root_usage_metric\>

**d) Changes made to Identity and Access Management (IAM) policies**

\<x1\> = \<iam_changes_alarm\>

\<x2\> = \<iam_changes_metric\>

**e) Changes to CloudTrail&#39;s configurations**

\<x1\> = \<cloudtrail_cfg_changes_alarm\>

\<x2\> = \<cloudtrail_cfg_changes_metric\>

**f) Failed console authentication attempts**

\<x1\> = \<console_signin_failure_alarm\>

\<x2\> = \<console_signin_failure_metric\>

**g) Customer-created CMKs which have changed state to disabled or scheduled deletion**

\<x1\> = \<disable_or_delete_cmk_changes_alarm\>

\<x2\> = \<disable_or_delete_cmk_changes_metric\>

**h) Detect changes to S3 buckets**

\<x1\> = \<s3_bucket_policy_changes_alarm\>

\<x2\> = \<s3_bucket_policy_changes_metric\>

**i) Detect changes to AWS Config configuration**

\<x1\> = \<aws_config_changes_alarm\>

\<x2\> = \<aws_config_changes_metric\>

**j) Detect changes made to Security Groups**

\<x1\> = \<security_group_changes_alarm\>

\<x2\> = \<security_group_changes_metric\>

**k) Changes made to NACLs**

\<x1\> = \<nacl_changes_alarm\>

\<x2\> = \<nacl_changes_metric\>

**l) Changes to the network**

\<x1\> = \<network_gw_changes_alarm\>

\<x2\> = \<network_gw_changes_metric\>

**m) Detect changes to route tables**

\<x1\> = \<route_table_changes_alarm\>

\<x2\> = \<route_table_changes_metric\>

**n) Detect changes made to VPCs**

\<x1\> = \<vpc_changes_alarm\> 

\<x2\> = \<vpc_changes_metric\>

# **Networking**

**Ingress access (4.1 and 4.2)**

**Ensure no security groups allow ingress from 0.0.0.0/0 to port 3389**

Security groups provide stateful filtering of ingress/egress network traffic to AWS resources.

It is recommended that no security group allows unrestricted ingress access to port 3389 and port 22.

Perform the following to remove unrestricted ingress access to port 3389 and port 22 via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console at [https://console.aws.amazon.com/vpc/home](https://console.aws.amazon.com/vpc/home)

**Step 2.** In the left pane, click on _Security Groups_

**Step 3.** For each security group, perform the following:

- Select the security group.
- Click on the _Inbound Rules_ tab.
- Identify the rules to be removed.
- Click on _x_ in the "Remove" column.
- Click on _Save._

**Ensure the default security group of every VPC restricts all traffic (4.3)**

A VPC comes with a default security group whose initial settings:

- Deny all inbound traffic
- Allow all outbound traffic
- Allow all traffic between instances assigned to the security group.

If you don't specify a security group when you launch an instance, the instance is automatically assigned to this default security group. Security groups provide stateful filtering of ingress/egress network traffic to AWS resources.

It is recommended that the default security group restrict all traffic.

The default VPC in every region should have its default security group updated to comply. Any newly created VPCs will automatically contain a default security group that will need remediation to comply with this recommendation.

Security group members can perform the following to implement the prescribed state via the AWS Management Console:

**Step 1.** Identify the AWS resources that exist within the default security group.

**Step 2.** Create a set of least privilege security groups for those resources.

**Step 3.** Place the resources in those security groups.

**Step 4.** Remove the resources noted in #1 from the default security group _Security Group State_

**Step 5.** Log in to the AWS Management Console at [https://console.aws.amazon.com/vpc/home](https://console.aws.amazon.com/vpc/home).

**Step 6.** Repeat the next steps for all VPCs, including the default VPC in each AWS region.

**Step 7.** In the left pane, click on _Security Groups._

**Step 8.** For each default security group, perform the following:

- Select the default security group.
- Click on the _Inbound Rules_ tab.
- Remove any inbound rules.
- Click on the _Outbound Rules_ tab.
- Remove any outbound rules.

**Recommended:** IAM groups allow you to edit the "name" field. After remediating default groups rules for all VPCs in all regions, edit this field to add text similar to "DO NOT USE. DO NOT ADD RULES."

**Ensure routing tables for VPC peering are "least access" (4.4)**

Once a VPC peering connection is established, routing tables must be updated to establish any connections between the peered VPCs. These routes can be as specific as desired. You can even peer a VPC to just a single host on the other side of the connection.

Remove and add route table entries to ensure that the least number of subnets or hosts is required to accomplish the purpose for peering are routable.

To give VPC peering routing tables "least access" via CLI:

**Step 1.** For each \<route_table_id\> containing routes non compliant with your routing policy (which grants more than desired "least access"), delete the non compliant route: aws ec2 delete-route --route-table-id \<route_table_id\> --destination-cidrblock \<non_compliant_destination_CIDR\> 

**Step 2.** Create a new compliant route: aws ec2 create-route --route-table-id \<route_table_id\> --destination-cidrblock \<compliant_destination_CIDR\> --vpc-peering-connection-id \<peering_connection_id\>

# **Other Rules**

**Ensure that GuardDuty enabled for required region**

By enabling GuardDuty, you will receive a threat detection service. This service continuously monitors for malicious activity and unauthorized behavior to protect your AWS accounts, workloads, and data stored in Amazon S3 by event logs analysis.

We are excluding verification for aws-global region from possible regions for GuardDuty.

Perform the following steps under the below link to enable GuardDuty: [https://docs.aws.amazon.com/guardduty/latest/ug/guardduty\_settingup.html](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_settingup.html)

**Ensure there no stale roles with Attached Policies for S3 access**

Avoid stale roles as these could cause access leakage and uncontrolled manipulation with S3 bucket data, which can lead to ransomware violations.

This rule checks inline policies only. The Attached policies are verified under the respective rule.

Perform the following to delete stale roles via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the IAM console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/).

**Step 2.** In the navigation pane of the IAM console, click on _Roles_. Then select the check box next to the role name you want to delete, not the name or row itself.

**Step 3.** For "Role actions" at the top of the page, click on _Delete_.

**Step 4.** In the confirmation dialog box, review the last accessed information to see when each of the selected roles last accessed an AWS service. This helps you to confirm whether the role is currently active. If you want to proceed, click on _Yes, Delete_ to submit the service-linked role for deletion.

**Step 5.** Observe the IAM console notifications to monitor the progress of the service-linked role deletion.

Because the IAM service-linked role deletion is asynchronous, after you submit the role for deletion, the deletion task can succeed or fail.

If the task succeeds, then the role is removed from the list and notification of success appears at the top of the page.

If the task fails, you can click on _View details_ or _View Resources_ from the notifications to learn why the deletion failed. If the deletion fails because the role uses the service's resources, then the notification includes a list of resources, if the service returns that information. You can then clean up the resources and submit the deletion again.

- [https://aws.amazon.com/blogs/security/identify-unused-iam-roles-remove-confidently-last-used-timestamp/](https://aws.amazon.com/blogs/security/identify-unused-iam-roles-remove-confidently-last-used-timestamp/)
- [https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role)

**Ensure all EC2 EBS Volumes has snapshots**

Snapshot availability could prevent data loss and simplify data recovery in the case of data encryption.

Perform the following to create snapshots for EC2 EBS Volumes via the AWS Management Console:

**Step 1.** Log in to the Amazon EC2 console at [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/).

**Step 2.** Click on _Snapshots_ under the Elastic Block Store in the navigation pane.

**Step 3.** Click on _Create Snapshot_.

**Step 4.** For "Select resource type" click on _Volume_.

**Step 5.** For "Volume,"" select the volume.

**Step 6 (Optional).** Enter a description for the snapshot.

**Step 7 (Optional).** Choose _Add Tag_ to add tags to your snapshot. For each tag, provide a tag key and a tag value.

**Step 8.** Click on _Create Snapshot_.

**Ensure all EC2 instances are managed by SSM**

Systems Manager (SSM) helps you maintain security and compliance by scanning your managed instances and reporting on or taking corrective action on any policy violations that it detects.

In cases where SSM is enabled for a specific region, but EC2 instances are not managed, they will not be monitored from any kind of SSM perspective.

Perform the following to configure EC2 instances for use with SMM via the AWS Management Console:

**Step 1.** Open the Amazon EC2 console at [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/).

**Step 2.** In the navigation pane, under "Instances" click on _Instances_.

**Step 3.** Navigate to and choose your EC2 instance from the list.

**Step 4.** In the "Actions" menu, click on _Security_, Modify IAM role.

**Step 5.** For the IAM role, select the instance profile you created for that perspective.

**Ensure that SecurityHub enabled for required region**

AWS Security Hub gives you a comprehensive view of your security alerts and security posture across your AWS accounts. SecurityHub is a cloud security posture management service that automatically checks for best security practices, bundles alerts, and automates remediation.

**AWS security group allows access to known command and control destinations**

The AWS security group allows access to destinations that are known or suspected of being command and control systems used in ransomware and botnet attacks.

Perform the following to gain access to known command and control destinations with the minimum required connectivity via the AWS Management Console:

**Step 1.** Open the Amazon EC2 console at [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/) and click on _Security Groups_.

**Step 2.** For each group found by this rule:

- Select the given rule.
- Click on the &quot;Outbound Rules&quot; tab.
- Click on _Edit outbound rules_.
- Edit any CIDR/IP addresses and minimize the permitted scope to just the minimum required for connectivity.

**Ensure that S3 Bucket has MFA option enabled for changing Bucket Versioning settings and permanently deleting object versions**

S3 Buckets should be protected from ransomware attacks by configuring versioning and MFA Delete. Doing so will disallow immediate bucket content removal, data encryption, or any other harmful modifications.

Disabled versioning is also considered a violation by this rule. The reason for that is that the attacker may make the bucket vulnerable by disabling object versioning with the s3:PutBucketVersioning permission.

Perform the following to enable S3 bucket versioning and MFA Delete via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the S3 console at [https://console.aws.amazon.com/s3](https://console.aws.amazon.com/s3).

**Step 2.** Under "All Buckets,"" click on the target S3 bucket.

**Step 3.** Click on the _Properties_ tab.

**Step 4.** Find the "Bucket Versioning" section.

**Step 5.** Click on _Edit_ and enable versioning.

**Step 6.** Follow the AWS instructions to add the MFA Delete option via CLI or SDK.

**Ensure S3 bucket deny overriding of default KMS Key encryption**

Attackers that have S3:PutObject permission can still override the default KMS Key encryption on updating object operation with their own provided KMS Key, thus leading to a ransomware violation.

To prevent this method of malicious access, the bucket owner should therefore define a policy to allow object modification using only the defined default KMS Key, which attackers are unlikely to have permissions to change or modify.

Perform the following to enable S3 bucket policy control over the KMS Key via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the S3 console at [https://console.aws.amazon.com/s3](https://console.aws.amazon.com/s3).

**Step 2.** In the Buckets list, choose the name of the bucket that you want to create a bucket policy for or whose bucket policy you want to edit.

**Step 3.** Click on _Permissions_.

**Step 4.** Under "Bucket policy,"" click on _Edit_.

**Step 5.** In the "Policy" text field, type or copy and paste a new bucket policy, or edit an existing policy.

The bucket policy is a JSON file. The text you type in the editor must be valid JSON.

**Step 6.** Add policy Condition statement where you define the KMS Key to be matched for Statement with s3:PutObject permission

**Step 7.** Condition example for Allow effect for: "Condition":{"StringEquals":{"s3:x-amz-server-side-encryption-aws-kms-key-id":"arn:aws:kms:REGION:ACCOUNT-ID:key/KEY-ID"}}}

***Note:*** Reference:

[https://rhinosecuritylabs.com/aws/s3-ransomware-part-2-prevention-and-defense/#:~:text=Another%20feature%20of,be%20found%20here](https://rhinosecuritylabs.com/aws/s3-ransomware-part-2-prevention-and-defense/#:~:text=Another%20feature%20of,be%20found%20here).

[https://docs.aws.amazon.com/AmazonS3/latest/userguide/add-bucket-policy.html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/add-bucket-policy.html)

[https://docs.aws.amazon.com/service-authorization/latest/reference/list\_amazons3.html](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazons3.html)

**Ensure that S3 Bucket restrict public access by ACL and policy**

S3 Buckets should not be reachable outside of the project by default. Any violation of this rule could cause major vulnerabilities and data loss

Perform the following to restrict public access to S3 bucket via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the S3 console at [https://console.aws.amazon.com/s3](https://console.aws.amazon.com/s3).

**Step 2.** On the left side menu, find the option "Block Public Access settings for this account.""

**Step 3.** Edit the settings and click on the required level of restriction. The option to "Block all public access" is preferable and will set it globally.

**Step 4.** Open the bucket, then open the tab "Permissions."

**Step 5.** Find the "Access control list (ACL)" part and ensure only the bucket owner has access to it.

**Step 6.** Find "Bucket policy" and check for any public configuration like "principal": * etc. 

***Note:*** Reference: [https://docs.amazonaws.cn/en\_us/AmazonS3/latest/userguide/access-control-block-public-access.html](https://docs.amazonaws.cn/en_us/AmazonS3/latest/userguide/access-control-block-public-access.html)

[https://aws.amazon.com/s3/features/block-public-access/](https://aws.amazon.com/s3/features/block-public-access/)

**Ensure S3 bucket has no server-side encryption being enabled by another account**

S3 bucket policies allow uploaded files to be encrypted with AES256 or a specific AWS KMS key. In the case when a specified KMS Key belongs to a different account, it can lead to a high risk of data encryption without the ability to decrypt it.

Perform the following to clear cross-account KMS Key on S3 via the AWS Management Console:

**Step 1.** Log in to the AWS Management Console and open the S3 console at [https://console.aws.amazon.com/s3](https://console.aws.amazon.com/s3).

**Step 2.** In the "Buckets" list, choose a bucket name.

**Step 3.** Click on _Properties_.

**Step 4.** Under "Default encryption,"" click on _Edit_.

**Step 5.** Remove the cross-account KMS Key and specify the account-related KMS Key for data encryption

**Step 6.** Consider the rule aws_058 aws_s3_prevent_default_kms_key_override.yaml remediation to prevent such violations in the future.