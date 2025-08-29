# ChromeOS Device Actions (Foundry app)

A Foundry application that provides response actions for ChromeOS devices. This application enables security teams to rapidly respond to device incidents through:

1. UI-driven response actions directly from the Host Management and Endpoint Detection sidebar
2. Creating automated workflow templates for device containment and state management

The current implementation allows users to contain, disable, and re-enable ChromeOS devices, with additional response capabilities planned for future releases.

![Image](chromeos.png)

## Installation

The application can be installed through Foundry's app import functionality. You'll need appropriate permissions in your Foundry environment, as well as the appropriate GCP service account credentials to import and install the application.

### Requirements

- **Falcon Roles:**
  - Falcon Administrator
- **GCP Service Account Credentials**
  1. **Create a service account**:
     - Navigate to the [Google Cloud Console](https://console.cloud.google.com/)
     - Go to "IAM & Admin" > "Service Accounts"
     - Click "Create Service Account" and follow the [official documentation](https://cloud.google.com/iam/docs/creating-managing-service-accounts)
     - Download the JSON key file for authentication
        > Make sure to note the email address of the service account

  2. **Associate the service account with proper roles**:
     - The service account must be [associated with a role](https://support.google.com/a/answer/9807615?hl=en&ref_topic=9832445&sjid=12016677148987675569-NA#zippy=%2Cassign-a-role-to-a-service-account) in [Google Admin](https://admin.google.com/)
       > Make sure to use the email address of the service account when assigning the role

  3. **Required API scopes**:
     - Ensure the service account has the following API scopes:
       - `Manage ChromeOS Devices` - For managing ChromeOS devices
       - `Organizational Units -> read` - For viewing organizational units
- **Target Containment OU Path**
  - The path to a dedicated Organizational Unit (OU) for device containment, ideally one that enforces stricter security controls.
    > Paths can be specified as either the **full** path of the organizational unit or its **ID**.
    >
    > Examples:
    >
    >   - /MyParent/ChromeOS_Quarantine
    >   - id:01234567890
- **Google Admin Customer ID**
  - You can find this in [account settings](https://admin.google.com/u/3/ac/accountsettings)

### Install the app

To install the Foundry app and make it available to your CID:

1. In the **App overview** page, select **View in app catalog** to navigate to the app catalog for the Chrome Device Actions app
![Image](appcatalog.png)
1. Click **Install now** to start the installation process
![Image](installapp.png)
1. The application permissions will be displayed. Review them and click **Save and install** to complete the installation
![Image](permissions.png)
1. Fill our your Google Service Account information
   1. Enter a name for your service account credentials
   1. Upload your service account JSON key file
   1. Enter your Google Workspace customer ID
![Image](configuration.png)
1. Click **Install app** to complete the installation

## UI Extension

The ChromeOS Device Actions (Foundry app) includes a UI Extension that provides a side panel interface in the **Host Management** and **Endpoint detections** pages. This panel allows security teams to quickly perform ChromeOS management actions directly from the CrowdStrike console without switching to the Google Admin console.

The extension allows security teams to perform actions such as disabling devices and enabling devices directly from the CrowdStrike console.

![Image](uiextension.png)

## User Roles

The ChromeOS Device Actions app includes a predefined role that can be assigned to users:

- **ChromeOS Security Admin**: This role has permissions to enable and disable ChromeOS devices using the ChromeOS Device Actions UI extension.

In order to leverage this role properly, users need to be assigned this role, along with the **Foundry-Extensions** built-in role in the CrowdStrike console under **User Management**.

> Administrators should ensure that only authorized personnel are granted this role to maintain security controls over ChromeOS device management actions.

![Image](roles.png)

## Example Custom Fusion Workflows

The Chrome Device Actions app can be integrated with Fusion workflows to automate ChromeOS device management tasks. Here are some example workflows:

### Automated Response to ChromeOS Security Incidents

#### Workflow is triggered by a detection

![Image](wf1main.png)

#### Workflow Condition Details

The condition checks if the detection's platform is ChromeOS and if the severity is high or critical.

![Image](wf1condition.png)

#### Workflow Action (Move Device)

This workflow action takes the device ID from the `EPP Detection` trigger and a target OU path as input.

- **Device ID (AID)**: configured as a workflow variable from: `Alerts -> EPP Detection -> Sensor host id`
- **Target OU Path**: manual input to your containment OU path

![Image](wf1movedevice.png)

#### Workflow Action (Disable Device)

This workflow action takes the device ID from the `EPP Detection` trigger and the target device state as input. In this example we are setting the status/state to `Disabled`.

- **Device ID (AID)**: configured as a workflow variable from: `Alerts -> EPP Detection -> Sensor host id`
- **Device Status**: drop down list for device state

![Image](wf1status.png)
