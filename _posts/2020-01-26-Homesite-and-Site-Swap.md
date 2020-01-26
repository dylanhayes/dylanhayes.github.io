---
layout: post
title: SharePoint Home sites and Site Swap
---

It's 2020, and time to finally get rid of that classic root site on your SharePoint tenant!
Although there have been work arounds to put a modern site in the root, now that site swap and home sites are in general availability, there's a better way. 
_I've tried this on a couple of tenants in the first few weeks of January, so this should be available on your tenant._

Here's what we are going to do. First we'll create what is going to be our root site as new communications site. In this example it's called 'Homesite'. It can't be member of a hub - so make sure it's not. This will be your shiny new root home site when you are done.

Now, it's PowerShell time. You'll need the latest 
[PowerShell libraries](https://www.microsoft.com/en-us/download/details.aspx?id=35588)
, and an account with SharePoint admin rights.

In this example, we're going to login with a user name and password. In production, admin accounts should be secured with MFA.

Once we've logged in, We're going to make the site 'Homesite' into a homesite with **Set-SPOHomeSite**. 

**Invoke-SPOSiteSwap** is going to swap the site called 'Homesite' to the root, and put the old one into a site called 'Archive'. It's really that simple.



$spoUsername = "*****t*****@dylanhayes.onmicrosoft.com"
$spoPassword = "*****1*****"
$SourceHubSiteUrl = "https://dylanhayes.sharepoint.com/sites/HomeSite"


[SecureString]$SecurePassword = ConvertTo-SecureString $spoPassword -AsPlainText -Force
[System.Management.Automation.PSCredential]$PSCredentials = New-Object System.Management.Automation.PSCredential($spoUsername, $SecurePassword)

Connect-SPOService -Url https://dylanhayes-admin.sharepoint.com -Credential $PSCredentials

Set-SPOHomeSite -HomeSiteUrl https://dylanhayes.sharepoint.com/HomeSite

Invoke-SPOSiteSwap -SourceUrl https://dylanhayes.sharepoint.com/sites/HomeSite -TargetUrl https://dylanhayes.sharepoint.com -ArchiveUrl https://dylanhayes.sharepoint.com/sites/Archive



