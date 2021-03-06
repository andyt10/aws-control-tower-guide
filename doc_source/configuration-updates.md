# Configuration Update Management in AWS Control Tower<a name="configuration-updates"></a>

It is the responsibility of the members of your central cloud administrators' team to keep your landing zone updated\. Updating your landing zone ensures that AWS Control Tower is patched and updated\. In addition, to protect your landing zone from potential compliance issues, the members of the central cloud administrator team should resolve drift issues as soon as they're detected and reported\.

**Note**  
 The AWS Control Tower console indicates when your landing zone needs to be updated\. If you don't see an option to update, your landing zone is already up to date\.

## Updating Your Landing Zone<a name="update-controltower"></a>

The easiest way to update your AWS Control Tower landing zone is through the **Settings** page\. Navigate to the **Settings** page by choosing **Settings** in the left navigation\.

The **Settings** page shows you the current version of your landing zone, and it lists any updated versions that may be available\. You can choose the **Update** button if you need to update your version\. If the **Update** button appears greyed\-out, you do not need to update\.

**Note**  
Alternatively, you can update your landing zone manually\. The update takes approximately the same amount of time, whether you use the **Update ** button or the manual process\.

The following procedure walks you through the steps of updating your landing zone manually\.

**To update your landing zone manually**

1. Open a web browser, and navigate to the AWS Control Tower console at [https://us\-west\-2\.console\.aws\.amazon\.com/controltower/home/update](https://console.aws.amazon.com/controltower/home/update)\.

1. Review the information in the wizard and choose **Update**\. This updates the backend of the landing zone as well as your shared accounts\. This process can take a little more than an hour\.

1. Update your member accounts\. From the navigation pane, choose **Accounts**\.

1. Choose **Provision new account** to open the AWS Service Catalog console and the Account Factory product\.

1. From the navigation pane, choose **Provisioned products list**\.

1. For each account listed, perform the following steps to update all your member accounts:

   1. From the menu for the account, choose **Provisioned product details**\.

   1. Make a note of the following parameters:
      + **SSOUserEmail** \(Available in provisioned product details\)
      + **AccountEmail** \(Available in provisioned product details\)
      + **SSOUserFirstName** \(Available in SSO\)
      + **SSOUSerLastName** \(Available in SSO\)
      + **AccountName** \(Available in SSO\)

   1. From **ACTIONS**, choose **Update**\.

   1. Choose the radio button next to the **Version** of the product you want to update, and choose **NEXT**\.

   1. Provide the parameter values that were mentioned previously\. For **ManagedOrganizationalUnit** choose the OU that the account is in\. You can find this information in the AWS Control Tower console, under **Accounts**\.

   1. Choose **NEXT**\.

   1. Review your changes, and then choose **UPDATE**\. This process can take a few minutes per account\.

## Resolve Drift<a name="resolve-drift"></a>

When you create your AWS Control Tower landing zone, the landing zone and all the OUs, accounts, and resources are compliant with all of the governance rules enforced by your guardrails, whether mandatory or elective\. As you and your organization members use the landing zone, changes in compliance status may occur\. Some changes may be accidental, and some may be made intentionally to respond to time\-sensitive operational events\. Regardless, changes can complicate your compliance story\.

Resolving drift helps to ensure your organization's compliance with governance regulations\. Drift resolution is a regular operations task for your master account administrators\.

Drift detection is automatic in AWS Control Tower\. It helps you identify resources that need changes or configuration updates that must be made to resolve the drift\.

To repair most types of drift, choose **Repair** on the **Settings** page\. The **Repair** button becomes selectable when drift has occurred\. For more information, see [Detecting and Resolving Drift in AWS Control Tower](drift.md)\.

## Deploying AWS Control Tower to a New AWS Region<a name="deploying-to-new-region"></a>

This section describes the behavior you can expect when you deploy your AWS Control Tower landing zone into a new AWS Region\. Generally, this type of deployment is performed through the **Update** function of the AWS Control Tower console\.

**Note**  
We recommend that you avoid expanding your AWS Control Tower landing zone into AWS Regions in which you do not require your workloads to run\.

During deployment into a new AWS Region, AWS Control Tower updates the landing zone, which means that it *baselines* your landing zone to operate actively in the new Region\. Individual accounts within your organizational units \(OUs\) that are managed by AWS Control Tower are not updated as part of this landing zone update process\. Therefore, you must apply updates to your accounts individually\. 

Certain significant behavioral changes in AWS Control Tower detective guardrails are expected as a result of a deployment to a new AWS Region:
+ *What exists stays the same\.* Guardrail behavior, detective as well as preventive, is unchanged for existing accounts, in existing OUs, in existing Regions\.
+ *You can’t apply new detective guardrails to existing OUs containing accounts that are not updated\.* When you’ve deployed your AWS Control Tower landing zone into the new Region \(by updating it\), you must update existing accounts in your existing OUs before you can enable new detective guardrails on those OUs and accounts\.
+ *Your existing detective guardrails begin working in the new Region as soon as you update the accounts\.* When you update your AWS Control Tower landing zone to deploy into the new Region and then update an account, the detective guardrails that already are enabled on the OU will begin working on that account in the new Region\. 
+ *Update only to Regions in which you need to run\.* Due to this behavior, it can be time\-consuming to deploy your AWS Control Tower landing zone into new AWS Regions if your OUs manage a lot of member accounts\. Therefore, we recommend that you avoid expanding your AWS Control Tower deployment into AWS Regions in which you do not require your workloads to run\.

**Methods for automated updating of accounts**

One method of updating individual accounts after deployment to a new region is by using the API framework of AWS Service Catalog and the AWS CLI to update the accounts in a batch process\. You'd call the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html) API of AWS Service Catalog for each account\. You can write a script to update the accounts, one by one, with this API\. More information about this approach is available in a [blog post](http://aws.amazon.com/blogs/field-notes/enabling-guardrails-in-new-aws-regions-the-aws-control-tower-supports/)\.

You must wait for each account update to succeed before beginning the next account update\. Therefore, the process may take a long time if you have a lot of accounts, but it is not complicated\. For more information about this approach, see the [Walkthrough: Automated Account Provisioning in AWS Control Tower](automated-provisioning-walkthrough.md)\. 

**Note**  
The [Video Walkthrough](automated-provisioning-walkthrough.md#automated-provisioning-video) is designed for automated account provisioning, but the steps also apply to account updating, if you remember to call the "UpdateProvisionedProduct" API instead of the "ProvisionProduct" API\.

A further step of automation is to check for the AWS Control Tower lifecycle "UpdateLandingZone" **Succeed** event and use it as a trigger to begin updating individual accounts as described in the video\. A lifecycle event marks the completion of a sequence of activites, so the occurrence of this event means that a landing zone update is complete\. The landing zone update must be complete before account updates begin\. For more information about working with lifecycle events, see [ Lifecycle Events](https://docs.aws.amazon.com/controltower/latest/userguide/lifecycle-events.html)\.