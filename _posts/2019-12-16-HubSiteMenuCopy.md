---
layout: post
title: Hubsite Menu Copy
---

## Introduction

Each hubsite can have it's own menu, and that's fine. But, sometimes we need to have the same menu across multiple hub sites. There's no out of the box way to do this, so once again, PowerShell is here to ride in and save the day. 
The secret to making this work is the marvellous PNP PowerShell, and the commands which enable access to the navigation menu. The documentation doesn't say how to 'get at' the hubsite menu, but if you access the navigation menu on the hubsite, that's what you get.

## How the code works

In the example below we first delete the existing menu in the target site and then add it all in again. In the example below we're hardcoding the username and password, but in production you may want to prompt for credentials, or use one of the authentication methods that work with 2FA.

## Code

```

$pnpUsername = "user@name"
$pnpPassword = "************"
$SourceHubSiteUrl = "https://AcmeIncIntranet.sharepoint.com/sites/sourceSite"
$TargetHubSite = "https://AcmeIncIntranet.sharepoint.com/sites/targetSite"


[SecureString]$SecurePassword = ConvertTo-SecureString $pnpPassword -AsPlainText -Force
[System.Management.Automation.PSCredential]$PSCredentials = New-Object System.Management.Automation.PSCredential($pnpUsername, $SecurePassword)


#delete the existing menu
Connect-PnPOnline -Url $TargetHubSite -Credentials $PSCredentials
$TopNavs=Get-PnPNavigationNode -Location TopNavigationBar   | Select Title, Url, Id

foreach($TopNav in $TopNavs)
{
    Write-Host "Deleting " $TopNav.Id " Title: " $TopNav.Title "Url: " $TopNav.Url -ForegroundColor DarkRed
    Remove-PnPNavigationNode -Identity  $TopNav.Id -Force
}



Connect-PnPOnline -Url $SourceHubSiteUrl -Credentials $PSCredentials
$TopNavs=Get-PnPNavigationNode -Location TopNavigationBar   | Select Title, Url, Id

foreach($TopNav in $TopNavs)
{

    Connect-PnPOnline -Url $SourceHubSiteUrl -Credentials $PSCredentials

    $node = Get-PnPNavigationNode -Id $TopNav.Id 

    Write-Host "Top Level Id: "$TopNav.Id" Title: "$TopNav.Title " Url: "$TopNav.Url -ForegroundColor Yellow
  
    Connect-PnPOnline -Url $TargetHubSite -Credentials $PSCredentials
    $newTopLevel = Add-PnPNavigationNode -Title $TopNav.Title -Url $TopNav.Url -Location "TopNavigationBar"

    if($node.Children)
    {
        $child = $node.Children | Select Title, Url, Id     #gets topnavs children

        foreach($child in $node.Children){

        Write-Host "   -- ChildId: "$child.Id"  Title: " $child.Title "Url: "$child.Url -ForegroundColor Green

        #add the level 2 items
        Connect-PnPOnline -Url $TargetHubSite -Credentials $PSCredentials
        $neSubLevel1 = Add-PnPNavigationNode -Title $child.Title -Url $child.Url -Location "TopNavigationBar" -Parent $newTopLevel.Id


        #get 3rd level terms

        Connect-PnPOnline -Url $SourceHubSiteUrl -Credentials $PSCredentials

        $subChildNode = Get-PnPNavigationNode -Id $child.Id 

        if($subChildNode.Children)
        {
          #gets childrens children
          $subChild = $subChildNode.Children | Select Title, Url, Id   

          foreach($subChild in $subChildNode.Children)
          {
            Write-Host "     -- SubChildId: "$subChild.Id" Title: " $subChild.Title "Url: "$subChild.Url -ForegroundColor DarkGreen


                #add the level 2 items
                Connect-PnPOnline -Url $TargetHubSite -Credentials $PSCredentials
                $newSubLevel2 = Add-PnPNavigationNode -Title $subChild.Title -Url $subChild.Url -Location "TopNavigationBar" -Parent $neSubLevel1.Id

          }
        }
        }
    }
}