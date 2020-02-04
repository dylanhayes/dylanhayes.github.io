---
layout: post
title: Modern SharePoint and RSS Part 2 - extracting metadate from a webpage
---

## What are we trying to do? ##

In a previous post I discussed how it was actually very easy to 
[extract RSS into SharePoint news](2020-01-30/RSS-not-dead-yet.md), but I wasn't happy with the generic image for each RSS item, as news just looks so much nicer with a image for each. This is what you see if you add a link via the UI. What we need to do is add some further steps to our flow to go off and page scrape the destination of the link to get an image for the story. 

## Salvation from Og:Image ##

Rather than look for a main image by parsing the page body HTML which is inevitably going to be fragile and complicated, wouldn't it be great if there was a standard way to do this? It turns out that is, many bigger news organisations provide an 'og:image' metatag with our image which is intended to do exactly that. As always, with these things there's no certaincy it will exist, but it's our best bet. 'og' means open graph, and an example of the image looks something like this

```HTML
<meta property="og:image" content="http://ia.media-imdb.com/images/rock.jpg" /> 
```

## Parsing HTML in Power Automate ##

### Here be dragons ###

With a further step we can go out and grab the HTML for the target of the link. 
To accomplish this we need the HTTP connector, which is a premium action, i.e. this automation is going to incur additional costs. At this point, life takes a turn for the complicated, as parsing HTML in flow is not at all nice. I first thought that HTML is just XML, then I could parse the XML with XPATH. But, most webpages are not valid XML. I did manage to make something work with lots of hunting for certain characters and slicing out substrings, but it was horribly complex and broke easily. I then found with a mixture of disapointment and relief that regular expressions are not available in Power Automate without additional 3rd party connectors.

## Overview of a workable solution ##

I finally found that if I converted the HTML into an array using the new line as a separator, I had an array containing all the HTML line by line. Then, using the select command in the obscure but under-utilised data operations actions, I could easily pull out the metadata tag containing the `og:image`. I found that splitting the string containing the `og:image` on double quotes finally got me the URL. Phew!

To my suprise and delight I found this worked perfectly with the HTTP POST to the SharePoint API once I had added the URL to the image and changed the `IsBannerImageUrlExternal` to true.

## Step by step ##

There's a lot of steps in the previous description. 
_We could probably collapse some of the operations together with all the parsing, but in this case more steps are easier to explain, as really complicated expressions can be tricky to understand._ 
Let's go through each of them in turn.

RSS feed- exactly as before

![rss feed](../../images/2020-02-01/rss feed.PNG)

HTTP GET to go and get the content of the link target.

![http get](../../images/2020-02-01/http get.PNG)

Creating a variable for the `og:image` URL. 

![Variable for images](../../images/2020-02-01/init image url.PNG)

Creating a variable for newline. *Note the value is a return not an empty line!*

![Newline variable](../../images/2020-02-01/init newline.PNG)


Parsing the HTML into an array. Note how we use the newline variable as the separator.

`split(body('HTTP'), variables('newline'))`

![split string](../../images/2020-02-01/array split.PNG)

Filter the array to find the `og:image` line

![split string](../../images/2020-02-01/filter array.PNG)

Now we need to check we actually have found `og:image` tag so we check the filter operation found something

![has image?](../../images/2020-02-01/has ogimage.PNG)

Now we need to cast the array output from the previous step into a string, and then split the string on double quotes and take 4th element. This is a fiddly line to enter and there's a lot to go wrong, but without regex we don't have much choice.

`split(join(body('Filter_array_to_extract_og:image'),''), '"')[3]`

![get image url](../../images/2020-02-01/get image url.PNG)


And finally our penultimate step to escape the double quotes in the RSS body

``` 
replace(triggerBody()?['summary'],'"', '\"')
```

![Replace](../images/2020-01-30/story body replace.PNG)

Finally the POST where we compose our REST call to the SharePoint API. Note the 
`IsBannerImageUrlExternal` should be true, as our image resides outside Office 365.

![News with images](../../images/2020-02-01/http request.PNG)

## And now here is the news again ##

The news web part is very versitile and has many different options, and will cope brilliantly with different sizedscreens. Now we have images it can really shine.

*News with images*
![News with images](../../images/2020-02-01/news with images.PNG)

*News as image tiles*
![News with images](../../images/2020-02-01/news with tiles.PNG)


## Conclusions ##

Just getting started with this refinement to the original idea of reading RSS requires a the HTTP action, which is a premium Power Automate feature. So the solution has to offer real business value, and adding images to RSS items may not 
always be important enough to justify the cost.  
Parsing HTML is painful, involves lots of steps and is potentially fragile. 

Despite these two negatives, the overall solution looks really good, as news is such a rich way to display this sort of content. There's no reason why the same can't be applied to other use cases involving picking out the og:image metatag from internet pages, or indeed that the same technique can't be applied to other web page parsing needs. This is one area that a custom connector which integrates with an existing HTML parsing library could really make life easier (custom connectors are premium features so it wouldn't be any cheaper to run) as even something as obtuse as XPATH would still be more robust and terse than fishing for strings in a big bowl of tag soup.

If cost is a barrier, then recreating this in Azure logic apps may be the way to go, espcially if the volume of RSS feeds is low, as costs are consumption based. 


