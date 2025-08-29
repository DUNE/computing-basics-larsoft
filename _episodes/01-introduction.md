---
title: Online Tutorial Welcome and Introduction 
teaching: 5
exercises: 0
questions:
- What should I expect in participating in this Tutorial? 
objectives:  
- Introduce instructors and mentors.
- Provide overview of the modules
- Spotlight helpful network provided by Slack channel.
keypoints:
- This tutorial is brought to you by the DUNE Computing Consortium.
- The goals are to give you the computing basis to work on DUNE.
---
## DUNE Computing Consortium

The DUNE Computing Consortium works to establish a global computing network that will handle the massive data streams produced by distributing these across the computing grid.
Selected consortia members coordinate DUNE computing activities to new members to acquaint them with the specific and DUNE software and resources.

DUNE Computing Consortium Coordinator: Michael Kirby (Brookhave National Laboratory)



<!--
## Schedule

The May 2023 DUNE computing training spanned two days: [Indico site](https://indico.fnal.gov/event/59762/timetable/#20230524).

--> 

This is a short 3 hour version of the basics.  We will be adding/offering additional tutorials.  An important one that is coming soon is:

[The LArSoft tutorial at CERN, February 3-7, 2025](https://indico.cern.ch/event/1461779/) password on the [tutorials page](https://wiki.dunescience.org/wiki/Computing_tutorials)

[Also check out the longer list of DUNE computing tutorials](https://wiki.dunescience.org/wiki/Computing_tutorials) (collaborators only)

### Workshop Introduction Video from December 2024

<!--
The session will be captured on video a placed here after the workshop for asynchronous study.
A similar session from May 2022 was captured for your asynchronous review.
-->

<center>
<iframe width="560" height="315" src="https://www.youtube.com/embed/Yfa5SQlc8yM" title="DUNE Computing Tutorial Dec 2024 Introduction" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</center>


## Basic setup reminder

You should have gone through the [setup sequence]({{ site.baseurl }}/setup)

As a reminder you need to choose between running on sl7 in a container or al9.  You do NOT want to mix them.

You also need to be starting in a clean terminal session.  We recommend not having a `.profile` or `.login` at all and deliberately creating  setup scripts that you source whenever you start using DUNE code. 

~~~ 
source mysetup7.sh
~~~
{: .language-bash}

Here are some example scripts that do most of the setups explained in this tutorial.  You need to store these in your home area, source them every time you log in, and possibly update them as code versions evolve. 


- [SL7 setup](https://dune.github.io/computing-basics/sl7_setup)

- [AL9 setup](https://dune.github.io/computing-basics//al9_setup)

> ## If you run into problems, check out the [Common Error Messages]({{ site.baseurl }}/ErrorMessages) page and the [FAQ page](https://github.com/orgs/DUNE/projects/19/)
> if that doesn't help, use Slack to ask us about the problem - there is always a new one cropping up.
{: .challenge} 

## Instructional Crew

**Organizers:**
- Heidi Schellman (Oregon State University /FNAL)
- David DeMuth (Valley City State University)

**Module Authors (in order of appearance in the schedule):**
- Michael Kirby (FNAL): storage spaces
- Steven Timm (FNAL): data management 
- Tom Junk (FNAL): art and LArSoft


**Mentors**
- Amit Bashyal (ANL)
- Aaron Higuera (Rice University)
- Pengfei Ding (FNAL)
- Barnali Chowdury (ANL)
- Nilay Bostan (University of Iowa)

## Support

<!--
There will be live documents linked from [Indico][indico-event-link] for each [Zoom][zoom-link] session. You can write questions there, anonymously or not, and experts will reply. The chat on Zoom can quickly saturate so this is a more convenient solution and proved very successful at the previous training. We will collect all questions and release a Q&A after the event.
-->

You must be on the DUNE Collaboration member list and have a valid FNAL or CERN account. See the old [Indico Requirement page][indico-event-requirements] for more information. Windows users are invited to review the [Windows Setup page]({{ site.baseurl }}/Windows.html).

You should join the DUNE Slack instance and look in [#computing-training-basics](https://dunescience.slack.com/archives/C02TJDHUQPR) for help with this tutorial

go to [https://atwork.dunescience.org/tools/](https://atwork.dunescience.org/tools/) scroll down to Slack and request an invite.  Please do not do this if you are already in DUNE Slack.

The livedoc is here [livedoc](https://docs.google.com/document/d/1QNK-hKPqLIVaecRyg9q4QZOHNwAZgq32oHVuboG_AvQ/edit?usp=sharing)






{%include links.md%} 

[metacat]: https://dune.github.io/DataCatalogDocs/
[rucio]: https://rucio.github.io/documentation/
<!--[indico-event-link]: https://indico.fnal.gov/event/59762/
[slack-join-link]: https://dunescience.slack.com/
[zoom-link]: https://fnal.zoom.us/
[indico-event-requirements]: https://indico.fnal.gov/event/59762/page/3229-requirements
-->
