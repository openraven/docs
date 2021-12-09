
**Ensure CloudTrail trails are integrated with CloudWatch Logs**

 AWS CloudTrail is a web service that records AWS API calls made in a given AWS account.
  The recorded information includes the identity of the API caller, the time of the API call, the
  source IP address of the API caller, the request parameters, and the response elements
  returned by the AWS service. CloudTrail uses Amazon S3 for log file storage and delivery,
  so log files are stored durably. In addition to capturing CloudTrail logs within a specified S3
  bucket for long term analysis, realtime analysis can be performed by configuring CloudTrail
  to send logs to CloudWatch Logs. For a trail that is enabled in all regions in an account,
  CloudTrail sends log files from all those regions to a CloudWatch Logs log group. It is
  recommended that CloudTrail logs be sent to CloudWatch Logs

  Perform the following to establish the prescribed state:
  Via the AWS management Console
    1. Sign in to the AWS Management Console and open the CloudTrail console at
    https://console.aws.amazon.com/cloudtrail/
    2. Under All Buckets, click on the target bucket you wish to evaluate
    3. Click Properties on the top right of the console
    4. Click Trails in the left menu
    5. Click on each trail where no CloudWatch Logs are defined
    6. Go to the CloudWatch Logs section and click on Configure
    7. Define a new or select an existing log group
    8. Click on Continue
    9. Configure IAM Role which will deliver CloudTrail events to CloudWatch Logs
      o Create/Select an IAM Role and Policy Name
      o Click Allow to continue
  Via CLI
    aws cloudtrail update-trail --name <trail_name> --cloudwatch-logs-log-grouparn <cloudtrail_log_group_arn> --cloudwatch-logs-role-arn
    <cloudtrail_cloudwatchLogs_role_arn>




**Ensure a support role has been created to manage incidents with AWS Support**
  AWS provides a support center that can be used for incident notification and response, as
  well as technical support and customer services. Create an IAM Role to allow authorized
  users to manage incidents with AWS Support.
  Using the Amazon unified command line interface:
  - Create an IAM role for managing incidents with AWS:
    - Create a trust relationship policy document that allows <iam_user> to
    manage AWS incidents, and save it locally as /tmp/TrustPolicy.json:
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": { "AWS": "arn:aws:iam::321859670049:root" },
          "Action": "sts:AssumeRole"
        }
      ]
    }
    - Create the IAM role using the above trust policy:
    aws iam create-role --role-name <aws_support_iam_role> --assume-rolepolicy-document file:///tmp/TrustPolicy.json
    - Attach 'AWSSupportAccess' managed policy to the created IAM role:
    aws iam attach-role-policy --policy-arn <iam_policy_arn> --role-name <aws_support_iam_role>



**Ensure all EC2 instances are managed by SSM**

  Systems Manager helps you maintain security and compliance by scanning your managed instances
  and reporting on (or taking corrective action on) any policy violations it detects.
  In case if SSM is enabled for specific region but EC2 instances are not managed -
  they will not be monitored from any kind of SSM perspective

  1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.
  2. In the navigation pane, under Instances, choose Instances.
  3. Navigate to and choose your EC2 instance from the list.
  4. In the Actions menu, choose Security, Modify IAM role.
  5. For IAM role, select the instance profile you created for that perspective

**Ensure that SecurityHub enabled for required region**

  AWS Security Hub gives you a comprehensive view of your security alerts and security posture across your AWS accounts.
  SecurityHub is a single place that manages

AWS Security Hub gives you a comprehensive view of your security alerts and security posture across your AWS accounts.
  SecurityHub is a single place that manages


**AWS security group allows access to known command and control destinations**
  
The AWS security group allows access to destinations that are known or suspected of being command and control systems used in ransomware and botnet attacks.
  In the AWS Management Console, go to EC2->Security Groups. For each group found by this rule:
  1) Select the given rule
  2) Choose the 'Outbound Rules' tab
  3) Click 'Edit outbound rules'
  4) Edit any CIDR / IP addresses and minimize the permitted scope to just the minimum required for connectivity.


**Ensure security contact information is registered**

  AWS provides customers with the option of specifying the contact information for
  account's security team. It is recommended that this information be provided.

  Perform the following in the AWS Management Console to establish security contact
  information:
    1. Click on your account name at the top right corner of the console.
    2. From the drop-down menu Click My Account
    3. Scroll down to the Alternate Contacts section
    4. Enter contact information in the Security section
  Note: Consider specifying an internal email distribution list to ensure emails are regularly
  monitored by more than one individual.

**Ensure that S3 Bucket has MFA option enabled for changing Bucket Versioning settings and permanently deleting 
object versions**

  S3 Buckets should be protected from ransomware attacks, by configuring versioning and MFA Delete,
  in order to disallow immediate bucket content removal to encryption, or any other harmful modifications
  Disabled versioning is also considered as violation by this rule since attacker may make the bucket vulnerable
  by disabling object versioning with the s3:PutBucketVersioning permission

  Perform the following to enable S3 bucket versioning and enable mfaDevice
    1. Sign in to the AWS Management Console and open the S3 console at https://console.aws.amazon.com/s3.
    2. Under All Buckets click on the target S3 bucket
    3. Click on Properties tab
    4. Find "Bucket Versioning" section
    5. Click Edit and enable versioning
    6. Follow AWS instruction to add MFA Delete option via CLI or SDK



**Ensure S3 bucket deny overriding of default KMS Key encryption**

  Attackers which have S3:PutObject permission are still able to override default KMS Key encryption
  on updating object operation with their own provided KMS Key, thus lead ransomware violation
  Bucket owner should define policy to allow objects modification only using the defined default KMS Key,
  which attack unlikely have permissions to change or modify

  Perform the following to enable S3 bucket policy control over KMS Key:
  Via the Management Console
    1. Sign in to the AWS Management Console and open the S3 console at https://console.aws.amazon.com/s3.
    2. In the Buckets list, choose the name of the bucket that you want to create a bucket policy for or whose bucket policy you want to edit.
    3. Choose Permissions.
    4. Under Bucket policy, choose Edit.
    5. In the Policy text field, type or copy and paste a new bucket policy, or edit an existing policy.
       The bucket policy is a JSON file. The text you type in the editor must be valid JSON.
    6. Add policy Condition statement where you define the KMS Key to be matched for Statement with s3:PutObject permission
    7. Condition example for Allow effect for:
       "Condition":{"StringEquals":{"s3:x-amz-server-side-encryption-aws-kms-key-id":"arn:aws:kms:REGION:ACCOUNT-ID:key\/KEY-ID"}}}

https://rhinosecuritylabs.com/aws/s3-ransomware-part-2-prevention-and-defense/#:~:text=Another%20feature%20of,be%20found%20here.
https://docs.aws.amazon.com/AmazonS3/latest/userguide/add-bucket-policy.html
https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazons3.html

**Ensure that S3 Bucket restrict public access by ACL and policy**

  S3 Buckets should not be reachable outside of the project by default,
  violation if that rule could cause major vulnerabilities and data loss

  Perform the following to restrict public access to S3 bucket
    1. Sign in to the AWS Management Console and open the S3 console at https://console.aws.amazon.com/s3.
    2. On the left side menu find option "Block Public Access settings for this account"
    3. Edit and click on required level of restriction. Block all public access is preferable. Will set it globally
    4. Open bucket, then open tab "Permissions"
    5. Find "Access control list (ACL)" part and ensure bucket owner is only have access
    6. Find "Bucket policy" check for any public configuration like "principal": * etc
    7. Reference: https://docs.amazonaws.cn/en_us/AmazonS3/latest/userguide/access-control-block-public-access.html
    8. Reference: https://aws.amazon.com/s3/features/block-public-access/



