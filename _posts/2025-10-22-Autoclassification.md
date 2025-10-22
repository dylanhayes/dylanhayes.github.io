---
layout: post
title: SharePoint Knowledge Agent - Automatic classification
---


Previously we've [been looking at autofill](2025-06-07-In-Praise-Of-Autofill.md). It's useful, but there's more that can be done with classification. Classification is a useful first step in dealing with documents - as by knowing what something is, you know what must be done.

## Prerequisites ##

As of late October 2025, SharePoint now features the SharePoint Knowledge Agent in preview. This requires PowerShell to enable (this will change), and Microsoft 365 Copilot. Additionally you will need to have enabled Autofill columns - which requires turning on in admin centre.

First we're going to do this the easy way using the SharePoint Knowledge Agent.

## Cheat Mode ##

You can go straight to the SharePoint Knowledge Agent, and select 'Organize this library' then select the prepared prompt for 'classifiy documents', and run the prompt. This will create a classification column, which can also be done manually - but with the AI creating the classification choices. This works reasonably well if you have good examples in the library already. For fine grained control, especially if you want to apply further processes depending on the type (for example rules in the SharePoint Knowledge Agent) you will probably want to define the choices yourself.

![SharePoint Knowledge Agent](../images/2025-10-22/9a.png)

Opening the SharePoint Knowledge Agent


![Organise Library](../images/2025-10-22/9b.png)

Classify Documents

![Classification Column](../images/2025-10-22/9c.png)

First pass

