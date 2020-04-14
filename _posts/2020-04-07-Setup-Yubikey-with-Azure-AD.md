---
layout: post
title: Yubikey and Azure Active Directory login
---

_Take the fastlane around passwords_
## What are we trying to do? ##

In a previous post I covered just how easy [logging in with a Yubikey](https://dylanhayes.github.io/2FA-Going-beyond-the-phone/) or other similar FIDO2 devices is. 
Now we're going to setup this on a new Office 365 Tenant. This feature requires Azure Active Directory and uses a feature that at the time of writing is still in preview.

### Prerequisites ###

* A Yubikey or similar device. I used a [Yubikey 5 NFC](https://www.yubico.com/works-with-yubikey/catalog/azure-active-directory/), but other keys may work as long they support the correct standards - FIDO2, and some additional extra requirements. 

* A modern browser, and modern Operating System. I have tested this on MacOS and Windows 10 (1903 onwards).

* Office 365, with Azure AD Premium P1 or P2. There are cases where you don't need P1/2 for certain narrow scenarios. As ever, licensing is complex and could fill an entire new blog post. If you have an E3 license, you will need P1. If you are lucky enough to have E5, you will not.


### Azure Portal ###

Sign in to the [Azure portal](https://portal.azure.com/) to let the fun begin.

Head to Azure Active Directory from the ribbon of Azure services, and then go to 'Security'.

![Azure Portal](../../images/2020-04-07/mfa_az_portal.PNG)

In security click on 'Authentication Methods'. You will be reminded that you don't have AAD Premium at this point. If you don't already, start a trial of Azure Active Directory Premium 1.

Now click on Authentication Methods. You should see a list of authentication methods, and the number of users enrolled in them. 

![Authentication Methods](../../images/2020-04-07/authmethods.PNG)

Click on the FIDO2 Security Key method.

You will need to enable it, and then choose which users the policy applies to. For a first attempt, I would suggest targeting a single user. Ensure you have selected 'Allow self-service set up'.

![Allow self-service set up](../../images/2020-04-07/selectmfauser.PNG)

<a name="selfservice">Important - Remember to Allow Self Service</a>
Then you must follow the link at the top of the page with "Click here to enable users for the enhanced registration preview"

Now select a group who should be able to use the new preview features. *Unless you do this you will not be able to access the new authentication methods.*

![Preview features](../../images/2020-04-07/previewfeatures.PNG)


### Self service set up ###

In a new browser window login to [https://myprofile.microsoft.com](https://myprofile.microsoft.com) as the user you want to set the key for

Select 'Security Info' from the menu on the left. You will be prompted for your password again to make sure that you are you. If you have already turned on MFA (please say it's true), you will have to reauthenticate in MFA. *If you have just emerged from a 100 years sleep, and don't yet have MFA enabled you will see a slightly different interface where you will need to set up your first MFA method.*

Note: if you will see the classic rather than modern interface, you need to enable the preview features - which is the [last step](#selfservice) above

![Add security key](../../images/2020-04-07/addseckey.PNG)

Now, just 'Add method' and choose Security Key

You will probably be asked to sign in with MFA again.

Now choose a device, and select a USB device

![Choose device](../../images/2020-04-07/choosedevice.PNG)

You will then be walked through setting up the key. If you have entered a PIN previously for the device you will need to provide this

![Setup key](../../images/2020-04-07/pinentry.PNG)

You will then need to touch the device and allow the device to be registered - this will vary slightly depending on browser

Then you will go back to the mysignins.microsoft.com site to finish by providing a name for the key key

![Setup key](../../images/2020-04-07/seekey.PNG)

All done! Use the key instead of a username and password to [login in with a Yubikey](https://dylanhayes.github.io/2FA-Going-beyond-the-phone/) instead. 

![Setup key](../../images/2020-04-07/keyready.PNG)
