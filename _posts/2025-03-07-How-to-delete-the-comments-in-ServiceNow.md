---
layout: post
title: "How to delete Comments or Work notes from a record"
date: 2026-03-07
categories: [servicenow, record]
tags: [comments, worknotes, delete, history]
---

Tags: delete comments worknotes

---
<br>

1. Right-click on the record and select **Copy URL to Clipboard** to obtain the unique sys_id of the record.
For example: https://<instancename>service-now.com/nav_to.do?uri=incident.do?sys_id=85befb1c4a34bb12013b216a9fd5fee8

2. Copy the sys_id. 
In the URL example above, the sys_id is: 85befb1c4a34bb12013b216a9fd5fee8.

3. Update the **Journal Entry**:
    1. Enter the journal entry URL + the sys_id. 
    For example: https://<instancename>.service-now.com/sys_journal_field_list.do?sysparm_query=element_id=85befb1c4a34bb12013b216a9fd5fee8
    2. Locate the journal entry.
    3. Modify the record and update or delete the record.

4. Update the **Audit Entry**:
    1. Enter the audit entry URL + the sys_id.
    For example: https://<instancename>.service-now.com/sys_audit_list.do?sysparm_query=documentkey=85befb1c4a34bb12013b216a9fd5fee8
    2. Locate the audit entry.
    3. Modify the record and update or delete the record.

5. Rebuild the **History Set**. 
This is only required when you do not use direct auditing and the System Property [***glide.sys.activity_using_audit_direct***] is set to **false** (= default value (this is the value used if the System Property does not exist on the instance).
    1. Locate all History Set records for the item whose history needs to be rebuilt by entering the history set URL + the sys_id. 
    For example: https://<instancename>.service-now.com/sys_history_set_list.do?sysparm_query=id=85befb1c4a34bb12013b216a9fd5fee8
    2. Click the **Delete** button for each History Set. 
    This will delete the History Set, *not* the audit data. 
    The History Set will be rebuilt with the corrected audit and journal information as soon as a user views the item.

6. Update the **Email Entry**:
    1. Enter the email entry URL + the sys_id. 
    For example: https://<instancename>.service-now.com/sys_email_list.do?sysparm_query=instance=85befb1c4a34bb12013b216a9fd5fee8
    2. Locate the email entry.
    3. Modify the record and update or delete the record.