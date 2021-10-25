**Ensure that instances are not configured to use the default service account**
  It is recommended to configure your instance to not use the default Compute Engine
  service account because it has the Editor role on the project.

  From Console:
    1. Go to the VM instances page by visiting:
      https://console.cloud.google.com/compute/instances.
    2. Click on the instance name to go to its VM instance details page.
    3. Click STOP and then click EDIT .
    4. Under the section Service Account , select a service account other than the default
      Compute Engine service account. You may first need to create a new service
      account.
    5. Click Save and then click START .
  From Command Line:
    1. Stop the instance:
      gcloud compute instances stop INSTANCE_NAME
    2. Update the instance:
      gcloud compute instances set-service-account INSTANCE_NAME --service-account=SERVICE_ACCOUNT
    3. Restart the instance:
      gcloud compute instances start INSTANCE_NAME
  
**Ensure that IAM users are not assigned the Service Account User or
  Service Account Token Creator roles at project level**

  It is recommended to assign the Service Account User (iam.serviceAccountUser) and
  Service Account Token Creator (iam.serviceAccountTokenCreator) roles to a user for
  a specific service account rather than assigning the role to a user at project level

  From Console:
    1. Go to the IAM page in the GCP Console by visiting:
    https://console.cloud.google.com/iam-admin/iam.
    2. Click on the filter table text bar. Type Role: Service Account User
    3. Click the Delete Bin icon in front of the role Service Account User for every user
    listed as a result of a filter.
    4. Click on the filter table text bar. Type Role: Service Account Token Creator
    5. Click the Delete Bin icon in front of the role Service Account Token Creator for
    every user listed as a result of a filter.
  From Command Line:
    1. Using a text editor, remove the bindings with the roles/iam.serviceAccountUser
    or roles/iam.serviceAccountTokenCreator.

    For example, you can use the iam.json file shown below as follows:
    ```
      {
      "bindings": [
      {
       "members": [
       "serviceAccount:our-project-123@appspot.gserviceaccount.com",
       ],
       "role": "roles/appengine.appViewer"
      },
      {
       "members": [
       "user:email1@gmail.com"
       ],
       "role": "roles/owner"
       },
      {
       "members": [
       "serviceAccount:our-project-123@appspot.gserviceaccount.com",
       "serviceAccount:123456789012-compute@developer.gserviceaccount.com"
       ],
       "role": "roles/editor"
      }
      ],
      "etag": "BwUjMhCsNvY="
       }
    2. Update the project's IAM policy:
    gcloud projects set-iam-policy PROJECT_ID iam.json```


**Ensure that there are only GCP-managed service account keys for each service account**

  User managed service accounts should not have user-managed keys

  From Console
    1. Go to the IAM page in the GCP Console using https://console.cloud.google.com/iam-admin/iam
    2. In the left navigation pane, click Service accounts. All service accounts and their corresponding keys are listed.
    3. Click the service account.
    4. Click the edit and delete the keys.
  From CLI To delete a user managed Service Account Key:
    gcloud iam service-accounts keys delete --iam-account=<user-managed-service-account-EMAIL> <KEY-ID>
