﻿---
title: 'Lync Server 2013: Testing Lync Server services'
TOCTitle: Testing Lync Server services
ms:assetid: b564b450-a746-4ec9-aabb-e076309ccd5f
ms:mtpsurl: https://technet.microsoft.com/de-de/library/Dn689119(v=OCS.15)
ms:contentKeyID: 62247569
ms.date: 12/10/2016
mtps_version: v=OCS.15
ms.translationtype: HT
---

# Testing Lync Server services in Lync Server 2013

 

_**Letztes Änderungsdatum des Themas:** 2016-12-08_


<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p>Verification schedule</p></td>
<td><p>Daily</p></td>
</tr>
<tr class="even">
<td><p>Testing tool</p></td>
<td><p>Windows PowerShell</p></td>
</tr>
<tr class="odd">
<td><p>Permissions required</p></td>
<td><p>When run locally using the Lync Server-Verwaltungsshell, users must be members of the RTCUniversalServerAdmins security group.</p>
<p>When run using a remote instance of Windows PowerShell, users must be assigned an RBAC role that has permission to run the Test-CsComputer cmdlet. To see a list of all RBAC roles that can use this cmdlet, run the following command from the Windows PowerShell prompt:</p>
<pre><code>Get-CsAdminRole | Where-Object {$_.Cmdlets -match &quot;Test-CsComputer&quot;}</code></pre></td>
</tr>
</tbody>
</table>


## Description

Test-CsComputer verifies the status of all the Lync Server 2013 services that are running on the local computer. (Test-CsComputer can only be run locally, it cannot be run from a remote instance of Windows PowerShell.) The cmdlet also checks whether the appropriate firewall ports are opened on the computer, and determines whether the Active Directory groups that were created when you installed Lync Server 2013 were added to the corresponding local groups. For example, Test-CsComputer will verify that the Active Directory group RTCUniversalUserAdmins was added to the Administrators group.

For more information, see the Help documentation for the [Test-CsComputer](test-cscomputer.md) cmdlet.

## Running the test

The Test-CsComputer cmdlet can only be run on the local computer, you can't call Test-CsComputer from a remote instance of Windows PowerShell. By default, Test-CsComputer displays very little output on-screen, instead information returned by the cmdlet is written to an HTML file. Because of that, we recommend that you include the Verbose parameter and the Report parameter any time that you run Test-CsComputer. The Verbose parameter will provide slightly more detailed output on-screen while the cmdlet runs. The Report parameter allows you to specify a file path and file name for the HTML file generated by Test-CsComputer. If you do not include the Report parameter the HTML file will automatically be saved to your Users folder and be given a name similar to this: ce84964a-c4da-4622-ad34-c54ff3ed361f.html.

The following sample command runs Test-CsComputer and saves the output to a file that is named C:\\Logs\\ComputerTest.html:

    Test-CsComputer -Report "C:\Logs\ComputerTest.html" -Verbose

For more information, see the Help documentation for the [Test-CsComputer](test-cscomputer.md) cmdlet.

## Determining success or failure

Because of the number of verification checks that it performs, Test-CsComputer does not report back a simple **Yes, the test succeeded** or **No, the test failed**. Instead, you have to view the generated HTML file by using Internet Explorer to determine the success or failure of each test.

## Reasons why the test might have failed

Here are some common reasons why Test-CsComputer might fail:

  - The test computer might not be enabled for use with Lync Server. This can occur if the Lync Server services or server roles on the computer have changed and the Enable-CsComputer cmdlet was not run. To resolve this issue, run the following command:
    
        Enable-CsComputer

  - Replication might not be up to date on the test computer. You can check the current replication status for a computer by running the Get-CsManagementStoreReplicationStatus cmdlet:
    
        Get-CsManagementStoreReplicationStatus -ReplicaFqdn "atl-cs-001.litwareinc.com"
    
    If the replication status is not up to date, you can manually force replication to occur by using a command similar to this:
    
        Invoke-CsManagementStoreReplication -ReplicaFqdn "atl-cs-001.litwareinc.com"

  - The topology might have to be enabled. If you change the Lync Server topology (changes that might affect the local computer), then you must enable the new topology. You can enable the topology at any time by running this command:
    
        Enable-CsTopology

