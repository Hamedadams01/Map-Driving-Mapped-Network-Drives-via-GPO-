# Mapping a Network Drive via Group Policy

## Overview

In this lab, I configured Active Directory Group Policy on a Windows Server 2019 domain controller (Houtech) to automatically map a network drive for the Human Resources (HR) department. This involved creating a dedicated GPO, sharing and permissioning a folder on the server, manually testing the mapping from a Windows 11 client, then replicating that mapping through a GPO Drive Map preference, linking it to the HR Organizational Unit, and finally validating the result with gpupdate /force.

## Objectives


Confirm the AD forest and existing OU structure were in place before making changes
Create a new, dedicated GPO ("Mapped Drives") rather than editing an existing policy
Create and share an HR folder on the server, restricting access to the HR security group
Manually map the share to a drive letter to confirm connectivity and permissions before automating anything
Configure a Drive Maps preference inside the GPO to replicate the working manual mapping
Link the GPO to the Human Resources OU so it applies only to HR users
Force a policy update on a client and confirm the drive mapped successfully


## Process 

 1 — Group Policy Management console opened
 
Opened the Group Policy Management console on Houtech and confirmed the Houtech.local forest was listed under Contents. This was the entry point for creating and linking a GPO that would map a network drive for HR.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20091545.png?raw=true)

 2 — Reviewing the OU structure
 
Expanded Domains > Houtech.local and reviewed the existing Department OUs (Finance, Human Resources, IT, Management, Marketing, Sales) along with the Group Policy Objects, WMI Filters, and Starter GPOs containers, confirming the OU structure from a previous lab was still intact.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20091707.png?raw=true)

 3 — Creating a new GPO 
 
Right-clicked Group Policy Objects and selected New, which opened the New GPO dialog. Named the new GPO "Mapped Drives" with Source Starter GPO left as (none), creating a dedicated policy object for the drive-mapping configuration.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20091946.png?raw=true)


4 — New GPO created and enabled

Opened the Group Policy Objects container and confirmed "Mapped Drives" appeared alongside the built-in Default Domain Controllers Policy and Default Domain Policy, with a GPO Status of Enabled — verifying the GPO was created successfully.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20092002.png?raw=true)


5 — Verifying GPO details

Reviewed the Group Policy Objects list a second time to confirm the Mapped Drives GPO's Enabled status, lack of a WMI filter, and correct modified date/owner as a sanity check before continuing.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20092248.png?raw=true)


6 — Creating the HR folder

On the C: drive of the Houtech server, created a new folder named "Human Resources" to serve as the dedicated network location for the HR department.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20092342.png?raw=true)


7 — Folder Properties, Sharing tab

Opened the Human Resources folder's Properties and viewed the Sharing tab, which showed the network path as "Not Shared" — the starting point for turning the local folder into a network share.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20092509.png?raw=true)


8 — Advanced Sharing configuration

Opened Advanced Sharing and checked "Share this folder," setting the share name to "Human Resources" and enabling the Permissions and Caching options, rather than using the basic file-sharing wizard.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20092533.png?raw=true)


9 — Permissions dialog, adding a group

Clicked Permissions to open the Permissions for Human Resources dialog, then clicked Add to open the Select Users, Computers, Service Accounts, or Groups dialog, in order to control exactly who could access the HR share.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20092611.png?raw=true
)


10 — Selecting the HR security group

Typed "#Human_Resources" into the object name field, referencing the security group created in an earlier OU and Group lab, so permissions could be managed through the department's existing group.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20092629.png?raw=true)

11 — Setting NTFS/share permissions

Back on the Security tab, confirmed the #Human_Resources group now appeared alongside CREATOR OWNER, SYSTEM, Administrators, and Users, and set Full Control, Modify, Read & Execute, List Folder Contents, and Read to Allow for the HR group.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20092706.png?raw=true)

12 — Map Network Drive wizard opened

On a Windows 11 client, opened the Map Network Drive wizard, which defaulted to drive letter Z: with an empty Folder field and "Reconnect at sign-in" checked — used to manually test the share before automating it with Group Policy.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20093905.png?raw=true)

13 — Entering the UNC path

Changed the drive letter to H: and typed the UNC path \\houtech\Human Resources into the Folder field to connect directly to the shared folder created and permissioned earlier.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20094122.png?raw=true)

14 — Browsing available shares

Used the Browse button, which opened the Browse For Folder dialog and displayed the shares published by the Houtech server (Human Resources, NETLOGON, SYSVOL), confirming the HR share was visible on the network.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20094210.png?raw=true)

15 — Mapped drive confirmed in File Explorer

After completing the wizard, File Explorer showed the new "Human Resources (\houtech)" drive mapped to H:, currently empty — proving the share, permissions, and path were all working before configuring the GPO.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20094245.png?raw=true)

16 — Group Policy Management Editor opened

Right-clicked the Mapped Drives GPO and opened the Group Policy Management Editor, showing the standard Computer Configuration and User Configuration nodes, ready to build the drive-mapping preference.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20094312.png?raw=true)

17 — Navigating to Drive Maps

Inside User Configuration > Preferences > Windows Settings, selected Drive Maps, which was empty ("There are no items to show in this view") — the correct node for configuring an automatic drive mapping.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20094509.png?raw=true)

18 — New Mapped Drive properties

Right-clicked Drive Maps and selected New Mapped Drive, opening the New Drive Properties dialog with Action set to Update and the Location field blank, ready to define the drive mapping.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20094518.png?raw=true)

19 — Configuring the drive mapping

Set the Location to \\houtech\Human Resources and set the Drive Letter option to "Use: H:", matching the exact share path and drive letter already verified manually so the GPO would recreate the same mapping 
automatically.
![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20094619.png?raw=true)

20 — Drive Maps preference saved

After clicking OK, the Drive Maps view showed one entry: Name H:, Order 1, Action Update, Path \\houtech\Human Resour..., Reconnect No — confirming the preference was saved correctly inside the GPO.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20094651.png?raw=true)

21 — Linking the GPO to the HR OU

Right-clicked the Human Resources (HR) OU and chose "Link an Existing GPO," opening the Select GPO dialog listing Default Domain Controllers Policy, Default Domain Policy, and Mapped Drives, then selected Mapped Drives to scope the policy specifically to the HR OU.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20094748.png?raw=true)

 22 — Confirming the GPO link
 
Opened the HR OU's properties and checked the Linked Group Policy Objects tab, confirming Mapped Drives was linked with Link Order 1, Link Enabled = Yes, and GPO Status = Enabled.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/Screenshot%202026-06-22%20094757.png?raw=true) 

24— Final verification

Signed in as dsmith, opened File Explorer and confirmed the "Human Resources (\houtech)" drive was automatically mapped to H: (empty), proving the GPO-based drive mapping worked exactly as the earlier manual test had.

![image alt](https://github.com/Hamedadams01/Map-Driving-Mapped-Network-Drives-via-GPO-/blob/main/preview%20(2).webp?raw=true)
