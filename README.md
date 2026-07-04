# Map-Driving-Mapped-Network-Drives-via-GPO

# Overview
This project covers creating a shared HR folder on the server and automatically mapping it as a network drive for HR users through Group Policy, rather than relying on manual drive mapping.
# Objectives

- Create and share a dedicated folder for the HR department
- Set folder permissions scoped to the HR security group
- Manually test the share before automating it
- Configure a GPO to automatically map the drive for HR users
- Link the GPO to the HR OU and verify it applies correctly

# Step-by-Step

- I opened Group Policy Management on Houtech and confirmed I could see the Houtech.local forest under Contents.
- I expanded Domains > Houtech.local and reviewed my existing Department OUs to confirm my earlier OU structure was intact.
- I right-clicked Group Policy Objects and created a new GPO named "Mapped Drives" with no Source Starter GPO.
- I opened the Group Policy Objects container and confirmed the new Mapped Drives policy appeared with a GPO Status of Enabled.
- I created a new folder named "Human Resources" on the C: drive of the Houtech server.
- I opened the folder's Properties, switched to the Sharing tab, and confirmed the network path showed as "Not Shared."
- I clicked Advanced Sharing, checked "Share this folder," and set the share name to "Human Resources."
- I clicked Permissions, then Add, to open the Select Users, Computers, Service Accounts, or Groups dialog.
- I typed "#Human_Resources" into the object name field, referencing the security group I'd created earlier.
- I confirmed the #Human_Resources group appeared in the permissions list and set Full control, Modify, Read & execute, List folder contents, and Read to Allow.
- I opened the Map Network Drive wizard on the Windows 11 client to test the share manually before automating it.
- I changed the drive letter to H: and typed the UNC path \houtech\Human Resources into the Folder field.
- I used the Browse button to confirm the Human Resources share was visible alongside NETLOGON and SYSVOL.
- I finished the wizard and confirmed File Explorer showed the new H: drive mapped and connected, currently empty.
- I opened the Mapped Drives GPO in the Group Policy Management Editor to configure the actual policy.
- I navigated to User Configuration > Preferences > Windows Settings > Drive Maps, which was empty.
- I right-clicked Drive Maps and created a New Mapped Drive, setting the Action to Update.
- I set the Location to \houtech\Human Resources and set the Drive Letter option to "Use:" H:, matching my manual test.
- I confirmed the Drive Maps view now showed one entry: Name H:, Order 1, Action Update, Path \houtech\Human Resources.
- I right-clicked the Human Resources (HR) OU and chose "Link an Existing GPO," selecting Mapped Drives.
- I opened the HR OU's properties and confirmed on the Linked Group Policy Objects tab that Mapped Drives was linked with Link Order 1, Link Enabled Yes, GPO Status Enabled.
- I signed in as dsmith on the Windows 11 client and ran gpupdate /force to pull down the new policy immediately.
- I confirmed File Explorer showed the "Human Resources (\houtech)" drive automatically mapped to H:.

# Conclusion

- A shared HR folder was created and permissioned using the department's existing security group
- The share was manually tested and verified before automation
- A GPO was built to automatically map the drive for HR users via Group Policy Preferences
- The GPO was linked to the HR OU and confirmed working on a client after a policy refresh
