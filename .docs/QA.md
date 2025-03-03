---
title: Qualtiy ASsuance Test Script
layout: page
title: "Quality Assurance Test Script"
permalink: /QA
---

# Quality Assurance Test Script

Here is my first QA test script I wrote. 

## Goal 1 – For Team Lead

**Set Up**

A team lead with the following:
 * Append permissions
 
1. From a Team page from the Email notifications tab, enable the "Stale On Hold Case Notifications" setting

 
## Goal 2 – For CSR

**Set Up**

A CSR on the Testing Team where the following is true:

 * The "Stale On Hold Case Notifications" have been enabled (see above)
 * There is at least one case with a status of _On Hold_

1. Notify the Developer that you wish to begin testing this ticket.
   * In the Sandbox environment, the automation will be run in a demo state that should send emails out for any _On Hold_ cases not modified in 1 minute.
   * In Production, this query will be every 30 days.
2. Create a case and change the status of the case to _On Hold_

**Result**

You should receive an email within a few minutes that states, "You have Cases that have been On Hold for 30 days or more."

&nbsp;

[Return to main page](https://jenpetsmit.github.io/)
