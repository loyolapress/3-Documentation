#unpublished 
### Overview
With the high volume of small web orders received throughout the year, Loyola Press would like to relieve the customer service team of the task of reviewing every single order that comes in and approving it. This system was developed to allow a flexible set of criteria be applied to certain orders and have them be automatically approved. 

### Description
The Auto Approval process runs entirely through the NetSuite Workflow [[Sales Order Basic Approval v4]]. The criteria and steps for auto approval are among several functions and customizations within this workflow.

Orders enter the workflow in the Pending Approval stage. Normally, after some data is set, the order would then wait in this stage until manual approval or rejection moves it forward. However, a new transition pipeline has been added with a series of criteria that will allow eligible orders to automatically progress from Pending Approval into a new stage called Auto Approve. Once a few more data fields are set, the order goes automatically to the stage Approved, where the status of the order is converted from Pending Approval to Pending Fulfillment. 
### Details

#### Current Criteria for Auto Approval
- Order is <= $150
- Order comes from web store
- Customer category of the purchaser it "Individual"
- Customer has NOT declared that they are buying for an organization; they are buying for themselves.
- Shipping country is not Canada (workflow variable `Shipping Country`)
- No TE items are in the order (workflow variable `TE Items in Order`)

#### Auto Approve Steps in the Workflow
On save, every sales order automatically enters the [[Sales Order Basic Approval v4]] workflow. 

![[Resources/attachments/Pasted image 20240429111047.png]]In the Pending Approval initial stage, on Entry, the workflow-specific variable `TE Items in Order` (int datatype) is automatically set to 1. This is because there is a script that runs later that will count the actual number of TE materials in the order, but it is rather resource intensive. We only want to run it when it is absolutely necessary, when an order has passed every other check. 

When the sales order is submitted to the database, but before commit, we also set the workflow-specific variable `Shipping country`. This is necessary because the transition criteria couldn't seem to access this in real time when it fires. So we set it in a variable first. 

Finally, the last action is to scan all items in the order to determine how many are TE materials. Because this involves running a resource intensive workflow action script, this workflow action has criteria that will only allow it to run when all of the other criteria above is already true; otherwise the script will not run. The script counts how many items in the order have an item attribute of Style = TE. This number is overwrites the default values set on entry for the workflow-specific variable `TE Items in Order`. 

After all actions are run, the transactions are evaluated. Only one allows orders to pass automatically. The criteria above are all checked against every order, and if the order meets ALL criteria, it automatically progresses to the stage Auto Approve. 

In this specific stage we:
- Set the order status to Pending Approval
- Set the Bill to Code and Ship to Code fields to match the customer id
- Note in the Internal Memo field that this order was auto approved

The order automatically moves to the final stage (which is never exited), joining all approved orders. 
### Components

#### Scripts
###### Workflow Action Script
- [[TE Materials in Order]] 

#### Workflows
- [[Sales Order Basic Approval v4]]

#### Custom Fields
###### Entity
- [[User Declared Customer Category]]
- [[Customer Category]]

###### Transaction Body
- [[Order Source]]

### Known Issues


