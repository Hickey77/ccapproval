# Company Communicator Approval Workflow Solution
Company Communicator Approval Workflow

This solution leverages Forms and Flow to add Approval workflow for Company Communicator App Template.  Many organizations require multiple submitters and desire approval of comms before they out.

[This solution is part of the Company Communicator Adoption Kit](https://aka.ms/ccadopt)

Best practice is to have just a few people with the ability to send Company Communications (Operators).
1. Requesters:  Anyone with Form link can request new company Communications
2. Approvers:  Flow sends Approval Email and task to designated departmental approver
3. CC Operator:  Once communication is approved, they send the draft message at specified time.  The flow could also send planner task or reminder to operators

# Deployment Guide
I. Create a Form for users to submit new requests for Company Communicator communication.
  - Form needs to collect Card Title, Card Summary, Image URL, Author, Button Title, Button URL which we pass to Flow for use in Approvals and to create the draft Company Communicator card.
  - This example also asks submiter to pick department.  This is optional.  My Flow switches off this selection to allow separate approvers for each department
  
  ![image](https://user-images.githubusercontent.com/54556057/112554428-ac4bde80-8d9c-11eb-844f-31cf39edcd63.png)
  
II. Import the Flow from the Repo or create your own Flow.

1. First step is trigger to start Flow when a response to you form has been submitted.  Change this step by picking the form you created in step 1 in the drop down or enter Form ID.

![image](https://user-images.githubusercontent.com/54556057/112661594-157b3280-8e2d-11eb-929b-de1bff00f720.png)

2. Step 2 in the Flow gets the details of the form response.  Change this step to point to your Form.  In the Response details - choose List of Response variable from Forms

![image](https://user-images.githubusercontent.com/54556057/112661975-84f12200-8e2d-11eb-9e40-b73ebe96edc7.png)

3. Next, we initialize two variables.  One for the approver and one GUID to use in Company Communicator.  No changes required here.

![image](https://user-images.githubusercontent.com/54556057/112662426-0ea0ef80-8e2e-11eb-9d3c-8bf9c83c7f99.png)

4. Now the flow switches on Department choice to set VAR_Approvers variable for correct department approver.  You can skip this switch if you have just a few central approvers.

![image](https://user-images.githubusercontent.com/54556057/112662511-27a9a080-8e2e-11eb-98ca-f035d050ed2f.png)

All we do in the switch is set correct approver email address in VAR_Approvers varible based on department choice in field.
![image](https://user-images.githubusercontent.com/54556057/112662739-6b9ca580-8e2e-11eb-825c-d2246daf1d94.png)

5. Next flow step - Start an approval

Approval type:  Approve/Reject - First to respond

Title:  @{body('Get_response_details')?['responder']} is requesting Approval for Company Communication

Assigned to:  VAR_Approvers

**Please Approve or Reject request for new Company Communication:**
Department: @{body('Get_response_details')?['rd30fffe9430d489c8b049e1a1041dd53']}
Date: @{body('Get_response_details')?['rddc9519b6722446c9b3e4985defef28f']}
Title: @{body('Get_response_details')?['r77821ae347f240b7a180372e960f2f33']}

**Communication Preview:**

**@{body('Get_response_details')?['r77821ae347f240b7a180372e960f2f33']}**

![](@{body('Get_response_details')?['r15aac592e77541a5967bded12e556709']})

@{body('Get_response_details')?['r9616d930eb274e4b807b9041791d40b8']}

@{body('Get_response_details')?['r88a2fe7978a94a1dafa91cf089d17ae0']}

[@{body('Get_response_details')?['r96eef560005248d5b1380413165fc962']}](@{body('Get_response_details')?['rca92ce26b78c42c6b9a227f201797cb6']})

![image](https://user-images.githubusercontent.com/54556057/112848649-4c905480-9076-11eb-8179-f42e5315c096.png)

The flow will send approval Email to user(s) in VAR_Approver

6. Next step is conditional based on Approval by VAR_Approvers - once approved the flow will continue

If not Approved - send email rejection to responder and approver - End of Flow

If Approved - Send Approval email and insert Entity into Company Communicator Azure table DraftNotifications directly

![image](https://user-images.githubusercontent.com/54556057/112665506-845a8a80-8e31-11eb-8d3d-ea5b79167ed6.png)

7. This is where the magic happens, and the Flow creates a draft Company Communicator Message

![image](https://user-images.githubusercontent.com/54556057/112664223-1cf00b00-8e30-11eb-970a-76dc3442eb4d.png)

Here's the Raw JSON:
{
  "PartitionKey": "DraftNotifications",
  "RowKey": "@{variables('VAR_Rowkey')}",
  "AllUsers": "false",
  "Author": "@{body('Get_response_details')?['r88a2fe7978a94a1dafa91cf089d17ae0']}",
  "ButtonLink": "@{body('Get_response_details')?['rca92ce26b78c42c6b9a227f201797cb6']}",
  "ButtonTitle": "@{body('Get_response_details')?['r96eef560005248d5b1380413165fc962']}",
  "CreatedBy": "@{body('Start_an_approval')?['responder']?['email']}",
  "CreatedDate": "@{body('Get_response_details')?['submitDate']}",
  "Failed": 0,
  "GroupsInString": "[]",
  "Id": "@{variables('VAR_Rowkey')}",
  "ImageLink": "@{body('Get_response_details')?['r15aac592e77541a5967bded12e556709']}",
  "IsCompleted": "false",
  "IsDraft": "true",
  "IsPreparingToSend": "false",
  "RostersInString": "[]",
  "Succeeded": 0,
  "Summary": "@{body('Get_response_details')?['r9616d930eb274e4b807b9041791d40b8']}",
  "TeamsInString": "[]",
  "Throttled": 0,
  "Title": "@{body('Get_response_details')?['r77821ae347f240b7a180372e960f2f33']}",
  "TotalMessageCount": 0,
  "Unknown": 0
}

8. When the flow is complete, you will have a new draft Company Communicator mesage in the Company Communicator Author tool.  

![image](https://user-images.githubusercontent.com/54556057/112666014-12cf0c00-8e32-11eb-914d-ede0dcf5f52e.png)
