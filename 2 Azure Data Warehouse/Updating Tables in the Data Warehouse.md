The data warehouse was built so that destination tables can be easily edited. 

# Adding a column to an existing table.
>[!note] Note
>The transactionLine table is too big to do a full load using the very reusable tools Swoon built. There are special instructions below that details the steps for doing a full reload of the transactionLine table.

## Overview
The following steps will allow you to add a new column from a data source to it's related data warehouse table. 
1. Find the exact column name in the original data source. 
2. Add the column to the destination table in the data warehouse, using the exact same name. 
3. If the destination table is an incremental table, capture the current `BenchmarkField` and `BenchmarkValue` for later. 
4. Add the new column name to the query in the data warehouse metadata table for the correct data source and table. 
5. If the destination table is incremental and there is existing data for the new column that needs to be brought in from before the last incremental update, the table needs to be fully blown out and rewritten. 
	- Set `incrementalFlag `to N
	- Set `BenchmarkField` and `BenchmarkValue` to Null
6. Wait for the data sync to occur or force it (I had trouble getting a manual sync to work last time I tried). 
	- to do this manually: Go to the Azure portal and launch the data factory. In Monitor > Pipeline Runs find the first NetSuite pipeline that ran the last night `PL_MAIN_Cpy_Salesforce_to_AzureSqlDb_csv` and hover over it, and click the rerun button
7. After sync, verify the new column is in the table and populated as expected. 
8. Open the metadata table again: 
	- set `incrementalFlag` back to Y 
	- set `BenchmarkField` and `BenchmarkValue` back to their previous values

# Adding columns to transactionLine table
# Process
1. Truncate the transactionLines table
2. Launch the VM (manually or via the pipeline PL_VM_NetSuite_Auto_Start)
3. Edit the query and the destination filename for the special transactionLine pipeline that uses parquet, PL_Cpy_NetSuite_Src_to_ADLS_Land_transactionLine_parquet. 
	1. Edit the query to have the correct columns (if there are new ones) and the correct dates for the batch you are working on. Paste into the Query parameter and save.
	2. Select the one step Copy Data and go to the subtab SINK. Click on the **filename** field to open the larger editing window. Update the appended filename to match the batch number you are running (eg P1 for part 1, P2 for part 2, etc.)
4. Go update the dataset DS_NetSuite_ADLS_Land_TransactionLine_Parquet that the filename has the correct appended batch number on it. (This is pointing the second pipeline to the correct file created by the first pipeline so it can push into the database.)
5. PUBLISH ALL (should be 2, the pipeline change and the dataset change)
6. Run the first pipeline.
7. Run the second pipeline.
8. Repeat from step 3, editing for the correct batch number and date boundaries each time BUT stop a few days before the current date
9. Stop the VM (optional, but neat)
10. Go into the blob storage and delete the parquet files manually
	1. Go back to the resource group and enter the storage called stgacclpdev > Blob Containers > loyolapress-landing-dev > NetSuite > transactionLine
	2. Delete the parquet files found there
11. Make sure the incremental load is set up to run with the final cutoff day you used above (maybe minus 1 day to be safe). Then run the full data load
12. Verify transaction lines and total amount against the ODBC query

- This could probably be more fully automated and strung together into 1 chained operation. Just not sure it's worth it.
