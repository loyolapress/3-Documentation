
_These steps are to be performed between June 30 and July 15 to reset portions of digital fulfillment for the new school year._
# General - June 30
Turn off fulfillment job for digital textbooks, online assessments, and Wanderlight B2B.
# Digital Textbooks
- archive data tables (make a copy, rename with datetime in name): `dt_UserAccounts`
    
    ```SQL
    select * 
    into xt_UserAccounts_20XXarchive
    from dt_UserAccounts
    ```
    
- clear data tables (Lisa will preserve any orders that need to be carried over on the MySQL side — my table can be cleared
- Update hard-coded expiration date in `dff_dt4_executeUserPush` (line 161)
- Update sentences (X2) that states the period of the license in `dff_dt5_sendAdminEmail` (lines 141 and 179)
- Run test order through
# Digital Library
## Finding God App G7 & G8
`dff_dl4_pushProductLines`  
update the hardcoded expiration date for the push of lines into the FG app license staging table (line 272)  
# Wanderlight
`[dff_WLB2B3_pushCustomerOrder]` update the variable `@schoolYearID` to match the id of the new school year as set in the Wanderlight database by Lisa (line 124). School year ID can be found by accessing the direct MySQL view
```SQL
SELECT * FROM [DigitalTextbooks].[dbo].[wlb2bv_mysql_schoolYears]
```
`[dff_WLB2B4_customerEmail]` - update the variable `@expireDate` so that the query uses the id of the new school year as set in the Wanderlight database by Lisa (line 64) — use the same school id from above.
# General - July 18 (verify date as it varies)
Resume jobs.

> [!important]  
> Discuss with Anette - date of next school year fulfillment start- when to update the default date formula for the field NEXT SCHOOL YEAR START — custbody_jz_nextfulfillmentstart![[Resources/attachments/Untitled 47.png|Untitled 47.png]]  
# Communication Center
Set `hold` to null for those orders to allow them to process
Update CommCenter processing to no longer set those holds until next year: [🚦Turn on/off CommCenter Digital Item Hold](https://www.notion.so/Turn-on-off-CommCenter-Digital-Item-Hold-fd46799c592147ebb58900c3f3ae7ae6?pvs=21)
Set a reminder to talk to CC about turning that hold back on in April.