**Ensure S3 bucket has no server-side encryption being enabled by another account**
  S3 bucket policies allow uploaded files to be encrypted with AES256,
  or forcing any uploaded file to be encrypted with a specific AWS KMS key.
  In the case when a specified KMS Key belongs to a different account it leads to a high risk of encryption of the data
  without the ability to decrypt it.
  Perform the following to clear cross-account KMS Key on S3
  Via the Management Console
    1. Sign in to the AWS Management Console and open the S3 console at https://console.aws.amazon.com/s3.
    2. In the Buckets list, choose the name of the bucket that you want.
    3. Choose Properties.
    4. Under Default encryption, choose Edit.
    5. Remove cross-account KMS Key and specify account related KMS Key for data encryption
    6. Consider rule aws_058 aws_s3_prevent_default_kms_key_override.yaml remediation to prevent such violation on future

**Ensure the S3 bucket used to store CloudTrail logs is not publicly accessible**


  CloudTrail logs a record of every API call made in your AWS account. These logs file are
  stored in an S3 bucket. It is recommended that the bucket policy, or access control list
  (ACL), applied to the S3 bucket that CloudTrail logs to prevents public access to the
  CloudTrail logs.

  Perform the following to remove any public access that has been granted to the bucket via
  an ACL or S3 bucket policy:
    1. Go to Amazon S3 console at https://console.aws.amazon.com/s3/home
    2. Right-click on the bucket and click Properties
    3. In the Properties pane, click the Permissions tab.
    4. The tab shows a list of grants, one row per grant, in the bucket ACL. Each row
    identifies the grantee and the permissions granted.
    5. Select the row that grants permission to Everyone or Any Authenticated User
    6. Uncheck all the permissions granted to Everyone or Any Authenticated User (click
    x to delete the row).
    7. Click Save to save the ACL.
    8. If the Edit bucket policy button is present, click it.
    9. Remove any Statement having an Effect set to Allow and a Principal set to "*" or
    {"AWS" : "*"}.


**Ensure S3 bucket access logging is enabled on the CloudTrail S3 bucket**

  S3 Bucket Access Logging generates a log that contains access records for each request
  made to your S3 bucket. An access log record contains details about the request, such as the
  request type, the resources specified in the request worked, and the time and date the
  request was processed. It is recommended that bucket access logging be enabled on the
  CloudTrail S3 bucket.

  Perform the following to enable S3 bucket logging:
  Via the Management Console
    1. Sign in to the AWS Management Console and open the S3 console at https://console.aws.amazon.com/s3.
    2. Under All Buckets click on the target S3 bucket
    3. Click on Properties in the top right of the console
    4. Under Bucket: <s3_bucket_for_cloudtrail> click on Logging
    5. Configure bucket logging
      1. Click on Enabled checkbox
      2. Select Target Bucket from list
      3. Enter a Target Prefix
    6. Click Save


**Ensure rotation for customer created CMKs is enabled**
  AWS Key Management Service (KMS) allows customers to rotate the backing key which is
  key material stored within the KMS which is tied to the key ID of the Customer Created
  customer master key (CMK). It is the backing key that is used to perform cryptographic
  operations such as encryption and decryption. Automated key rotation currently retains all
  prior backing keys so that decryption of encrypted data can take place transparently. It is
  recommended that CMK key rotation be enabled

  Via the Management Console:
    1. Sign in to the AWS Management Console and open the IAM console at
    https://console.aws.amazon.com/iam.
    2. In the left navigation pane, choose Encryption Keys .
    3. Select a customer created master key (CMK)
    4. Under the Key Policy section, move down to Key Rotation .
    5. Check the Rotate this key every year checkbox.
  Via CLI
    1. Run the following command to enable key rotation:
     aws kms enable-key-rotation --key-id <kms_key_id>


**Ensure access keys are rotated every 90 days or less**
  Access keys consist of an access key ID and secret access key, which are used to sign
  programmatic requests that you make to AWS. AWS users need their own access keys to
  make programmatic calls to AWS from the AWS Command Line Interface (AWS CLI), Tools
  for Windows PowerShell, the AWS SDKs, or direct HTTP calls using the APIs for individual
  AWS services. It is recommended that all access keys be regularly rotated.

  Perform the following to rotate access keys:
    1. Login to the AWS Management Console:
    2. Click Services
    3. Click IAM
    4. Click on Users
    5. Click on Security Credentials
    6. As an Administrator
      - Click on Make Inactive for keys that have not been rotated in 90 Days
    7. As an IAM User
      - Click on Make Inactive or Delete for keys which have not been rotated or used in 90 Days
    8. Click on Create Access Key
    9. Update programmatic call with new Access Key credentials

  Or Via CLI:
    aws iam update-access-key
    aws iam create-access-key
    aws iam delete-access-key


**Ensure routing tables for VPC peering are "least access"**

Once a VPC peering connection is established, routing tables must be updated to establish
  any connections between the peered VPCs. These routes can be as specific as desired - even
  peering a VPC to only a single host on the other side of the connection.
  
Remove and add route table entries to ensure that the least number of subnets or hosts as
  is required to accomplish the purpose for peering are routable.
  Via CLI:
    1. For each <route_table_id> containing routes non compliant with your routing policy
    (which grants more than desired "least access"), delete the non compliant route:
      aws ec2 delete-route --route-table-id <route_table_id> --destination-cidrblock <non_compliant_destination_CIDR>
    2. Create a new compliant route:
      aws ec2 create-route --route-table-id <route_table_id> --destination-cidrblock <compliant_destination_CIDR> --vpc-peering-connection-id
      <peering_connection_id>

**Ensure no security groups allow ingress from 0.0.0.0/0 to port 3389**

  Security groups provide stateful filtering of ingress/egress network traffic to AWS
  resources. It is recommended that no security group allows unrestricted ingress access to
  port 3389.

 Perform the following to implement the prescribed state:
    1. Login to the AWS Management Console at
    https://console.aws.amazon.com/vpc/home
    2. In the left pane, click Security Groups
    3. For each security group, perform the following:
    4. Select the security group
    5. Click the Inbound Rules tab
    6. Identify the rules to be removed
    7. Click the x in the Remove column
    8. Click Save



**Ensure no security groups allow ingress from 0.0.0.0/0 to port 22**
  
Security groups provide stateful filtering of ingress/egress network traffic to AWS
  resources. It is recommended that no security group allows unrestricted ingress access to
  port 22.

  Perform the following to implement the prescribed state:
    1. Login to the AWS Management Console at
    https://console.aws.amazon.com/vpc/home
    2. In the left pane, click Security Groups
    3. For each security group, perform the following:
    4. Select the security group
    5. Click the Inbound Rules tab
    6. Identify the rules to be removed
    7. Click the x in the Remove column
    8. Click Save


**Ensure no root account access key exists**
  The root account is the most privileged user in an AWS account. AWS Access Keys provide
  programmatic access to a given AWS account. It is recommended that all access keys
  associated with the root account be removed.

  1. Sign in to the AWS Management Console as Root and open the IAM console at
    https://console.aws.amazon.com/iam/.
  2. Click on <Root_Account_Name> at the top right and select Security Credentials
    from the drop down list
  3. On the pop out screen Click on Continue to Security Credentials
  4. Click on Access Keys (Access Key ID and Secret Access Key)
  5. Under the Status column if there are any Keys which are Active
    1. Click on Make Inactive - (Temporarily disable Key - may be needed again)
    2. Click Delete - (Deleted keys cannot be recovered)


