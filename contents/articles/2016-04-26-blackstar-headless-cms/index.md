---
title: Hosting a custom application inside of a CMS is a terrible idea
author: Liam McLennan
date: 2016-04-26 20:32
template: article.jade
---

> Building applications inside of a content management system is now a failed experiment. A new style of api-first, headless content management systems inverts the relationship and provides a pleasant and productive development process. 

<img src="dolls.jpg" alt="Russian dolls"/>

The Benefits of App-In-CMS
=========================

The appeal of building an application inside of a CMS is that you get stuff for free. Us greedy software developers cannot resist the siren song of pre-built authentication, security, widgets and of course content management. For very simple, short-lived applications where control over the output or quality engineering are not important then app-in-cms may be OK. 

Hosting a custom application inside of a CMS is a terrible idea
==========================================================

The old idea about how to merge user editable content into custom applications was to build applications inside of content management systems. Early productivity advantages (such as built-in user management and authentication) are soon overtaken by compromises required to stay within the CMS framework and challenges related to versioning and deployment. Architectural and functional compromises soon leak out and affect the user. It is common to get three months into a CMS-based application development project only to realise that it should have been a standalone application and that the CMS framework is now holding you back. When designing and building software applications we should try to avoid such architectural dead ends.

<img src="deadend.jpg" alt="Dead end"/>

The biggest problem with app-in-cms is that the application developer surrenders control of the application. The CMS is a framework that you must work within and this leads to the aforementioned compromises of design and functionality. 

Confessions of an app-in-cms developer
=====================

I know that app-in-cms is a bad idea, because I have done it, and seen it done. For me, this was DotNetNuke. I have seen the same experiment repeated with Sharepoint, Sitefinity, Kentico and Umbraco. The result is a predictable rapid accumulation of unresolvable technical debt.  

Years after my last DotNetNuke project I worked on a large ecommerce application. As a business to consumer ecommerce application marketing was a primary concern, and that means that the ability to manage content is essential. The problem was solved by reservering fixed banner ads throughout the site. When the marketing team wished to modify content they would create new banner ads of the prescribed dimensions and upload them to the site. When the user's cache expired they would receive the new banner ad with its new content. This process was labour intensive, horribly inflexible and bad for users who were subjected to too much text in banner ad images. The landing page of the site, and some associated content pages, were served from a CMS. When the user started an ecommerce process they transitioned into a custom application that had to be synchronized to the CMS front-end so that the transition was not obvious to the user. What a mess. 

More recently I was involved in another business-to-consumer project that required content management for the marketing department. After investigating possible integration with a content management system the team decided to instead build their own micro CMS features into the application so that application administrators could modify certain pieces of content. This is a better solution than replaceable banner ads but it is still inflexible and costly, having consumed some $10k of the project's budget. 

Hopefully I have given sufficient examples to demonstrate that content management within custom applications is a common problem, and app-in-cms is not the answer.  



