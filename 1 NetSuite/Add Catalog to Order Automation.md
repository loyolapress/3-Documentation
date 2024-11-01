#unpublished 

# Context

_In Oct 2022 marketing wanted to send catalogs in a more distributed fashion. They had me create a saved search pulling in PAR and CES customers who placed a significant curriculum order ($200) in the previous October. They wanted any order they placed this October to have a catalog added to the order. Given the difficulty of managing that manually, they asked for an automation. After much testing this is what I built._

# Summary

The automation is incorporated as a series of actions in the workflow that also manages Sales Order Approvals. It adds a specific item automatically to any order that meets certain criteria, usually based on customer and/or order attributes. In order to execute on a select list of customers, you can update the Arbitrary Text Field field on the customer record of the target group members.

# Notes

- Works on web and UI orders equally.
- A workflow action script added later will flag orders that are digital only. Those orders should be filtered from the other steps in the automation, otherwise we’ll be fulfilling JUST a catalog (or whatever it is) instead of adding it to a box already going out.
- There is no mechanism to prevent catalogs from being added to subsequent orders during the open period. A customer may receive multiple copies. Marketing didn’t seem bothered by this.
- Currently, deactivating the automation is a manual process. An expiration date could be worked into the filter, but I don’t like the idea of these actions running (or being checked) on every order unnecessarily. I prefer deactivating the actions at the end of the period.
- Initially, we tried to set this up using promotions; however, we ran into a number of technical issues, especially with regards to the website and display on the customer order PDF. While the issues might have been solvable, they were significant and difficult.
- For an order entered in the UI, the item will not appear on the order until after the order is saved.

# To Set Up Another Period

## Edit the workflow.

1. Open the sales order approval workflow, currently: [Sales Order Basic Approval v4](https://4672856.app.netsuite.com/app/common/workflow/setup/nextgen/workflowdesktop.nl?id=227&whence=)
2. Click EDIT and select the PENDING APPROVAL stage
    
    ![[Untitled 46.png|Untitled 46.png]]
    

### Update the Create Line action

1. In the right panel, scroll down to the RECORD SAVE: BEFORE SUBMIT section and open the CREATE LINE action.
    
    ![[Resources/attachments/Untitled 1 13.png|Untitled 1 13.png]]
    
2. In the CONDITION section, click the popout arrow to edit conditions.
    
    ![[Resources/attachments/Untitled 2 11.png|Untitled 2 11.png]]
    
3. Edit the condition for ARBITRARY TEXT FIELD (or other used field) to match whatever value you intend to use the customer record to tag the appropriate customers. You shouldn’t need to touch anything else.
    
    ![[Resources/attachments/Untitled 3 7.png|Untitled 3 7.png]]
    
4. Scroll down to the bottom and update the ITEM (LINE) portion to select the catalog that will be distributed. 

>[!important]  
    > Make sure that the warehouse is also fulfilled, should always be Fulfillment W/H. If this is not set, it can cause 2 shippers to be generated because the line is missing a location/warehouse.  
    
    ![[Resources/attachments/Untitled 4 7.png|Untitled 4 7.png]]
    
5. Uncheck the INACTIVE box just above the PARAMETERS section.
6. SAVE.

### Update the Set Field value action

This action marks each order has having gotten the item added. This will prevent the item being added every single time someone makes a change to the order.

1. Open the SET FIELD action that sets Catalog Added=T
2. On this action, edit the criteria to match the criteria you are using above to add the item. This may not be necessary, but I prefer to minimize the orders touched.
3. Again, uncheck the INACTIVE box to turn on this action.
4. SAVE.

## If using Arbitrary Text Field field: Import your “tag” to the customer records.

You will need a list of the eligible customers with their internal ids saved as a CSV.

> [!important]  
> Do not do this step until you are ready to go live. If you want to do it ahead of time, you can go back to the 2 actions in the workflow and set them both to INACTIVE again until you are ready.  

1. Open the CSV import [Set Arbitrary Text Field on Customer Record](https://4672856.app.netsuite.com/app/setup/assistants/nsimport/importassistant.nl?recid=257&new=T)
2. On the first screen you only need to select your CSV field for upload.
3. Click through to the Field Mapping page. Confirm the internal id field on your CSV is mapped to the NetSuite internal id field. Then click the pencil in the left column of the row for ARBITRARY TEXT FIELD to edit the hard-coded value that will be imported.
    
    ![[Resources/attachments/Untitled 5 7.png|Untitled 5 7.png]]
    
4. Update the text to reflect the text you set above in the workflow. Then click OK.
5. Click next and run the import.

Watch for completion of the import, otherwise you are done.

# Turning off the automation

While an expiration date could be added to the filters on the workflow, I prefer doing it this way to keep unnecessary workflow actions from running.

## Edit the workflow.

1. Open the workflow: [Sales Order Basic Approval v4](https://4672856.app.netsuite.com/app/common/workflow/setup/nextgen/workflowdesktop.nl?id=227&whence=)
2. Click EDIT and select the PENDING APPROVAL stage
    
    ![[Resources/attachments/Untitled 6 7.png|Untitled 6 7.png]]
    

## Inactivate the Create Line action

1. In the right panel, scroll down to the RECORD SAVE: BEFORE SUBMIT section and open the CREATE LINE action.
2. Check the INACTIVE box just above the PARAMETERS section. This will stop the action from firing.
3. Click the SAVE button.

## Inactivate the Set Field Value action

1. Open the Set Field action that sets Catalog Added=T
2. Again, check the INACTIVE box to turn on this action to turn it off.
3. SAVE