**Ensure IAM policies that allow full `````"*:*"````` administrative privileges are not created**
  
  IAM policies are the means by which privileges are granted to users, groups, or roles. It is
  recommended and considered a standard security advice to grant least privilege—that is,
  granting only the permissions required to perform a task. Determine what users need to do
  and then craft policies for them that let the users perform only those tasks, instead of
  allowing full administrative privileges.

 Using the GUI, perform the following to detach the policy that has full administrative
  privileges:
    1. Sign in to the AWS Management Console and open the IAM console at
    https://console.aws.amazon.com/iam/.
    2. In the navigation pane, click Policies and then search for the policy name found in
    the audit step.
    3. Select the policy that needs to be deleted.
    4. In the policy action menu, select first Detach
    5. Select all Users, Groups, Roles that have this policy attached
    6. Click Detach Policy
    7. In the policy action menu, select Detach

  Using the CLI, perform the following to detach the policy that has full administrative
  privileges as found in the audit step:
    1. Lists all IAM users, groups, and roles that the specified managed policy is attached to.
     aws iam list-entities-for-policy --policy-arn <policy_arn>
    2. Detach the policy from all IAM Users:
     aws iam detach-user-policy --user-name <iam_user> --policy-arn <policy_arn>
    3. Detach the policy from all IAM Groups:
     aws iam detach-group-policy --group-name <iam_group> --policy-arn <policy_arn>
    4. Detach the policy from all IAM Roles:
     aws iam detach-role-policy --role-name <iam_role> --policy-arn <policy_arn>

**Do not setup access keys during initial user setup for all IAM users
  that have a console password**

  AWS console defaults the checkbox for creating access keys to enabled. This results in
  many access keys being generated unnecessarily. In addition to unnecessary credentials, it
  also generates unnecessary management work in auditing and rotating these keys.

  Perform the following to delete access keys that do not pass the audit:
    1. Login to the AWS Management Console:
    2. Click Services
    3. Click IAM
    4. Click on Users
    5. Click on Security Credentials
    6. As an Administrator
      - Click on Delete for keys that were created at the same time as the user profile but
      have not been used.
    7. As an IAM User
      - Click on Delete for keys that were created at the same time as the user profile but
      have not been used.
  Via CLI
    aws iam delete-access-key


**Ensure multi-factor authentication (MFA) is enabled for all IAM users
  that have a console password**

  Multi-Factor Authentication (MFA) adds an extra layer of protection on top of a user name
  and password. With MFA enabled, when a user signs in to an AWS website, they will be
  prompted for their user name and password as well as for an authentication code from
  their AWS MFA device. It is recommended that MFA be enabled for all accounts that have a
  console password.

  Perform the following to enable MFA:
    1. Sign in to the AWS Management Console and open the IAM console at https://console.aws.amazon.com/iam/.
    2. In the navigation pane, choose Users.
    3. In the User Name list, choose the name of the intended MFA user.
    4. Choose the Security Credentials tab, and then choose Manage MFA Device.
    5. In the Manage MFA Device wizard, choose A virtual MFA device, and then choose Next Step.


**Ensure MFA is enabled for the "root" account**
  The root account is the most privileged user in an AWS account. MFA adds an extra layer of
  protection on top of a user name and password. With MFA enabled, when a user signs in to
  an AWS website, they will be prompted for their user name and password as well as for an
  authentication code from their AWS MFA device.

  Perform the following to establish MFA for the root account:
    1. Sign in to the AWS Management Console and open the IAM console at
    https://console.aws.amazon.com/iam/.
      Note: to manage MFA devices for the root AWS account, you must use your root account
      credentials to sign in to AWS. You cannot manage MFA devices for the root account using
      other credentials.
    2. Choose Dashboard , and under Security Status , expand Activate MFA on your root
    account.
    3. Choose Activate MFA
    4. In the wizard, choose A virtual MFA device and then choose Next Step .
    5. IAM generates and displays configuration information for the virtual MFA device,
    including a QR code graphic. The graphic is a representation of the 'secret configuration
    key' that is available for manual entry on devices that do not support QR codes.
    6. Open your virtual MFA application. (For a list of apps that you can use for hosting virtual
    MFA devices, see Virtual MFA Applications.) If the virtual MFA application supports
    multiple accounts (multiple virtual MFA devices), choose the option to create a new
    account (a new virtual MFA device).
    7. Determine whether the MFA app supports QR codes, and then do one of the following:
       Use the app to scan the QR code. For example, you might choose the camera icon or
      choose an option similar to Scan code, and then use the device's camera to scan the
      code.
       In the Manage MFA Device wizard, choose Show secret key for manual
      configuration, and then type the secret configuration key into your MFA application.
      When you are finished, the virtual MFA device starts generating one-time passwords.
      1. In the Manage MFA Device wizard, in the Authentication Code 1 box, type the onetime password that currently appears in the virtual MFA device. Wait up to 30
      seconds for the device to generate a new one-time password. Then type the second
      one-time password into the Authentication Code 2 box. Choose Active Virtual MFA.

**Ensure a log metric filter and alarm exist for VPC changes**
  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. It is possible to
  have more than 1 VPC within an account, in addition it is also possible to create a peer
  connection between 2 VPCs enabling network traffic to route between VPCs. It is
  recommended that a metric filter and alarm be established for changes made to VPCs.

  Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for VPC changes
    and the <cloudtrail_log_group_name> taken from audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<vpc_changes_metric>` --metric-transformations metricName=
      `<vpc_changes_metric>` ,metricNamespace='CISBenchmark',metricValue=1 --
      filter-pattern '{ ($.eventName = CreateVpc) || ($.eventName = DeleteVpc) ||
      ($.eventName = ModifyVpcAttribute) || ($.eventName =
      AcceptVpcPeeringConnection) || ($.eventName = CreateVpcPeeringConnection) ||
      ($.eventName = DeleteVpcPeeringConnection) || ($.eventName =
      RejectVpcPeeringConnection) || ($.eventName = AttachClassicLinkVpc) ||
      ($.eventName = DetachClassicLinkVpc) || ($.eventName = DisableVpcClassicLink)
      || ($.eventName = EnableVpcClassicLink) }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name `<vpc_changes_alarm>` --
      metric-name `<vpc_changes_metric>` --statistic Sum --period 300 --threshold
      1 --comparison-operator GreaterThanOrEqualToThreshold --evaluation-periods 1
      --namespace 'CISBenchmark' --alarm-actions <sns_topic_arn>

**Ensure a log metric filter and alarm exist for unauthorized API calls**
  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. It is
  recommended that a metric filter and alarm be established for unauthorized API calls.

  Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for
    unauthorized API calls and the <cloudtrail_log_group_name> taken from audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<unauthorized_api_calls_metric>` --metric-transformations
      metricName= `<unauthorized_api_calls_metric>` 
      ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{
      ($.errorCode = "*UnauthorizedOperation") || ($.errorCode = "AccessDenied*")
      }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
    -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name
      `<unauthorized_api_calls_alarm>` --metric-name
      `<unauthorized_api_calls_metric>` --statistic Sum --period 300 --threshold 1
      --comparison-operator GreaterThanOrEqualToThreshold --evaluation-periods 1 --
      namespace 'CISBenchmark' --alarm-actions <sns_topic_arn>


