---
layout: post
title: SharePoint Home sites and Site Swap
---

# Take me home to SharePoint

It's 2020, and time to finally get rid of that classic root site on your SharePoint tenant!
Although there have been work-arounds to put a modern site in the root, now that site swap and home sites are in general availability, there's a better way. 

_I've tried this on a couple of tenants in the first few weeks of January, so this should be available on your tenant._

Here's what we are going to do. First we'll create what is going to be our root site as new communications site (or you can do this to an existing communications site). In this example it's called 'Homesite'. It can't be member of a hub when we promote it to a homesite- so make sure it's not. This will be your shiny new root home site when you are done.

Now, it's PowerShell time. You'll need the latest 
[PowerShell libraries](https://www.microsoft.com/en-us/download/details.aspx?id=35588)
, and an account with SharePoint admin rights.

In this example, for simplicity we're going to login with a user name and password. In production, admin accounts should be secured with MFA of course.

Once we've logged in, We're going to make the site 'Homesite' into a homesite with **Set-SPOHomeSite**. 

Then, **Invoke-SPOSiteSwap** is going to swap the site called 'Homesite' to the root, and put the old root into a site called 'Archive'. It's really that simple. The script will take a minute or two to run as there's a lot going on.

## Code

```PowerShell
$spoUsername = "**********@dylanhayes.onmicrosoft.com"
$spoPassword = "**********"
$SourceHubSiteUrl = "https://dylanhayes.sharepoint.com/sites/HomeSite"


[SecureString]$SecurePassword = ConvertTo-SecureString $spoPassword -AsPlainText -Force
[System.Management.Automation.PSCredential]$PSCredentials = New-Object System.Management.Automation.PSCredential($spoUsername, $SecurePassword)

Connect-SPOService -Url https://dylanhayes-admin.sharepoint.com -Credential $PSCredentials

Set-SPOHomeSite -HomeSiteUrl https://dylanhayes.sharepoint.com/HomeSite

Invoke-SPOSiteSwap -SourceUrl https://dylanhayes.sharepoint.com/sites/HomeSite -TargetUrl https://dylanhayes.sharepoint.com -ArchiveUrl https://dylanhayes.sharepoint.com/sites/Archive

```

## Areas of confusion

A homesite may be part of a hubsite, but you must un-enroll from the hub whilst you promote it. Depending on the structure and size of your intranet, you may decide the home site is either part of one of your existing hubs, or merits a hub all of its own for corporate level sites.

You can of course have many hub sites, but there can be only one homesite.  I haven't actually tried making another site into a homesite yet to see what happens. 

The homesite doesn't have to be at the root, but it makes a lot more sense that it is, so if somebody just types the root URL for your site they end up at the home site.

Although you create a homesite from a regular communications sites, and it looks no different to a regular communication site, some blogs suggest you can create a new communications site from a template for an 'Organisation'. As of late Jan 2020, I can't see this feature on any of the tenants I have access to. Maybe I'm missing something? If I do spot how to do this, I'll amend the blog to document how.


## Conclusions

Site swap has been something we've all been asking about for a long while. There's quite a lot going on behind the sceans to make it happen, as any access to the old URL (external links and bookmarks) need to be redirected to the new URL. There's also the stuff that happens in the background (API calls to SharePoint) that need to cope with the root site moving. 

So, what are homesites then? Well, they are as the name suggests intended to be the home site for an intranet, there are some additional things happening below the surface to help them in their mission to be the place users land on the intranet.

- Search scope is tenant wide, rather than hub wide or site wide (you will notice the search box says 'search across sites').
- News published here is considered a trusted source of organisation wide news, and the site is setup as organisation news site.
- The SharePoint mobile app will go to this site if you hit the 'home' button.
- The new SharePoint start page will take it's branding from this site. 