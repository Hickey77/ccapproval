# Company Communicator Approval Workflow Solution
Company Communicator Approval Workflow

This solution leverages Forms and Flow to add Approval workflow for Company Communicator App Template.  Many organization require multiple submiters and desise approval of comms before they out.

# Deployment Guide
1. Create a Form for users to submit new requests for Company Communicator communication.
  - Form needs to collect Card Title, Card Summary, Image URL, Author, Button Title, Button URL which we pass to Flow for use in Approvals and to create the draft Company Communicator card.
  - This example also asks submiter to pick department.  The Flow switches off this selection to allow seperate approvers for each department
  
  ![image](https://user-images.githubusercontent.com/54556057/112554428-ac4bde80-8d9c-11eb-844f-31cf39edcd63.png)
  
2. Import the Flow from the Repro
   - Flow steps
   - Key is adding entitiy to table