**Ensure a log metric filter and alarm exist for security group changes**

  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. Security
  Groups are a stateful packet filter that controls ingress and egress traffic within a VPC. It is
  recommended that a metric filter and alarm be established changes made to Security Groups.

  Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for security
    groups changes and the <cloudtrail_log_group_name> taken from audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<security_group_changes_metric>` --metric-transformations
      metricName= `<security_group_changes_metric>`
      ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ 
      ($.eventName = AuthorizeSecurityGroupIngress) || ($.eventName =
      AuthorizeSecurityGroupEgress) || ($.eventName = RevokeSecurityGroupIngress)
      || ($.eventName = RevokeSecurityGroupEgress) || ($.eventName =
      CreateSecurityGroup) || ($.eventName = DeleteSecurityGroup) }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name
      `<security_group_changes_alarm>` --metric-name
      `<security_group_changes_metric>` --statistic Sum --period 300 --threshold 1
      --comparison-operator GreaterThanOrEqualToThreshold --evaluation-periods 1 --
      namespace 'CISBenchmark' --alarm-actions <sns_topic_arn>



**Ensure a log metric filter and alarm exist for S3 bucket policy changes**

  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. It is
  recommended that a metric filter and alarm be established for changes to S3 bucket

  Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for S3 bucket
    policy changes and the <cloudtrail_log_group_name> taken from audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<s3_bucket_policy_changes_metric>` --metric-transformations 
      metricName= `<s3_bucket_policy_changes_metric>`
      ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{
      ($.eventSource = s3.amazonaws.com) && (($.eventName = PutBucketAcl) ||
      ($.eventName = PutBucketPolicy) || ($.eventName = PutBucketCors) ||
      ($.eventName = PutBucketLifecycle) || ($.eventName = PutBucketReplication) ||
      ($.eventName = DeleteBucketPolicy) || ($.eventName = DeleteBucketCors) ||
      ($.eventName = DeleteBucketLifecycle) || ($.eventName =
      DeleteBucketReplication)) }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name
      `<s3_bucket_policy_changes_alarm>` --metric-name
      `<s3_bucket_policy_changes_metric>` --statistic Sum --period 300 --threshold
      1 --comparison-operator GreaterThanOrEqualToThreshold --evaluation-periods 1
      --namespace 'CISBenchmark' --alarm-actions <sns_topic_arn>


**Ensure a log metric filter and alarm exist for route table changes**

Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. Routing tables
  are used to route network traffic between subnets and to network gateways. It is
  recommended that a metric filter and alarm be established for changes to route tables.

 Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for route table
    changes and the <cloudtrail_log_group_name> taken from audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<route_table_changes_metric>` --metric-transformations
      metricName= `<route_table_changes_metric>`
      ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ 
      ($.eventName = CreateRoute) || ($.eventName = CreateRouteTable) ||
      ($.eventName = ReplaceRoute) || ($.eventName = ReplaceRouteTableAssociation)
      || ($.eventName = DeleteRouteTable) || ($.eventName = DeleteRoute) ||
      ($.eventName = DisassociateRouteTable) }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name `<route_table_changes_alarm>`
      --metric-name `<route_table_changes_metric>` --statistic Sum --period 300 -
      -threshold 1 --comparison-operator GreaterThanOrEqualToThreshold --
      evaluation-periods 1 --namespace 'CISBenchmark' --alarm-actions
      <sns_topic_arn>


**Ensure a log metric filter and alarm exist for usage of "root" account**
  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. It is
  recommended that a metric filter and alarm be established for root login attempts.
  
Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for "Root"
    account usage and the <cloudtrail_log_group_name> taken from audit step 1.
      aws logs put-metric-filter --log-group-name `<cloudtrail_log_group_name>` --
      filter-name `<root_usage_metric>` --metric-transformations metricName=
      `<root_usage_metric>` ,metricNamespace='CISBenchmark',metricValue=1 --filterpattern '{ $.userIdentity.type = "Root" && $.userIdentity.invokedBy NOT
      EXISTS && $.eventType != "AwsServiceEvent" }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name `<root_usage_alarm>` --metricname `<root_usage_metric>` --statistic Sum --period 300 --threshold 1 --
      comparison-operator GreaterThanOrEqualToThreshold --evaluation-periods 1 --
      namespace 'CISBenchmark' --alarm-actions <sns_topic_arn>


**Ensure a log metric filter and alarm exist for changes to Network Access Control Lists (NACL)**
 Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. NACLs are used
  as a stateless packet filter to control ingress and egress traffic for subnets within a VPC. It is
  recommended that a metric filter and alarm be established for changes made to NACLs.

  Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for NACL
    changes and the <cloudtrail_log_group_name> taken from audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<nacl_changes_metric>` --metric-transformations metricName=
      `<nacl_changes_metric>` ,metricNamespace='CISBenchmark',metricValue=1 --
      filter-pattern '{ ($.eventName = CreateNetworkAcl) || ($.eventName = 
      CreateNetworkAclEntry) || ($.eventName = DeleteNetworkAcl) || ($.eventName =
      DeleteNetworkAclEntry) || ($.eventName = ReplaceNetworkAclEntry) ||
      ($.eventName = ReplaceNetworkAclAssociation) }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name `<nacl_changes_alarm>` --
      metric-name `<nacl_changes_metric>` --statistic Sum --period 300 --
      threshold 1 --comparison-operator GreaterThanOrEqualToThreshold --evaluationperiods 1 --namespace 'CISBenchmark' --alarm-actions <sns_topic_arn>



**Ensure a log metric filter and alarm exist for IAM policy changes**

  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. It is
  recommended that a metric filter and alarm be established changes made to Identity and
  Access Management (IAM) policies.

  Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for IAM policy
    changes and the <cloudtrail_log_group_name> taken from audit step 1.
      aws logs put-metric-filter --log-group-name `<cloudtrail_log_group_name>` --
      filter-name `<iam_changes_metric>` --metric-transformations metricName=
      `<iam_changes_metric>` ,metricNamespace='CISBenchmark',metricValue=1 --
      filter-pattern
      '{($.eventName=DeleteGroupPolicy)||($.eventName=DeleteRolePolicy)||($.eventNa
      me=DeleteUserPolicy)||($.eventName=PutGroupPolicy)||($.eventName=PutRolePolic
      y)||($.eventName=PutUserPolicy)||($.eventName=CreatePolicy)||($.eventName=Del
      etePolicy)||($.eventName=CreatePolicyVersion)||($.eventName=DeletePolicyVersi
      on)||($.eventName=AttachRolePolicy)||($.eventName=DetachRolePolicy)||($.event
      Name=AttachUserPolicy)||($.eventName=DetachUserPolicy)||($.eventName=AttachGr
      oupPolicy)||($.eventName=DetachGroupPolicy)}'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name `<iam_changes_alarm>` --
      metric-name `<iam_changes_metric>` --statistic Sum --period 300 --threshold
      1 --comparison-operator GreaterThanOrEqualToThreshold --evaluation-periods 1
      --namespace 'CISBenchmark' --alarm-actions <sns_topic_arn>

