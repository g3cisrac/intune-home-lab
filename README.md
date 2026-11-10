# Microsoft Intune Lab

**Lab goal:** Build a small Microsoft 365 and Intune environment from scratch to practice the core tasks of an endpoint support technician...

## TL;DR

Home lab: Microsoft 365 and Intune administration (users, licensing, device enrollment, compliance and configuration policies, app deployment, troubleshooting).

## Test users

- Created test users: Faculty Test, Front Desk Test, and Staff Test, plus my admin account
- Front Desk Test also received an Intune license (note on the screenshot)

![Test users](01-test-users.png)
## Create groups and add each user to their corresponding group. 
- Active groups + their members in screenshots:

![Lab faculty](02-lab-faculty.png)

![lab test devies](03-lab-test-devices.png)

![lab staff](04-lab-staff.png)
## Mobility (MIM and WIP) - Enrolling a VM: How to enroll Windows 11

![VM Setup](05-vm-setup.png)
## One issue I ran into: 
- I accidentally originally downloaded Windows 11 Home, not Pro. Home version does not support device mobility policies.
## Register VM with Windows 11; on a Windows 11 Pro or Enterprise VM (the free evaluation ISO works), open Settings > Accounts > Access work or school > Connect > Join this device to Microsoft Entra ID, and signed in as a test user. 
![entra](06-windows11-entra.png)

![further setup](07-win11pro-setup.png)
##	Adding new Win11Pro device to Entra. Chose to add stest[@]g3cisrac.onmicrosoft.com:
This issue took quite a while to resolve. Another challenge was the original test user was enrolled in the Windows 11 Home VM which didn’t work, I deleted it, and once I deleted the VM I was not allowed to use the same email to enroll through a different VM, since that Windows 11 Home  enrollment was still pending. Changing test users solved the problem, as I used the above email for “Staff Test.”
After a “Keep your account secure” prompt, clicking continue led to this error. This did not matter as the VM was already enrolled in Entra.

![entra setup](08-win11pro-entra.png)
![error](09-badrequesterror.png)
## Next I pushed a configuration profile. Devices > Configuration > Create > New policy > Windows 10 and later > Settings catalog. Start by going to Intune. As we can see, Windows11P the VM is enrolled and currently compliant.

![properties](10-windows11p-properties.png)
![intune url](11-intune-url.png)
![intune devices overview](12-intune-devices.png)
## Below, I select Create Policy

![createpolicy](13-create-policy.png)
## Then, named the compliance policy. Now I will select one setting I can see on the VM. 
## View the completed info on the compliance policy before creating.

![name compliance policy](14-name-compliance-policy.png)
![policy parameters](15-policy-parameters.png)
## The policies I changed are all listed under “Compliance settings” above. I chose these because they will be seen in the VM first. At first there was no visible policy change or compliant/non-compliant devices. I fixed this in Intune, ensured I could sync this policy to the VM. It took 15-20 mins.

![sync](16-sync.png)
## In the meantime, jumped ahead a bit, creating a configuration profile to mandate a screen timeout. I made it a 300 second timeout. 

![settings catalog](17-settings-catalog.png)
![configure name/description](18-config-name-description.png)
![device timeout](19-device-timeout.png)
![all devices checked](20-all-devices-checked.png)
## Screen will now lock after 5 mins. 

![vm locked](22-vm-locked.png)
## Here it shows my VM is compliant with the parameters I set earlier. I waited around 20 mins for this to populate in Intune. Good to know that when dealing with the compliance status, you have to give it time to catch up. Same with many other cloud based policies. Patience with these is important. 

![Finished Policy](21-finished-policy.png)

![compliance screen](23-compliance-1.png)

## Create an app to deploy in Intune
![newapp](24-create-new-app.png)
![in terminal](25-win-terminal.png)
## I added it under required for all devices. Now that it’s created, I synced the VM and waited another 15-30 mins. 
## After doing this, I realized this was a bad test application… I assumed this VM did not have that terminal but turns out it already does. I used a different app, something obviously not in the VM. I landed on Microsoft Bing dictionary (Chinese=English) as the app of choice. Then I let the VM sync again. Here is the synced on the device. 
![device enrolled](28a-device-enrolled.png)

![dictionary](25a.png)
## 	Now, to break it and fix it. To do this I’m going to make the VM non-compliant on purpose. I went to the VM. Windows Security>Firewall & Network Protection>Toggle Microsoft Defender Firewall to Off

![firewall settings](26-firewall.png)

![firewall off](27-firewall-off.png)

![noncompliant alert](28-noncompliant.png)

## 	This led to a notification saying it’s off and prompting to turn it back on. 
![firewall on](29-firewall-on.png)
## To fix this noncompliance issue, I went in and turned firewall back on. Windows Security > Firewall & Network Protection > Windows Defender Firewall with Advanced Security on Local Computer, went to Windows Defender Firewall Properties, toggled “Firewall State” to “On” then forced a sync.    

![security recs](30-security-rec.png)

![compliant device](31-compliant-device.png)



## What didn’t work at first was after a day of doing this lab, the VM stopped syncing, and I had to remove the device from the Entra ID to get it working again. To do this, too, I was forced to reset the password to even remove the device since it was the original password I setup the VM with. It refused to sync and  I read specific error codes (-2145833241 and 0x80190190)

## While creating the policy, I received 3 security recommendations in emails from Microsoft, as the way I went about it was not perfect. 
## What I learned
## •	Users, groups, and licenses control who gets which services. A missing license or an empty group can block everything else.
## •	Entra and Intune are separate admin centers. A device can show up in Entra without being enrolled in Intune, making sure to check both is important
## •	Enrollment takes a while, and making sure you choose the right edition was important, so I rebuilt the VM as ## Windows 11 Pro.
## •	A compliance policy defines what “healthy” looks like and reports on it, a configuration profile pushes settings to the device
## •	A policy does nothing until it’s assigned to a group that contains the device, i kept having to deal with it showing 0 devices lol
## •	Cloud management is a lot of waiting around. I learned to sync, wait, and check status and also do other tasks while waiting instead of assuming something was broken.
## •	My screen-lock configuration profile reached the VM and took effect, which confirmed the full path from the admin center to the device.
## •	App install behavior; learned how to know why certain apps are “Not applicable” because it was set to install for a user but assigned to a device.
## •	I read specific error codes (-2145833241 and 0x80190190) and checked likely causes one at a time: DNS, licensing, MDM scope, Windows Information Protection, and edition.
## •	I worked around constraints, including a restricted developer tenant, a 30-day trial, and an 8 GB Mac, where giving the VM the right amount of RAM and removing the CPU cap made it usable.
## •	I documented problems and fixes with screenshots so the work can be repeated and reviewed.
