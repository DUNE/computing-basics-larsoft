---
title: Introduction to art and LArSoft (2025 - Apptainer version)
teaching: 50
exercises: 0
questions:
- Why do we need a complicated software framework? Can't I just write standalone code?
objectives:  
- Learn what services the *art* framework provides.
- Learn how the LArSoft tookit is organized and how to use it.
keypoints:
- Art provides the tools physicists in a large collaboration need in order to contribute software to a large, shared effort without getting in each others' way.
- Art helps us keep track of our data and job configuration, reducing the chances of producing mystery data that no one knows where it came from.
- LArSoft is a set of simulation and reconstruction tools shared among the liquid-argon TPC collaborations.
---

#### Session Video

The session video on December 10, 2024 was captured for your asynchronous review. 

<center>
<iframe width="560" height="315" src="https://www.youtube.com/embed/u_HnXTxUs9Y" title="DUNE Computing Tutorial Dec 2024 Data Management" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</center>

<!--
#### Live Notes

Participants are encouraged to monitor and utilize the [Livedoc for May. 2023](https://docs.google.com/document/d/19XMQqQ0YV2AtR5OdJJkXoDkuRLWv30BnHY9C5N92uYs/edit?usp=sharing) to ask questions and learn.  For reference, the [Livedoc from Jan. 2023](https://docs.google.com/document/d/1sgRQPQn1OCMEUHAk28bTPhZoySdT5NUSDnW07aL-iQU/edit?usp=sharing) is provided.
-->

<!--
#### Temporary Instructor Note: 

The May 2023 version of the DUNE Software and Computing training was imported from the May 2022 version because it was a two day event, similar to this one, see [04-intro-art-larsoft.md (May 2022)](https://github.com/DUNE/computing-training-basics/blob/gh-pages/_episodes/04-intro-art-larsoft.md) for reference.

This lesson (06-intro-art-larsoft.md) was imported from the [Jan. 2023 lesson](https://github.com/DUNE/computing-training-basics-short/blob/gh-pages/_episodes/04-intro-art-larsoft.md) which was a one half day version of the training.

This lesson includes collapsable quiz blocks which are encouraged, a blank quiz question block included at the end of the page. -->

<!-- The official timetable for this training event is on the [Indico site](https://indico.fnal.gov/event/59762/timetable/#20230524).

-->

## Advertisement -- February 2025 LArSoft workshop at CERN

[https://indico.cern.ch/event/1461779/overview](https://indico.cern.ch/event/1461779/overview)

This page is protected by a password.  Dom Brailsford sent this password in an e-mail to the DUNE Collaboration on November 6, 2024.

## Introduction to *art*

*Art* is the framework used for the offline software used to process LArTPC data from the far detector and the ProtoDUNEs. It was chosen not only because of the features it provides, but also because it allows DUNE to use and share algorithms developed for other LArTPC experiments, such as ArgoNeuT, LArIAT, MicroBooNE and ICARUS. The section below describes LArSoft, a shared software toolkit. Art is also used by the NOvA and mu2e experiments. The primary language for *art* and experiment-specific plug-ins is C++.

The *art* wiki page is here: [https://cdcvs.fnal.gov/redmine/projects/art/wiki][art-wiki]. It contains important information on command-line utilities, how to configure an *art* job, how to define, read in and write out data products, how and when to use *art* modules, services, and tools.

*Art* features:

1. Defines the event loop
2. Manages event data storage memory and prevents unintended overwrites
3. Input file interface -- allows ganging together input files
4. Schedules module execution
5. Defines a standard way to store data products in *art*-formatted ROOT files
6. Defines a format for associations between data products (for example, tracks have hits, and associations between  tracks and hits can be made via art's association mechanism).
7. Provides a uniform job configuration interface
8. Stores job configuration information in *art*-formatted root files.
9. Output file control -- lets you define output filenames based on parts of the input filename.
10. Message handling
11. Random number control
12. Exception handling

The configuration storage is particularly useful if you receive a data file from a colleague, or find one in a data repository and you want to know more about how it was produced, with what settings.

### Getting set up to try the tools

Log in to a `dunegpvm*.fnal.gov` or `lxplus.cern.ch` machine and set up your environment (This script is defined in Exercise 5 of https://dune.github.io/computing-basics/setup.html)

> ## Note
> For now do this in the Apptainer.  Due to the need to set up the container separately at CERN, on the build nodes, and  on the gpvms due to /pnfs mounts being different, and the need to keep your environment clean for use on other experiments, it is best to define aliases in your .profile or .bashrc or other login script you use to define aliases.  A set of convenient aliases is
{: .challenge}

~~~
alias dunesl7="/cvmfs/oasis.opensciencegrid.org/mis/apptainer/current/bin/apptainer shell --shell=/bin/bash -B /cvmfs,/exp,/nashome,/pnfs/dune,/opt,/run/user,/etc/hostname,/etc/hosts,/etc/krb5.conf --ipc --pid /cvmfs/singularity.opensciencegrid.org/fermilab/fnal-dev-sl7:latest"

alias dunesl7build="/cvmfs/oasis.opensciencegrid.org/mis/apptainer/current/bin/apptainer shell --shell=/bin/bash -B /cvmfs,/exp,/build,/nashome,/opt,/run/user,/etc/hostname,/etc/hosts,/etc/krb5.conf --ipc --pid /cvmfs/singularity.opensciencegrid.org/fermilab/fnal-dev-sl7:latest"

alias dunesl7CERN="/cvmfs/oasis.opensciencegrid.org/mis/apptainer/current/bin/apptainer shell --shell=/bin/bash \
-B /cvmfs,/afs,/opt,/run/user,/etc/hostname --ipc --pid \
/cvmfs/singularity.opensciencegrid.org/fermilab/fnal-dev-sl7:latest"

alias dunesetups="source /cvmfs/dune.opensciencegrid.org/products/dune/setup_dune.sh"
~~~
{: .language-bash}

Then you can use the appropriate alias to start the SL7 container on either the build node or the gpvms or lxplus. Starting a container gives you a very bare environment -- it does not source your .profile for you; you have to do that yourself.  The examples below assume you put the aliases above in your .profile or in a script sourced by your .profile.  I always set the prompt variable PS1 in my profile so I can tell that I've sourced it.

~~~
PS1="<`hostname`> "; export PS1
~~~
{: .language-bash}

Then when you log in, you can type these commands to set up your environment in a container:
~~~
dunesl7
source .profile
dunesetups

export DUNELAR_VERSION=v10_07_00d00
export DUNELAR_QUALIFIER=e26:prof
setup dunesw $DUNELAR_VERSION -q $DUNELAR_QUALIFIER

setup_fnal_security
~~~
{: .language-bash}

~~~
# define a sample file
export SAMPLE_FILE=root://fndca1.fnal.gov:1094//pnfs/fnal.gov/usr/dune/persistent/users/schellma/tutorial_2025/NNBarAtm_hA_BR_dune10kt_1x2x6_54053565_607_20220331T192335Z_gen_g4_detsim_reco_65751406_0_20230125T150414Z_reReco.root
~~~
{: .language-bash}

<!-- FIXME get a CERN sample file -->

The examples below will refer to files in `dCache` at Fermilab which can best be accessed via `xrootd`. 

**For those with no access to Fermilab computing resources but with a CERN account:**  
Copies are stored in `/afs/cern.ch/work/t/tjunk/public/jan2023tutorialfiles/`.

The follow-up of this tutorial provides help on how to find data and MC files in storage.

You can list available versions of `dunesw` installed in `CVMFS` with this command:

~~~
ups list -aK+ dunesw
~~~
{: .language-bash}

The output is not sorted, although portions of it may look sorted. Do not depend on it being sorted. The string indicating the version is called the version tag (v09_72_01d00 here). The qualifiers are e26 and prof. Qualifiers can be entered in any order and are separated by colons.  "e26" corresponds to a specific version of the GNU compiler -- v9.3.0.   We also compile with `clang` -- the compiler qualifier for that is "c7".

"prof" means "compiled with optimizations turned on." "debug" means "compiled with optimizations turned off". More information on qualifiers is [here][about-qualifiers].

In addition to the version and qualifiers, `UPS` products have "flavors". This refers to the operating system type and version. Older versions of DUNE software supported `SL6` and some versions of macOS. Currently only SL7 and the compatible CentOS 7 are supported. The flavor of a product is automatically selected to match your current operating system when you set up a product. If a product does not have a compatible flavor, you will get an error message.  "Unflavored" products are ones that do not depend on the operating-system libraries.  They are listed with a flavor of "NULL". 

There is a setup command provided by the operating system -- you usually don't want to use it (at least not when developing DUNE software). If you haven't yet sourced the `setup_dune.sh` script in `CVMFS` above but type `setup xyz` anyway, you will get the system setup command, which will ask you for the root password. Just `control-C` out of it, source the `setup_dune.sh` script, and try again.  On AL9 and the SL7 container, there is no system setup command so you will get "command not found" if you haven't yet set up UPS.

UPS's setup command (find out where it lives with this command):

~~~
type setup
~~~
{: .language-bash}

will not only set up the product you specify (in the instructions above, dunesw), but also all dependent products with corresponding versions so that you get a consistent software environment. You can get a list of everything that's set up with this command

~~~
 ups active
~~~
{: .language-bash}

It is often useful to pipe the output through grep to find a particular product.

~~~
 ups active | grep geant4
~~~
{: .language-bash}

for example, to see what version of geant4 you have set up.

### *Art* command-line tools

All of these command-line tools have online help. Invoke the help feature with the `--help` command-line option. Example:

~~~
config_dumper --help
~~~
{: .language-bash}

Docmentation on art command-line tools is available on the [art wiki page][art-wiki].

#### config_dumper

Configuration information for a file can be printed with config_dumper. 

~~~
config_dumper -P <artrootfile>
~~~
{: .language-bash}

Try it out:
~~~
config_dumper -P $SAMPLE_FILE
~~~
{: .language-bash}

The output is an executable `fcl` file, sent to stdout. We recommend redirecting the output to a file that you can look at in a text editor:

Try it out:
~~~
config_dumper -P $SAMPLE_FILE > tmp.fcl
~~~
{: .language-bash}

Your shell may be configured with `noclobber`, meaning that if you already have a file called `tmp.fcl`, the shell will refuse to overwrite it. Just `rm tmp.fcl` and try again.

The `-P` option to `config_dumper` is needed to tell `config_dumper` to print out all processing configuration `fcl` parameters. The default behavior of `config_dumper` prints out only a subset of the configuration parameters, and is most notably missing art services configuration.


> ## Quiz 
>
> Quiz questions from the output of the above run of `config_dumper`:
>
>  1.  What generators were used?  What physics processes are simulated in this file?
>  2.  What geometry is used?  (hint:  look for "GDML" or "gdml")
>  3.  What electron lifetime was assumed?
>  4.  What is the readout window size? 
> 
{: .solution}

 
#### fhicl-dump

You can parse a `FCL` file with `fhicl-dump`. 

Try it out:
~~~
fhicl-dump protoDUNE_refactored_g4_stage2.fcl
~~~
{: .language-bash}

See the section below on `FCL` files for more information on what you're looking at.

#### count_events

Try it out:
~~~
count_events $SAMPLE_FILE
~~~
{: .language-bash}


#### product_sizes_dumper

You can get a peek at what's inside an *art*ROOT file with `product_sizes_dumper`.

Try it out:
~~~
product_sizes_dumper -f 0 $SAMPLE_FILE
~~~
{: .language-bash}

It is also useful to redirect the output of this command to a file so you can look at it with a text editor and search for items of interest. This command lists the sizes of the `TBranches` in the `Events TTree` in the *art*ROOT file. There is one `TBranch` per data product, and the name of the `TBranch` is the data product name, an "s" is appended (even if the plural of the data product name doesn't make sense with just an "s" on the end), an underscore, then the module label that made the data product, an underscore, the instance name, an underscore, and the process name and a period.


Quiz questions, looking at the output from above.

> ## Quiz
> Questions:
> 1.  What is the name of the data product that takes up the most space in the file?
> 2.  What the module label for this data product?
> 3.  What is the module instance name for this data product?   (This question is tricky.  You have to count underscores here).
> 4.  How many different modules produced simb::MCTruth data products?  What are their module labels?
> 5.  How many different modules produced recob::Hit data products?  What are their module labels?
{: .solution}

You can open up an *art*ROOT file with `ROOT` and browse the `TTrees` in it with a `TBrowser`. Not all `TBranches` and leaves can be inspected easily this way, but enough can that it can save a lot of time programming if you just want to know something simple about a file such as whether it contains a particular data product and how many there are. 

Try it out
~~~
root $SAMPLE_FILE
~~~
{: .language-bash}

then at the `root` prompt, type:
~~~
new TBrowser
~~~ 
{: .language-bash}

This will be faster with `VNC`. Navigate to the `Events TTree` in the file that is automatically opened, navigate to the `TBranch` with the Argon 39 MCTruths (it's near the bottom), click on the branch icon `simb::MCTruths_ar39__SinglesGen.obj`, and click on the `NParticles()` leaf (It's near the bottom. Yes, it has a red exclamation point on it, but go ahead and click on it). How many events are there? How many 39Ar decays are there per event on average?

Header files for many data products are in [lardataobj](https://github.com/larsoft/lardataobj)   and some are in [nusimdata](https://github.com/NuSoftHEP/nusimdata).

*Art* is not constrained to using `ROOT` files -- we use HDF5-formatted files for some purposes.  ROOT has nice browsing features for inspecting ROOT-formatted files;   Some HDF5 data visualiztion tools exist, but they assume that data are in particular formats.  ROOT has the ability to display more general kinds of data (C++ classes), but it needs dictionaries for some of the more complicated ones.

The *art* main executable program is a very short stub that interprets command-line options, reads in the configuration document (a `FHiCL` file which usually includes other `FHiCL` files), and loads shared libraries, initializes software components, and schedules execution of modules. Most code we are interested in is in the form of *art* plug-ins -- modules, services, and tools. The generic executable for invoking *art* is called `art`, but a LArSoft-customized one is called `lar`. No additional customization has yet been applied so in fact, the `lar` executable has identical functionality to the `art` executable.

There is online help:

~~~
 lar --help
~~~
{: .language-bash}

All programs in the art suite have a `--help` command-line option.

Most *art* job invocations take the form 

~~~
lar -n <nevents> -c fclfile.fcl artrootfile.root
~~~
{: .language-bash}

where the input file specification is just on the command line without a command-line option. Explicit examples follow below. The `-n <nevents>` is optional -- it specifies the number of events to process. If omitted, or if `<nevents>` is bigger than the number of events in the input file, the job processes all of the events in the input file. `-n <nevents>` is important for the generator stage. There's also a handy `--nskip <nevents_to_skip>` argument if you'd like the job to start processing partway through the input file. You can steer the output with

~~~
lar -c fclfile.fcl artrootfile.root -o outputartrootfile.root -T outputhistofile.root
~~~
{: .language-bash}


The `outputhistofile.root` file contains `ROOT` objects that have been declared with the `TFileService` service in user-supplied art plug-in code (i.e. your code).

### Job configuration with FHiCL

The Fermilab Hierarchical Configuration Language, FHiCL is described  here [https://cdcvs.fnal.gov/redmine/documents/327][fhicl-described].

FHiCL is **not** a Turing-complete language: you cannot write an executable program in it. It is meant to declare values for named parameters to steer job execution and adjust algorithm parameters (such as the electron lifetime in the simulation and reconstruction). Look at `.fcl` files in installed job directories, like `$DUNESW_DIR/fcl` for examples. `Fcl` files are sought in the directory seach path `FHICL_FILE_PATH` when art starts up and when `#include` statements are processed. A fully-expanded `fcl` file with all the #include statements executed is referred to as a fhicl "document".

Parameters may be defined more than once. The last instance of a parameter definition wins out over previous ones. This makes for a common idiom in changing one or two parameters in a fhicl document. The generic pattern for making a short fcl file that modifies a parameter is:

~~~
#include "fcl_file_that_does_almost_what_I_want.fcl"
block.subblock.parameter: new_value
~~~
{: .source}

To see what block and subblock a parameter is in, use `fhcl-dump` on the parent fcl file and look for the curly brackets. You can also use

~~~
lar -c fclfile.fcl --debug-config tmp.txt --annotate
~~~ 
{: .language-bash}

which is equivalent to `fhicl-dump` with the --annotate option and piping the output to tmp.txt.

Entire blocks of parameters can be substituted in using `@local` and `@table` idioms. See the examples and documentation for guidance on how to use these. Generally they are defined in the PROLOG sections of fcl files. PROLOGs must precede all non-PROLOG definitions and if their symbols are not subsequently used they do not get put in the final job configuration document (that gets stored with the data and thus may bloat it). This is useful if there are many alternate configurations for some module and only one is chosen at a time.


Try it out:
~~~
fhicl-dump protoDUNE_refactored_g4_stage2.fcl > tmp.txt
~~~ 
{: .language-bash}

Look for the parameter `ModBoxA`. It is one of the Modified Box Model ionization parameters. See what block it is in. Here are the contents of a modified g4 stage 2 fcl file that modifies just that parameter:

~~~
#include "protoDUNE_refactored_g4_stage2.fcl"
services.LArG4Parameters.ModBoxA: 7.7E-1
~~~
{: .source}

> ## Exercise
> Do a similar thing -- modify the stage 2 g4 fcl configuration to change the drift field from 486.7 V/cm to 500 V/cm. Hint -- you will find the drift field in an array of fields which also has the fields between wire planes listed.
{: .challenge}


### Types of Plug-Ins

Plug-ins each have their own .so library which gets dynamically loaded by art when referenced by name in the fcl configuration.

**Producer Modules**  
A producer module is a software component that writes data products to the event memory. It is characterized by produces<> and consumes<> statements in the class constructor, and `art::Event::put()` calls in the `produces()` method. A producer must produce the data product collection it says it produces, even if it is empty, or *art* will throw an exception at runtime. `art::Event::put()` transfers ownership of memory (use std::move so as not to copy the data) from the module to the *art* event memory. Data in the *art* event memory will be written to the output file unless output commands in the fcl file tell art not to do that. Documentation on output commands can be found in the LArSoft wiki [here][larsoft-rerun-part-job]. Producer modules have methods that are called on begin job, begin run, begin subrun, and on each event, as well as at the end of processing, so you can initialize counters or histograms, and finish up summaries at the end. Source code must be in files of the form: `modulename_module.cc`, where `modulename` does not have any underscores in it.

**Analyzer Modules**  
Analyzer modules read data products from the event memory and produce histograms or TTrees, or other output. They are typically scheduled after the producer modules have been run. Producer modules have methods that are called on begin job, begin run, begin subrun, and on each event, as well as at the end of processing, so you can initialize counters or histograms, and finish up summaries at the end. Source code must be in files of the form: `modulename_module.cc`, where `modulename` does not have any underscores in it.

**Source Modules**  
Source modules read data from input files and reformat it as need be, in order to put the data in *art* event data store. Most jobs use the art-provided RootInput source module which reads in art-formatted ROOT files. RootInput interacts well with the rest of the framework in that it provides lazy reading of TTree branches.  When using the RootInput source, data are not actually fetched from the file into memory when the source executes, but only when GetHandle or GetValidHandle or other product get methods are called. This is useful for *art* jobs that only read a subset of the TBranches in an input file. Code for sources must be in files of the form: `modulename_source.cc`, where `modulename` does not have any underscores in it.
Monte Carlo generator jobs use the input source called EmptyEvent.

**Services**  
These are singleton classes that are globally visible within an *art* job. They can be FHiCL configured like modules, and they can schedule methods to be called on begin job, begin run, begin event, etc. They are meant to help supply configuration parameters like the drift velocity, or more complicated things like geometry functions, to modules that need them. Please do not use services as a back door for storing event data outside of the *art* event store. Source code must be in files of the form: `servicename_service.cc`, where servicename does not have any underscores in it.

**Tools**  
Tools are FHiCL-configurable software components that are not singletons, like services. They are meant to be swappable by FHiCL parameters which tell art which .so libraries to load up, configure, and call from user code. See the [Art Wiki Page][art-wiki-redmine] for more information on tools and other plug-ins.

You can use cetskelgen to make empty skeletons of *art* plug-ins. See the art wiki for documentation, or use

~~~
cetskelgen --help
~~~
{: .language-bash}

for instructions on how to invoke it.

### Ordering of Plug-in Execution

The constructors for each plug-in are called at job-start time, after the shared object libraries are loaded by the image activater after their names have been discovered from the fcl configuration.  Producer, analyzer and service plug-ins have BeginJob, BeginRun, BeginSubRun, EndSubRun, EndRun, EndJob methods where they can do things like book histograms, write out summary information, or clean up memory.

When processing data, the input source always gets executed first, and it defines the run, subrun and event number of the trigger record being processed.
The producers and filters in trigger_paths then get executed for each event.  The analyzers and filters in end_paths then get executed.  Analyzers cannot be added to trigger_paths, and producers cannot be added to end_paths.  This ordering ensures that data products are all produced by the time they are needed to be analyzed.  But it also forces high memory usage for the same reason.

Services and tools are visible to other plug-ins at any stage of processing.  They are loaded dynamically from names in the fcl configurations, so a common error is to use in code a service that hasn't been mentioned in the job configuration.  You will get an error asking you to configure the service, even if it is just an empty configuration with the service name and no parameters set.



### Non-Plug-In Code

You are welcome to write standard C++ code -- classes and C-style functions are no problem. In fact, to enhance the portability of code, the *art* team encourages the separation of algorithm code into non-framework-specific source files, and to call these functions or class methods from the *art* plug-ins. Typically, source files for standalone algorithm code have the extension .cxx while art plug-ins have .cc extensions. Most directories have a CMakeLists.txt file which has instructions for building the plug-ins, each of which is built into a .so library, and all other code gets built and put in a separate .so library.

### Retrieving Data Products

In a producer or analyzer module, data products can be retrieved from the art event store with `getHandle()` or `getValidHandle()` calls, or more rarely `getManyByType` or other calls. The arguments to these calls specify the module label and the instance of the data product. A typical `TBranch` name in the Events tree in an *art*ROOT file is

~~~
simb::MCParticles_largeant__G4Stage1.
~~~
{: .source}

here, `simb::MCParticle` is the name of the class that defines the data product. The "s" after the data product name is added by *art* -- you have no choice in this even if the plural of your noun ought not to just add an "s". The underscore separates the data product name from the module name, "largeant". Another underscore separates the module name and the instance name, which in this example is the empty string -- there are two underscores together there. The last string is the process name and usually is not needed to be specified in data product retrieval. You can find the `TBranch` names by browsing an artroot file with `ROOT` and using a `TBrowser`, or by using `product_sizes_dumper -f 0`.

### *Art* documentation

There is a mailing list -- `art-users@fnal.gov` where users can ask questions and get help.

There is a workbook for art available at [https://art.fnal.gov/art-workbook/][art-workbook] Look for the "versions" link in the menu on the left for the actual document. It is a few years old and is missing some pieces like how to write a producer module, but it does answer some questions. I recommend keeping a copy of it on your computer and using it to search for answers.

There was an [art/LArSoft course in 2015][art-LArSoft-2015]. While it, too is a few years old, the examples are quite good and it serves as a useful reference.

## Gallery

Gallery is a lightweight tool that lets users read art-formatted root files and make plots without having to write and build art modules. It works well with interpreted and compiled ROOT macros, and is thus ideally suited for data exploration and fast turnaround of making plots. It lacks the ability to use art services, however, though some LArSoft services have been split into services and service providers. The service provider code is intended to be able to run outside of the art framework and linked into separate programs.

Gallery also lacks the ability to write data products to an output file. You are of course free to open and write files of your own devising in your gallery programs. There are example gallery ROOT scripts in duneexamples/duneexamples/GalleryScripts. They are only in the git repository but do not get installed in the UPS product.

More documentation: [https://art.fnal.gov/gallery/][art-more-documentation]

## LArSoft

### Introductory Documentation

LArSoft's home page: [larsoft.org](https://larsoft.org)
  
The LArSoft wiki is here: [larsoft-wiki](https://larsoft.github.io/LArSoftWiki/).

### Software structure

The LArSoft toolkit is a set of software components that simulate and reconstruct LArTPC data, and also it provides tools for accessing raw data from the experiments. LArSoft contains an interface to GEANT4 (art does not list GEANT4 as a dependency) and the GENIE generator. It contains geometry tools that are adapted for wire-based LArTPC detectors.

LArSoft provides a collection of shared simulation, reconstruction, and analysis tools, with art interfaces.  Often, a useful algorithm will be developed by an experimental collaboration, and desire to share it with other LArTPC collaborations, which is how much of the software in LArSoft came to be.  Interfaces and services have to be standardized for shared use.  Things like the detector geometry and the dead channel list, for example, are detector-specific, but shared simulation and reconstruction algorithms need to be able to access information from these services, which are not defined until an experiment's software stack is set up and the lar program is invoked.  LArSoft therefore uses plug-ins and class inheritance extensively to deal with these situations.

A recent graph of the UPS products in a full stack starting with dunesw is available [here](https://wiki.dunescience.org/w/img_auth.php/0/07/Dunesw_v10_00_04d00_e26-prof_graph.pdf) (dunesw). You can see the LArSoft pieces under dunesw, as well as GEANT4, GENIE, ROOT, and a few others.

### LArSoft Data Products

A very good introduction to data products such as raw digits, calibrated waveforms, hits and tracks, that are created and used by LArSoft modules and usable by analyzers was given by Tingjun Yang at the [2019 ProtoDUNE analysis workshop](https://indico.fnal.gov/event/19133/contributions/50492/attachments/31462/38611/dataproducts.pdf) (larsoft-data-products).

There are a number of data product dumper fcl files. A non-exhaustive list of useful examples is given below:

~~~
 dump_mctruth.fcl
 dump_mcparticles.fcl
 dump_simenergydeposits.fcl
 dump_simchannels.fcl
 dump_simphotons.fcl
 dump_rawdigits.fcl
 dump_wires.fcl
 dump_hits.fcl
 dump_clusters.fcl
 dump_tracks.fcl
 dump_pfparticles.fcl
 eventdump.fcl
 dump_lartpcdetector_channelmap.fcl
 dump_lartpcdetector_geometry.fcl
~~~
{: .language-bash}

Some of these may require some configuration of input module labels so they can find the data products of interest.

Some of these may require some configuration of input module labels so they can find the data products of interest. Try one of these yourself:

~~~ 
lar -n 1 -c dump_mctruth.fcl $SAMPLE_FILE
~~~
{: .language-bash}

This command will make a file called `DumpMCTruth.log` which you can open in a text editor. Reminder: `MCTruth` are particles made by the generator(s), and MCParticles are those made by GEANT4, except for those owned by the `MCTruth` data products. Due to the showering nature of LArTPCs, there are usually many more MCParticles than MCTruths.

## Examples and current workflows

The page with instructions on how to find and look at ProtoDUNE data has links to standard fcl configurations for simulating and reconstructing ProtoDUNE data: [https://wiki.dunescience.org/wiki/Look_at_ProtoDUNE_SP_data][look-at-protodune].

Try it yourself! The workflow for ProtoDUNE-SP MC is given in the [Simulation Task Force web page](https://wiki.dunescience.org/wiki/ProtoDUNE-SP_Simulation_Task_Force).


### Running on a dunegpvm machine at Fermilab

Warning - this takes time and has high peak memory use.

~~~
 export USER=`whoami`
 mkdir -p /exp/dune/data/users/$USER/tutorialtest
 cd /exp/dune/data/users/$USER/tutorialtest
 source /cvmfs/dune.opensciencegrid.org/products/dune/setup_dune.sh

 export DUNELAR_VERSION=v10_07_00d00
 export DUNELAR_QUALIFIER=e26:prof
 setup dunesw $DUNELAR_VERSION -q $DUNELAR_QUALIFIER

 TMPDIR=/tmp 
 lar -n 1 -c mcc12_gen_protoDune_beam_cosmics_p1GeV.fcl -o gen.root
 lar -n 1 -c protoDUNE_refactored_g4_stage1.fcl gen.root -o g4_stage1.root
 lar -n 1 -c protoDUNE_refactored_g4_stage2_sce_datadriven.fcl g4_stage1.root -o g4_stage2.root
 lar -n 1 -c protoDUNE_refactored_detsim_stage1.fcl g4_stage2.root -o detsim_stage1.root
 lar -n 1 -c protoDUNE_refactored_detsim_stage2.fcl detsim_stage1.root -o detsim_stage2.root
 lar -n 1 -c protoDUNE_refactored_reco_35ms_sce_datadriven_stage1.fcl detsim_stage2.root -o reco_stage1.root
 lar -c eventdump.fcl reco_stage1.root >& eventdump_output.txt
 config_dumper -P reco_stage1.root >& config_output.txt
 product_sizes_dumper -f 0 reco_stage1.root >& productsizes.txt
~~~
{: .language-bash}

Note added November 22, 2023:  The construct "TMPDIR=/tmp lar ..." defines the environment variable TMPDIR only for the duration of the subsequent command on the line.  This is needed for the tutorial example because the mcc12 gen stage copies a 2.9 GB file (see below -- it's the one we had to copy over to CERN) to /var/tmp using ifdh's default temporary location.  But the dunegpvm machines as of November 2023 seem to rarely have 2.9 GB of space in /var/tmp and you get a "no space left on device" error.  The newer prod4 versions of the fcls point to a newer version of the beam particle generator that can stream this file using XRootD instead of copying it with ifdh.  But the streaming flag is turned off by default in the prod4 fcl for the version of dunesw used in this tutorial, and so this is the minimal solution.  Note for the next iteration:  the Prod4 fcls are here: https://wiki.dunescience.org/wiki/ProtoDUNE-SP_Production_IV

### Run the event display on your new Monte Carlo event
~~~
 lar -c evd_protoDUNE_data.fcl reco_stage1.root
~~~
{: .language-bash}
and push the "Reconstructed" radio button at the bottom of the display.  

### Display decoded raw digits

To look at some raw digits in the event display, you need to decode a DAQ file or find one that's already been decoded.  The decoder fcl for ProtoDUNE-HD data taken in 2024 is run_pdhd_wibeth3_tpc_decoder.fcl.  An event display of an example decoded file is
~~~
 lar -c evd_protoDUNE_data.fcl /exp/dune/data/users/trj/nov2024tutorial/np04hd_raw_run028707_0075_dataflow5_datawriter_0_20240815T154544_decode.root
~~~
{: ..language-bash}

which is a file taken in August 2024.

### Running on HDF5 raw data

One has to load (on the same line) a special library to stream HDF5 formatted data from vd-protodune and hd-protodune.

'LD_PRELOAD=$XROOTD_LIB/libXrdPosixPreload.so ' has to be on the same line as your 'lar' command. 

in your apptainer:

~~~
export DATA=root://ccxrootdegee.in2p3.fr:1094/pnfs/in2p3.fr/data/dune/disk/hd-protodune/d1/a6/np04hd_raw_run029147_0032_dataflow4_datawriter_0_20240912T110618.hdf5
LD_PRELOAD=$XROOTD_LIB/libXrdPosixPreload.so lar -c standard_reco_protodunehd_keepup.fcl $DATA -n 1
~~~
{: ..language-bash}

### Running at CERN

This example puts all files in a subdirectory of your home directory. There is an input file for the ProtoDUNE-SP beamline simulation that is copied over and you need to point the generation job at it. The above sequence of commands will work at CERN if you have a Fermilab grid proxy, but not everyone signed up for the tutorial can get one of these yet, so we copied the necessary file over and adjusted a fcl file to point at it. It also runs faster with the local copy of the input file than the above workflow which copies it.

The apptainer command is slightly different as the mounts are different. Here we assume you are logged into an lxplus node running Alma9. 

>#### Note
> CERN Apptainer variant
{: .callout}

~~~
/cvmfs/oasis.opensciencegrid.org/mis/apptainer/current/bin/apptainer shell --she
ll=/bin/bash \
-B /cvmfs,/afs,/opt,/run/user,/etc/hostname,/etc/krb5.conf --ipc --pid \
/cvmfs/singularity.opensciencegrid.org/fermilab/fnal-dev-sl7:latest
~~~
{: .language-bash}

Make a fcl file:

~~~
#include "mcc12_gen_protoDune_beam_cosmics_p1GeV.fcl"
physics.producers.generator.FileName: "/afs/cern.ch/work/t/tjunk/public/may2023tutorialfiles/H4_v34b_1GeV_-27.7_10M_1.root"
~~~
{: .source}

~~~ 
 cd ~
 mkdir 2024Tutorial
 cd 2024Tutorial
 source /cvmfs/dune.opensciencegrid.org/products/dune/setup_dune.sh

 export DUNELAR_VERSION=v10_00_04
 export LARSOFT_VERSION=${DUNELAR_VERSION}
 export DUNELAR_QUALIFIER=e26:prof
 setup dunesw $DUNELAR_VERSION -q $DUNELAR_QUALIFIER

 #cat > tmpgen.fcl << EOF
 ##include "mcc12_gen_protoDune_beam_cosmics_p1GeV.fcl"
 #physics.producers.generator.FileName: "/afs/cern.ch/work/t/tjunk/public/may2023tutorialfiles/H4_v34b_1GeV_-27.7_10M_1.root"
 #EOF
 lar -n 1 -c tmpgen.fcl -o gen.root
 lar -n 1 -c protoDUNE_refactored_g4_stage1.fcl gen.root -o g4_stage1.root
 lar -n 1 -c protoDUNE_refactored_g4_stage2_sce_datadriven.fcl g4_stage1.root -o g4_stage2.root
 lar -n 1 -c protoDUNE_refactored_detsim_stage1.fcl g4_stage2.root -o detsim_stage1.root
 lar -n 1 -c protoDUNE_refactored_detsim_stage2.fcl detsim_stage1.root -o detsim_stage2.root
 lar -n 1 -c protoDUNE_refactored_reco_35ms_sce_datadriven_stage1.fcl detsim_stage2.root -o reco_stage1.root
 lar -c eventdump.fcl reco_stage1.root >& eventdump_output.txt
 config_dumper -P reco_stage1.root >& config_output.txt
 product_sizes_dumper -f 0 reco_stage1.root >& productsizes.txt
 ~~~ 
 {: .language-bash}

You can also browse the root files with a TBrowser or run other dumper fcl files on them. The dump example commands above redirect their outputs to text files which you can edit with a text editor or run grep on to look for things.

You can run the event display with

~~~ 
lar -c evd_protoDUNE.fcl reco_stage1.root
~~~
{: .language-bash}

but it will run very slowly over a tunneled X connection. A VNC session will be much faster. Tips: select the "Reconstructed" radio button at the bottom and click on "Unzoom Interest" on the left to see the reconstructed objects in the three views.


## DUNE software documentation and how-to's

The following legacy wiki page provides information on how to check out, build, and contribute to dune-specific larsoft plug-in code.

[https://cdcvs.fnal.gov/redmine/projects/dunetpc/wiki][dunetpc-wiki]

The follow-up part of this tutorial gives hands-on exercises for doing these things.

### Contributing to LArSoft

The LArSoft git repositories are hosted on GitHub and use a pull-request model. LArSoft's github link is [https://github.com/larsoft][github-link].  DUNE repositories, such as the dunesw stack, protoduneana and garsoft are also on GitHub but at the moment (not for long however), allow users to push code. 

To work with pull requests, see the documentation at this link: [https://larsoft.github.io/LArSoftWiki/Developing_With_LArSoft][developing-with-larsoft]

There are bi-weekly LArSoft coordination meetings [https://indico.fnal.gov/category/405/][larsoft-meetings] at which stakeholders, managers, and users discuss upcoming releases, plans, and new features to be added to LArSoft.

## Useful tip: check out an inspection copy of larsoft  <a name="inspection_copy"></a>

A good old-fashioned `grep -r` or a find command can be effective if you are looking for an example of how to call something but I do not know where such an example might live. The copies of LArSoft source in CVMFS lack the CMakeLists.txt files and if that's what you're looking for to find examples, it's good to have a copy checked out. Here's a script that checks out all the LArSoft source and DUNE LArSoft code but does not compile it. Warning: it deletes a directory called "inspect" in your app area. Make sure `/exp/dune/app/users/<yourusername>` exists first:


> ## Note
> Remember the Apptainer!  You can use your dunesl7 alias defined at the top of this page.
{: .callout}

~~~
 #!/bin/bash
 USERNAME=`whoami`
 source /cvmfs/dune.opensciencegrid.org/products/dune/setup_dune.sh
 cd /exp/dune/app/users/${USERNAME}
 rm -rf inspect
 mkdir inspect
 cd inspect
 mrb newDev
 source /exp/dune/app/users/${USERNAME}/inspect/localProducts*/setup
 cd srcs
 mrb g larsoft_suite
 mrb g larsoftobj_suite
 mrb g larutils
 mrb g larbatch
 mrb g dune_suite
 mrb g -d dune_raw_data dune-raw-data
~~~
{: .language-bash}

Putting it to use: A very common workflow in developing software is to look for an example of how to do something similar to what you want to do. Let's say you want to find some examples of how to use `FindManyP` -- it's an *art* method for retrieving associations between data products, and the art documentation isn't as good as the examples for learning how to use it. You can use a recursive grep through your checked-out version, or you can even look through the installed source in CVMFS. This example looks through the duneprototype product's source files for `FindManyP`:

~~~ 
 cd $DUNEPROTOTYPES_DIR/source/duneprototypes
 grep -r -i findmanyp *
~~~
{: .language-bash}
 
It is good to use the `-i` option to grep which tells it to ignore the difference between uppercase and lowercase string matches, in case you misremembered the case of what you are looking for. The list of matches is quite long -- you may want to pipe the output of that grep into another grep

~~~ 
 grep -r -i findmanyp * | grep recob::Hit
~~~
{: .language-bash}
 
The checked-out versions of the software have the advantage of providing some files that don't get installed in CVMFS, notably CMakeLists.txt files and the UPS product_deps files, which you may want to examine when looking for examples of how to do things.

## GArSoft

GArSoft is another art-based software package, designed to simulate the ND-GAr near detector. Many components were copied from LArSoft and modified for the pixel-based TPC with an ECAL. You can find installed versions in CVMFS with the following command:

~~~
ups list -aK+ garsoft
~~~
{: .language-bash}

and you can check out the source and build it by following the instructions on the [GArSoft wiki](https://cdcvs.fnal.gov/redmine/projects/garsoft/wiki).


<!-- 
## Quiz

> ## Question 01
>
> Enter Question here
> <ol type="A">
> <li>.</li>
> <li>.</li>
> <li>.</li>
> <li>.</li>
> <li>None of the Above</li>
> </ol>
>
> > ## Answer
> > The correct answer is .
> > {: .output}
> > Comment here 
> {: .solution}
{: .challenge} 

-->


{%include links.md%} 

[about-qualifiers]: https://cdcvs.fnal.gov/redmine/projects/cet-is-public/wiki/AboutQualifiers
[art-wiki]: https://cdcvs.fnal.gov/redmine/projects/art/wiki
[larsoft-rerun-part-job]: https://larsoft.github.io/LArSoftWiki/Rerun_part_of_all_a_job_on_an_output_file_of_that_job
[github-link]: https://github.com/larsoft
[protodune-sim-task-force]: https://wiki.dunescience.org/wiki/ProtoDUNE-SP_Simulalation_Task_Force
[larsoft-meetings]: https://indico.fnal.gov/category/405/][larsoft-meetings
[developing-with-larsoft]: https://larsoft.github.io/LArSoftWiki/Developing_With_LArSoft
[fhicl-described]: https://cdcvs.fnal.gov/redmine/documents/327
[garsoft-wiki]: https://cdcvs.fnal.gov/redmine/projects/garsoft/wiki
[art-wiki-redmine]: https://cdcvs.fnal.gov/redmine/projects/art/wiki#How-to-use-the-modularity-of-art
[art-more-documentation]: https://art.fnal.gov/gallery/][art-more-documentation
[using-larsoft]: https://cdcvs.fnal.gov/redmine/projects/larsoft/wiki/Using_LArSoft
[larsoft-data-products]: https://indico.fnal.gov/event/19133/contributions/50492/attachments/31462/38611/dataproducts.pdf
[dunetpc-wiki]: https://cdcvs.fnal.gov/redmine/projects/dunetpc/wiki
[look-at-protodune]: https://wiki.dunescience.org/wiki/Look_at_ProtoDUNE_SP_data
[art-LArSoft-2015]: https://indico.fnal.gov/event/9928/timetable/?view=standard
[art-workbook]: https://art.fnal.gov/art-workbook/