**Ensure a log metric filter and alarm exist for changes to network gateways**

 Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. Network
  gateways are required to send/receive traffic to a destination outside of a VPC. It is
  recommended that a metric filter and alarm be established for changes to network

  Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for network
    gateways changes and the <cloudtrail_log_group_name> taken from audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<network_gw_changes_metric>` --metric-transformations
      metricName= `<network_gw_changes_metric>`
      ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{ 
      ($.eventName = CreateCustomerGateway) || ($.eventName =
      DeleteCustomerGateway) || ($.eventName = AttachInternetGateway) ||
      ($.eventName = CreateInternetGateway) || ($.eventName =
      DeleteInternetGateway) || ($.eventName = DetachInternetGateway) }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name `<network_gw_changes_alarm>` -
      -metric-name `<network_gw_changes_metric>` --statistic Sum --period 300 --
      threshold 1 --comparison-operator GreaterThanOrEqualToThreshold --evaluationperiods 1 --namespace 'CISBenchmark' --alarm-actions <sns_topic_arn>

**Ensure a log metric filter and alarm exist for disabling or scheduled deletion of customer created CMKs**
  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. It is
  recommended that a metric filter and alarm be established for customer created CMKs
  which have changed state to disabled or scheduled deletion.

Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for disabled or
    scheduled for deletion CMK's and the <cloudtrail_log_group_name> taken from
    audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<disable_or_delete_cmk_changes_metric>` --metrictransformations metricName= `<disable_or_delete_cmk_changes_metric>`
      ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern 
      '{($.eventSource = kms.amazonaws.com) &&
      (($.eventName=DisableKey)||($.eventName=ScheduleKeyDeletion)) }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name
      `<disable_or_delete_cmk_changes_alarm>` --metric-name
      `<disable_or_delete_cmk_changes_metric>` --statistic Sum --period 300 --
      threshold 1 --comparison-operator GreaterThanOrEqualToThreshold --evaluationperiods 1 --namespace
   'CISBenchmark' --alarm-actions <sns_topic_arn>


**Ensure a log metric filter and alarm exist for Management Console sign-in without MFA**
  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. It is
  recommended that a metric filter and alarm be established for console logins that are not
  protected by multi-factor authentication (MFA).
 
Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for AWS
    Management Console sign-in without MFA and the <cloudtrail_log_group_name>
    taken from audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<no_mfa_console_signin_metric>` --metric-transformations 
      metricName= `<no_mfa_console_signin_metric>`
      ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{
      ($.eventName = "ConsoleLogin") && ($.additionalEventData.MFAUsed != "Yes") }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name `<no_mfa_console_signin_alarm>`
      --metric-name `<no_mfa_console_signin_metric>` --statistic Sum --period 300
      --threshold 1 --comparison-operator GreaterThanOrEqualToThreshold --
      evaluation-periods 1 --namespace 'CISBenchmark' --alarm-actions
      <sns_topic_arn>



**Ensure a log metric filter and alarm exist for CloudTrail configuration changes**
  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. It is
  recommended that a metric filter and alarm be established for detecting changes to
  CloudTrail's configurations.

  Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for cloudtrail
    configuration changes and the <cloudtrail_log_group_name> taken from audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<cloudtrail_cfg_changes_metric>` --metric-transformations
      metricName= `<cloudtrail_cfg_changes_metric>`
      ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{
      ($.eventName = CreateTrail) || ($.eventName = UpdateTrail) || ($.eventName =
      DeleteTrail) || ($.eventName = StartLogging) || ($.eventName = StopLogging)
      }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name
      `<cloudtrail_cfg_changes_alarm>` --metric-name
      `<cloudtrail_cfg_changes_metric>` --statistic Sum --period 300 --threshold 1
      --comparison-operator GreaterThanOrEqualToThreshold --evaluation-periods 1 --
      namespace 'CISBenchmark' --alarm-actions <sns_topic_arn>


**Ensure a log metric filter and alarm exist for AWS Management Console authentication failures**
  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. It is
  recommended that a metric filter and alarm be established for failed console
  authentication attempts.

  Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for AWS
    management Console Login Failures and the <cloudtrail_log_group_name> taken
    from audit step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<console_signin_failure_metric>` --metric-transformations
      metricName= `<console_signin_failure_metric>` 
      ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{
      ($.eventName = ConsoleLogin) && ($.errorMessage = "Failed authentication") }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to the topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name
      `<console_signin_failure_alarm>` --metric-name
      `<console_signin_failure_metric>` --statistic Sum --period 300 --threshold 1
      --comparison-operator GreaterThanOrEqualToThreshold --evaluation-periods 1 --
      namespace 'CISBenchmark' --alarm-actions <sns_topic_arn

**Ensure a log metric filter and alarm exist for AWS Config configuration changes**

  Real-time monitoring of API calls can be achieved by directing CloudTrail Logs to
  CloudWatch Logs and establishing corresponding metric filters and alarms. It is
  recommended that a metric filter and alarm be established for detecting changes to
  CloudTrail's configurations.

Perform the following to setup the metric filter, alarm, SNS topic, and subscription:
    1. Create a metric filter based on filter pattern provided which checks for AWS
    Configuration changes and the <cloudtrail_log_group_name> taken from audit
    step 1.
      aws logs put-metric-filter --log-group-name <cloudtrail_log_group_name> --
      filter-name `<aws_config_changes_metric>` --metric-transformations
      metricName= `<aws_config_changes_metric>`
      ,metricNamespace='CISBenchmark',metricValue=1 --filter-pattern '{
      ($.eventSource = config.amazonaws.com) &&
      (($.eventName=StopConfigurationRecorder)||($.eventName=DeleteDeliveryChannel)
      ||($.eventName=PutDeliveryChannel)||($.eventName=PutConfigurationRecorder))
      }'
    Note: You can choose your own metricName and metricNamespace strings. Using the same
    metricNamespace for all Foundations Benchmark metrics will group them together.
    2. Create an SNS topic that the alarm will notify
      aws sns create-topic --name <sns_topic_name>
    Note: you can execute this command once and then re-use the same topic for all
    monitoring alarms.
    3. Create an SNS subscription to topic created in step 2
      aws sns subscribe --topic-arn <sns_topic_arn> --protocol <protocol_for_sns> -
      -notification-endpoint <sns_subscription_endpoints>
    Note: you can execute this command once and then re-use the SNS subscription for all
    monitoring alarms.
    4. Create an alarm that is associated with the CloudWatch Logs Metric Filter created in
    step 1 and an SNS topic created in step 2
      aws cloudwatch put-metric-alarm --alarm-name `<aws_config_changes_alarm>` -
      -metric-name `<aws_config_changes_metric>` --statistic Sum --period 300 --
      threshold 1 --comparison-operator GreaterThanOrEqualToThreshold --evaluationperiods 1 --namespace 'CISBenchmark' --alarm-actions <sns_topic_arn>
# remediationDocURLs:



**Maintain current contact details**
  Ensure contact email and telephone details for AWS accounts are current and map to more
  than one individual in your organization.
  An AWS account supports a number of contact details, and AWS will use these to contact
  the account owner if activity judged to be in breach of Acceptable Use Policy or indicative
  of likely security compromise is observed by the AWS Abuse team. Contact details should
  not be for a single individual, as circumstances may arise where that individual is
  unavailable. Email contact details should point to a mail alias which forwards email to
  multiple individuals within the organization; where feasible, phone contact details should
  point to a PBX hunt group or other call-forwarding system.

This activity can only be performed via the AWS Console, with a user who has permission
  to read and write Billing information (aws-portal:*Billing ).
    - Sign in to the AWS Management Console and open the Billing and Cost Management
      console at https://console.aws.amazon.com/billing/home#/.
    - On the navigation bar, choose your account name, and then choose My Account.
    - On the Account Settings page, next to Account Settings, choose Edit.
    - Next to the field that you need to update, choose Edit.
    - After you have entered your changes, choose Save changes.
    - After you have made your changes, choose Done.
    - To edit your contact information, under Contact Information, choose Edit.
    - For the fields that you want to change, type your updated information, and then
      choose Update.


**Ensure there no stale roles with Attached Policies for S3 access**
  Avoid stale roles which could cause access leakage and uncontrolled manipulation with S3 bucket data
  which increase risks and leads to Ransomware violations.
  This rule checks inline policies only. The Attached policies verified under the respective rule.

  1. Sign in to the AWS Management Console and open the IAM console at https://console.aws.amazon.com/iam/.
  2. In the navigation pane of the IAM console, choose Roles. Then select the check box next to the role name that you want to delete, not the name or row itself.
  3. For Role actions at the top of the page, choose Delete.
  4. In the confirmation dialog box, review the last accessed information, which shows when each of the selected roles last accessed an AWS service.
      This helps you to confirm whether the role is currently active. If you want to proceed, choose Yes, Delete to submit the service-linked role for deletion.
  5. Watch the IAM console notifications to monitor the progress of the service-linked role deletion.
    Because the IAM service-linked role deletion is asynchronous, after you submit the role for deletion, the deletion task can succeed or fail.
    If the task succeeds, then the role is removed from the list and a notification of success appears at the top of the page.
    If the task fails, you can choose View details or View Resources from the notifications to learn why the deletion failed.
    If the deletion fails because the role is using the service's resources, then the notification includes a list of resources, if the service returns that information.
    You can then clean up the resources and submit the deletion again.
  - https://aws.amazon.com/blogs/security/identify-unused-iam-roles-remove-confidently-last-used-timestamp/
   - https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role

  Avoid stale roles which could cause access leakage and uncontrolled manipulation with S3 bucket data
  which increase risks and leads to Ransomware violations.
  This rule checks Attached policies only. The Inline policies verified under the respective rule.

  1. Sign in to the AWS Management Console and open the IAM console at https://console.aws.amazon.com/iam/.
  2. In the navigation pane of the IAM console, choose Roles. Then select the check box next to the role name that you want to delete, not the name or row itself.
  3. For Role actions at the top of the page, choose Delete.
  4. In the confirmation dialog box, review the last accessed information, which shows when each of the selected roles last accessed an AWS service.
      This helps you to confirm whether the role is currently active. If you want to proceed, choose Yes, Delete to submit the service-linked role for deletion.
  5. Watch the IAM console notifications to monitor the progress of the service-linked role deletion.
    Because the IAM service-linked role deletion is asynchronous, after you submit the role for deletion, the deletion task can succeed or fail.
    If the task succeeds, then the role is removed from the list and a notification of success appears at the top of the page.
    If the task fails, you can choose View details or View Resources from the notifications to learn why the deletion failed.
    If the deletion fails because the role is using the service's resources, then the notification includes a list of resources, if the service returns that information.
    You can then clean up the resources and submit the deletion again.

**Ensure IAM instance roles are used for AWS resource access from instances**
  AWS access from within AWS instances can be done by either encoding AWS keys into AWS
  API calls or by assigning the instance to a role which has an appropriate permissions policy
  for the required access. "AWS Access" means accessing the APIs of AWS in order to access
  AWS resources or manage AWS account resources.

  IAM roles can only be associated at the launch of an instance. To remediate an instance to
  add it to a role you must create a new instance.
  If the instance has no external dependencies on its current private ip or public addresses
  are elastic IPs:
    1. In AWS IAM create a new role. Assign a permissions policy if needed permissions
    are already known.
    2. In the AWS console launch a new instance with identical settings to the existing
    instance, and ensure that the newly created role is selected.
    3. Shutdown both the existing instance and the new instance.
    4. Detach disks from both instances.
    5. Attach the existing instance disks to the new instance.
    6. Boot the new instance and you should have the same machine, but with the
    associated role.
  Note: if your environment has dependencies on a dynamically assigned PRIVATE IP
  address you can create an AMI from the existing instance, destroy the old one and then
  when launching from the AMI, manually assign the previous private IP address.
  Note: if your environment has dependencies on a dynamically assigned PUBLIC IP address
  there is not a way ensure the address is retained and assign an instance role. Dependencies
  on dynamically assigned public IP addresses are a bad practice and, if possible, you may
  wish to rebuild the instance with a new elastic IP address and make the investment to
  remediate affected systems while assigning the system to a role.

**Ensure IAM policies are attached only to groups or roles**
  By default, IAM users, groups, and roles have no access to AWS resources. IAM policies are
  the means by which privileges are granted to users, groups, or roles. It is recommended
  that IAM policies be applied directly to groups and roles but not users.
Perform the following to create an IAM group and assign a policy to it
    1. Sign in to the AWS Management Console and open the IAM console at
    https://console.aws.amazon.com/iam/.
    2. In the navigation pane, click Groups and then click Create New Group .
    3. In the Group Name box, type the name of the group and then click Next Step .
    4. In the list of policies, select the check box for each policy that you want to apply to
    all members of the group. Then click Next Step .
    5. Click Create Group
  Perform the following to add a user to a given group
    1. Sign in to the AWS Management Console and open the IAM console at
    https://console.aws.amazon.com/iam/.
    2. In the navigation pane, click Groups
    3. Select the group to add a user to
    4. Click Add Users To Group
    5. Select the users to be added to the group
    6. Click Add Users
  Perform the following to remove a direct association between a user and policy
    1. Sign in to the AWS Management Console and open the IAM console at
    https://console.aws.amazon.com/iam/.
    2. In the left navigation pane, click on Users
    3. For each user
      1. Select the user
      2. Click on the Permissions tab
      3. Expand Managed Policies
      4. Click Detach Policy for each policy
      5. Expand Inline Policies
      6. Click Remove Policy for each policy



**Ensure IAM password policy requires at least one uppercase letter**
  Password policies are, in part, used to enforce password complexity requirements. IAM
  password policies can be used to ensure password are comprised of different character
  sets. It is recommended that the password policy require at least one uppercase letter
  
Perform the following to set the password policy as prescribed:
    Via AWS Console
    1. Login to AWS Console (with appropriate permissions to View Identity Access Management Account Settings)
    2. Go to IAM Service on the AWS Console
    3. Click on Account Settings on the Left Pane
    4. Check "Requires at least one uppercase letter"
    5. Click "Apply password policy"
  Via CLI
    aws iam update-account-password-policy --require-uppercase-characters
  Note: All commands starting with "aws iam update-account-password-policy" have be
     combined into a single command in order to all of them to take effect


**Ensure IAM password policy requires minimum length of 14 or greater**

  Password policies are, in part, used to enforce password complexity requirements. IAM
  password policies can be used to ensure password are at least a given length. It is
  recommended that the password policy require a minimum password length 14 

Perform the following to set the password policy as prescribed:
  Via AWS Console
    1. Login to AWS Console (with appropriate permissions to View Identity Access
    Management Account Settings)
    2. Go to IAM Service on the AWS Console
    3. Click on Account Settings on the Left Pane
    4. Set "Minimum password length" to 14 or greater.
    5. Click "Apply password policy"
  Via CLI
    aws iam update-account-password-policy --minimum-password-length 14
  Note: All commands starting with "aws iam update-account-password-policy" have be
    combined into a single command in order to all of them to take effect

**Ensure IAM password policy requires at least one lowercase letter**

  Password policies are, in part, used to enforce password complexity requirements. IAM
  password policies can be used to ensure password are comprised of different character
  sets. It is recommended that the password policy require at least one lowercase letter

  Perform the following to set the password policy as prescribed:
  Via the AWS Console
    1. Login to AWS Console (with appropriate permissions to View Identity Access Management Account Settings)
    2. Go to IAM Service on the AWS Console
    3. Click on Account Settings on the Left Pane
    4. Check "Requires at least one lowercase letter"
    5. Click "Apply password policy"
  Via CLI
    aws iam update-account-password-policy --require-lowercase-characters
  Note: All commands starting with "aws iam update-account-password-policy" have be
     combined into a single command in order to all of them to take effect

**Ensure IAM password policy require at least one symbol**
Password policies are, in part, used to enforce password complexity requirements. IAM
  password policies can be used to ensure password are comprised of different character
  sets. It is recommended that the password policy require at least one symbol

  Perform the following to set the password policy as prescribed:
  Via AWS Console
    1. Login to AWS Console (with appropriate permissions to View Identity Access
    Management Account Settings)
    2. Go to IAM Service on the AWS Console
    3. Click on Account Settings on the Left Pane
    4. Check "Require at least one non-alphanumeric character"
    5. Click "Apply password policy"
  Via CLI
    aws iam update-account-password-policy --require-symbols
  Note: All commands starting with "aws iam update-account-password-policy" have be
     combined into a single command in order to all of them to take effect

**Ensure IAM password policy require at least one number**
 Password policies are, in part, used to enforce password complexity requirements. IAM
  password policies can be used to ensure password are comprised of different character
  sets. It is recommended that the password policy require at least one number

 Perform the following to set the password policy as prescribed:
  Via AWS Console
    1. Login to AWS Console (with appropriate permissions to View Identity Access
    Management Account Settings)
    2. Go to IAM Service on the AWS Console
    3. Click on Account Settings on the Left Pane
    4. Check "Require at least one number"
    5. Click "Apply password policy"
  Via CLI
   aws iam update-account-password-policy --require-numbers
  Note: All commands starting with "aws iam update-account-password-policy" have be
     combined into a single command in order to all of them to take effect

**Ensure IAM password policy prevents password reuse**
  IAM password policies can prevent the reuse of a given password by the same user. It is
  recommended that the password policy prevent the reuse of passwords

  Perform the following to set the password policy as prescribed
  Via AWS Console
    1. Login to AWS Console (with appropriate permissions to View Identity Access
    Management Account Settings)
    2. Go to IAM Service on the AWS Console
    3. Click on Account Settings on the Left Pane
    4. Check "Prevent password reuse"
    5. Set "Number of passwords to remember" is set to 24
  Via CLI
    aws iam update-account-password-policy --password-reuse-prevention 24
  Note: All commands starting with "aws iam update-account-password-policy" have be
    combined into a single command in order to all of them to take effect

**Ensure IAM password policy expires passwords within 90 days or less**
  
IAM password policies can require passwords to be rotated or expired after a given
  number of days. It is recommended that the password policy expire passwords after 90
  days or less

  Perform the following to set the password policy as prescribed:
  Via AWS Console:
    1. Login to AWS Console (with appropriate permissions to View Identity Access
    Management Account Settings)
    2. Go to IAM Service on the AWS Console
    3. Click on Account Settings on the Left Pane
    4. Check "Enable password expiration"
    5. Set "Password expiration period (in days):" to 90 or less
  Via CLI
    aws iam update-account-password-policy --max-password-age 90
  Note: All commands starting with "aws iam update-account-password-policy" have be
     combined into a single command in order to all of them to take effec




**Ensure hardware MFA is enabled for the "root" account**

The root account is the most privileged user in an AWS account. MFA adds an extra layer of
  protection on top of a user name and password. With MFA enabled, when a user signs in to
  an AWS website, they will be prompted for their user name and password as well as for an
  authentication code from their AWS MFA device. For Level 2, it is recommended that the
  root account be protected with a hardware MFA.

  Perform the following to establish a hardware MFA for the root account:
    1. Sign in to the AWS Management Console and open the IAM console at
    https://console.aws.amazon.com/iam/.
    Note: to manage MFA devices for the root AWS account, you must use your root account
    credentials to sign in to AWS. You cannot manage MFA devices for the root account using
    other credentials.
    2. Choose Dashboard , and under Security Status , expand Activate MFA on your root
    account.
    3. Choose Activate MFA
    4. In the wizard, choose A hardware MFA device and then choose Next Step .
    5. In the Serial Number box, enter the serial number that is found on the back of the MFA
    device.
    6. In the Authentication Code 1 box, enter the six-digit number displayed by the MFA
    device. You might need to press the button on the front of the device to display the number.
    7. Wait 30 seconds while the device refreshes the code, and then enter the next six-digit
    number into the Authentication Code 2 box. You might need to press the button on the
    front of the device again to display the second number.
    8. Choose Next Step . The MFA device is now associated with the AWS account. The next
    time you use your AWS account credentials to sign in, you must type a code from the
    hardware MFA device.
  
**Ensure that GuardDuty enabled for required region**
  By enabling GuardDuty you will get threat detection service that continuously monitors for
  malicious activity and unauthorized behavior to protect your AWS accounts, workloads, and data stored in Amazon S3
  by event logs analysis.
  We are excluding verification for aws-global region from possible regions for GuardDuty

  Perform the following steps under the below link to enable GuardDuty:
  https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_settingup.html

**Ensure VPC flow logging is enabled in all VPCs**
  VPC Flow Logs is a feature that enables you to capture information about the IP traffic
  going to and from network interfaces in your VPC. After you've created a flow log, you can
  view and retrieve its data in Amazon CloudWatch Logs. It is recommended that VPC Flow
  Logs be enabled for packet "Rejects" for VPCs.

  Perform the following to determine if VPC Flow logs is enabled:
  Via the Management Console:
    1. Sign into the management console
    2. Select Services then VPC
    3. In the left navigation pane, select Your VPCs
    4. Select a VPC
    5. In the right pane, select the Flow Logs tab.
    6. If no Flow Log exists, click Create Flow Log
    7. For Filter, select Reject
    8. Enter in a Role and Destination Log Group
    9. Click Create Log Flow
    10. Click on CloudWatch Logs Group
  Note:
    Setting the filter to "Reject" will dramatically reduce the logging data accumulation
    for this recommendation and provide sufficient information for the purposes of breach
    detection, research and remediation. However, during periods of least privilege security
    group engineering, setting this the filter to "All" can be very helpful in discovering existing
    traffic flows required for proper operation of an already running environment.
  Impact:
    By default, CloudWatch Logs will store Logs indefinitely unless a specific retention period
    is defined for the log group. When choosing the number of days to retain, keep in mind the
    average days it takes an organization to realize they have been breached is 210 days (at the
    time of this writing). Since additional time is required to research a breach, a minimum 365
    day retention policy allows time for detection and research. You may also wish to archive
    the logs to a cheaper storage service rather than simply deleting them. See the following
    AWS resource to manage CloudWatch Logs retention periods:
      1. http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/SettingLogRetention.html



**Ensure all EC2 EBS Volumes has snapshots**
  
Availability of the snapshot could prevent data lose and will simplify restoring in case of data encryption
  Current rule check existence of Snapshots for EBS Volumes of running EC2 instances

  1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.
  2. Choose Snapshots under Elastic Block Store in the navigation pane.
  3. Choose Create Snapshot.
  4. For Select resource type, choose Volume.
  5. For Volume, select the volume.
    (Optional) Enter a description for the snapshot.
    (Optional) Choose Add Tag to add tags to your snapshot. For each tag, provide a tag key and a tag value.
  6. Choose Create Snapshot.

**Ensure credentials unused for 90 days or greater are disabled**
  AWS IAM users can access AWS resources using different types of credentials, such as
  passwords or access keys. It is recommended that all credentials that have been unused in
  90 or greater days be removed or deactivated.

  Perform the following to remove or deactivate credentials:
    1. Login to the AWS Management Console:
    2. Click Services
    3. Click IAM
    4. Click on Users
    5. Click on Security Credentials
    6. As an Administrator
      - Click on Make Inactive for credentials that have not been used in 90 Days
    7. As an IAM User
      - Click on Make Inactive or Delete for credentials which have not been used in 9


**Ensure the default security group of every VPC restricts all traffic**
  
A VPC comes with a default security group whose initial settings deny all inbound traffic,
  allow all outbound traffic, and allow all traffic between instances assigned to the security
  group. If you don't specify a security group when you launch an instance, the instance is
  automatically assigned to this default security group. Security groups provide stateful
  filtering of ingress/egress network traffic to AWS resources. It is recommended that the
  default security group restrict all traffic.
  The default VPC in every region should have its default security group updated to comply.
  Any newly created VPCs will automatically contain a default security group that will need
  remediation to comply with this recommendation.

  Security Group Members
  Perform the following to implement the prescribed state:
    1. Identify AWS resources that exist within the default security group
    2. Create a set of least privilege security groups for those resources
    3. Place the resources in those security groups
    4. Remove the resources noted in #1 from the default security group
  Security Group State
    1. Login to the AWS Management Console at
    https://console.aws.amazon.com/vpc/home
    2. Repeat the next steps for all VPCs - including the default VPC in each AWS region:
    3. In the left pane, click Security Groups
    4. For each default security group, perform the following:
    5. Select the default security group
    6. Click the Inbound Rules tab
    7. Remove any inbound rules
    8. Click the Outbound Rules tab
    9. Remove any inbound rules
  Recommended:
    IAM groups allow you to edit the "name" field. After remediating default groups rules for all
    VPCs in all regions, edit this field to add text similar to "DO NOT USE. DO NOT ADD RULES"

**Ensure AWS Config is enabled in all regions**
AWS Config is a web service that performs configuration management of supported AWS
  resources within your account and delivers log files to you. The recorded information
  includes the configuration item (AWS resource), relationships between configuration items
  (AWS resources), any configuration changes between resources. It is recommended to
  enable AWS Config be enabled in all region

  To implement AWS Config configuration:
  Via AWS Management Console:
    1. Select the region you want to focus on in the top right of the console
    2. Click Services
    3. Click Config
    4. Define which resources you want to record in the selected region
    5. Choose to include global resources (IAM resources)
    6. Specify an S3 bucket in the same account or in another managed AWS account
    7. Create an SNS Topic from the same AWS account or another managed AWS account
  Via AWS Command Line Interface:
    1. Ensure there is an appropriate S3 bucket, SNS topic, and IAM role per the AWS Config Service prerequisites.
    2. Run this command to set up the configuration recorder
      aws configservice subscribe --s3-bucket my-config-bucket --sns-topic
      arn:aws:sns:us-east-1:012345678912:my-config-notice --iam-role
      arn:aws:iam::012345678912:role/myConfigRole
    3. Run this command to start the configuration recorder:
      start-configuration-recorder --configuration-recorder-name <value

**Avoid the use of the "root" account**
 The "root" account has unrestricted access to all resources in the AWS account. It is highly
  recommended that the use of this account be avoided.

**Ensure CloudTrail logs are encrypted at rest using KMS CMKs**

  AWS CloudTrail is a web service that records AWS API calls for an account and makes those
  logs available to users and resources in accordance with IAM policies. AWS Key
  Management Service (KMS) is a managed service that helps create and control the
  encryption keys used to encrypt account data, and uses Hardware Security Modules
  (HSMs) to protect the security of encryption keys. CloudTrail logs can be configured to
  leverage server side encryption (SSE) and KMS customer created master keys (CMK) to
  further protect CloudTrail logs. It is recommended that CloudTrail be configured to use
  SSE-KMS

  Perform the following to configure CloudTrail to use SSE-KMS Via the Management Console
    1. Sign in to the AWS Management Console and open the CloudTrail console at
    https://console.aws.amazon.com/cloudtrail
    2. In the left navigation pane, choose Trails .
    3. Click on a Trail
    4. Under the S3 section click on the edit button (pencil icon)
    5. Click Advanced
    6. Select an existing CMK from the KMS key Id drop-down menu
    Note - Ensure the CMK is located in the same region as the S3 bucket
    Note - You will need to apply a KMS Key policy on the selected CMK in order for
      CloudTrail as a service to encrypt and decrypt log files using the CMK provided.
      Steps are provided here for editing the selected CMK Key policy
    7. Click Save
    8. You will see a notification message stating that you need to have decrypt
    permissions on the specified KMS key to decrypt log files.
    9. Click Yes
  Via CLI
    aws cloudtrail update-trail --name <trail_name> --kms-id
    <cloudtrail_kms_key>
    aws kms put-key-policy --key-id <cloudtrail_kms_key> --policy
    <cloudtrail_kms_key_policy>

**Ensure CloudTrail is enabled in all regions**

  Perform the following to enable global (Multi-region) CloudTrail logging:
  Via the management Console
    1. Sign in to the AWS Management Console and open the IAM console at
    https://console.aws.amazon.com/cloudtrail
    2. Click on Trails on the left navigation pane
    3. Click Get Started Now , if presented
       Click Add new trail
       Enter a trail name in the Trail name box
       Set the Apply trail to all regions option to Yes
       Specify an S3 bucket name in the S3 bucket box
       Click Create
    4. If 1 or more trails already exist, select the target trail to enable for global logging
    5. Click the edit icon (pencil) next to Apply trail to all regions , Click Yes and
    Click Save.
    6. Click the edit icon (pencil) next to Management Events click All for setting
    Read/Write Events and Click Save.
  Via CLI
    aws cloudtrail create-trail --name <trail_name> --bucket-name <s3_bucket_for_cloudtrail> --is-multi-region-trail
    aws cloudtrail update-trail --name <trail_name> --is-multi-region-trail
  Note: Creating CloudTrail via CLI without providing any overriding options configures
    Management Events to set All type of Read/Writes by default.

**Ensure CloudTrail log file validation is enabled**

  CloudTrail log file validation creates a digitally signed digest file containing a hash of each
  log that CloudTrail writes to S3. These digest files can be used to determine whether a log
  file was changed, deleted, or unchanged after CloudTrail delivered the log. It is
  recommended that file validation be enabled on all CloudTrails

  Perform the following to enable log file validation on a given trail:
  Via the management Console
    1. Sign in to the AWS Management Console and open the IAM console at
    https://console.aws.amazon.com/cloudtrail
    2. Click on Trails on the left navigation pane
    3. Click on target trail
    4. Within the S3 section click on the edit icon (pencil)
    5. Click Advanced
    6. Click on the Yes radio button in section Enable log file validation
    7. Click Save
  Via CLI
    aws cloudtrail update-trail --name <trail_name> --enable-log-file-validation
  Note that periodic validation of logs using these digests can be performed by running the following command:
    aws cloudtrail validate-logs --trail-arn <trail_arn> --start-time
    <start_time> --end-time <end_time>

**Ensure security questions are registered in the AWS account**

The AWS support portal allows account owners to establish security questions that can be
used to authenticate individuals calling AWS customer service for support. It is
recommended that security questions be established.

  Perform the following in the AWS Management Console:
- Login to the AWS Account as root
- Click on the <Root_Account_Name> from the top right of the console
- From the drop-down menu Click My Account
- Scroll down to the Configure Security Questions section 
- Click on Edit
- Click on each Question
  - From the drop-down select an appropriate question
  - Click on the Answer section
  - Enter an appropriate answer
    - Follow process for all 3 questions
- Click Update when complete
- Place Questions and Answers and place in a secure physical location