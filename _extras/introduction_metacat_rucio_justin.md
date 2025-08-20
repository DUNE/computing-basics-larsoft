---
title:  Submit grid jobs with JustIn
teaching: 20
exercises: 0
questions:
- How to submit realistic grid jobs with JustIn
objectives:  
- Demonstrate use of JustIn for job submission with more complicated setups.
keypoints:
- Always, always, always prestage input datasets. No exceptions.
---

#  PLEASE USE THE NEW JUSTIN SYSTEM INSTEAD OF POMS

__The JustIn Tutorial is currently in docdb at: [JustIn Tutorial](https://docs.dunescience.org/cgi-bin/sso/RetrieveFile?docid=30145)__

The JustIn system is described in detail at: 

__[JustIn Home](https://dunejustin.fnal.gov/dashboard/)__

__[JustIn Docs](https://dunejustin.fnal.gov/docs/)__


> ## Note More documentation coming soon
{: .callout}

<!--




Aaron Higuera
Elisabetta Pennacchio



The Data Management subsystem brings data from the detectors to the archival storage facility (tapes) and then distributes data to the storage elements around the world. 
SAM: 
.designed for D0 and CDF

.Object cataloged: files and collection of files called SAM dataset. 

.Data files are not stored in SAM but metadata are (metadata allows user to identify a file and locate data file)



SAMhas served  DUNE until now, a replacement is needed to have data movement capacity for a distributed worldwide storage system
DUNEis now transitioning to new systems (simplified picture)

1.Data catalog management MetaCathttps://metacat.readthedocs.io/en/latest/



stores file characteristics but not location 
2.  Location management Ruciohttps://rucio.github.io/

stores physical location of files 





General introduction to Rucio, MetaCatand justIN


This tutorial is divided into 3 main parts:
Page 5:  introduction to Rucio
Page 13: introduction to MetaCat
Page 16: introduction to justIN
Page 42: backup jobscriptchecklist,
how to setup environment for MetaCat, Rucio, justIN
useful links
Pre-requisite:
DUNE Computing training: https://hschellman.github.io/computing
-
basics/





Introductory remark: moving to Alma9





1)setup Rucioenvironment (on dunegpvm)
need to be DUNE member and have a FERMILAB valid account
2) Datasets are listed here: 
https://wiki.dunescience.org/wiki/Data_Collections_Manager/data_sets






An example: dataset from latest reco2(tracks and shower)  reconstruction campaign, nu sample, FD2-VD 

To list dataset content you can:
1)run the ruciocommand: 
ruciolist-files fardet-vd:fardet-vd__fd_mc_2023a_reco2__full-reconstructed__v09_81_00d02__reco2_dunevd10kt_nu_1x8x6_3view_30deg_geov3__prodgenie_nu_dunevd10kt_1x8x6_3view_30deg__out1__v1_official
2) follow the arrow on the right





to accessMetaCatweb interface 






use service accountto logon 







To getfile location for  
fardet:vd:nu_dunevd10kt_1x8x6_3view_30deg_1002_134_20230809T200445Z_gen_g4_detsim_hitreco__20240220T211721Z_reco2.root
run:

rucio-a $USER list-file-replicas fardet-vd:nu_dunevd10kt_1x8x6_3view_30deg_1002_134_20230809T200445Z_gen_g4_detsim_hitreco__20240220T211721Z_reco2.root --pfns


root://dune.dcache.nikhef.nl:1094/pnfs/nikhef.nl/data/dune/generic/rucio/fardet-vd/a1/11/nu_dunevd10kt_1x8x6_3view_30deg_1002_134_20230809T200445Z_gen_g4_detsim_hitreco__20240220T211721Z_reco2.root
root://fndca1.fnal.gov:1094/pnfs/fnal.gov/usr/dune/tape_backed/dunepro//fardet-vd/full-reconstructed/2024/mc/out1/fd_mc_2023a_reco2/00/00/10/02/nu_dunevd10kt_1x8x6_3view_30deg_1002_134_20230809T200445Z_gen_g4_detsim_hitreco__20240220T211721Z_reco2.root_1709165820
root://fndca1.fnal.gov:1094/pnfs/fnal.gov/usr/dune/persistent/staging/fardet-vd/a1/11/nu_dunevd10kt_1x8x6_3view_30deg_1002_134_20230809T200445Z_gen_g4_detsim_hitreco__20240220T211721Z_reco2.root
.3 different locations are shown: how to choose?


tape_backed:  it is the copy from tape, may require to be staged. It is preferable to use one of the other locations
In the example, two other locations are shown: one in Europeand one in US. Select the one nearest to your location 
(page 40: list of storage elements)

Results:


Once the file located, you can run lar: 
lar-c anatree_dunevd10kt_1x8x6_3view_30deg_geov3.fcl root://dune.dcache.nikhef.nl:1094/pnfs/nikhef.nl/data/dune/generic/rucio/fardet-vd/a1/11/nu_dunevd10kt_1x8x6_3view_30deg_1002_134_20230809T200445Z_gen_g4_detsim_hitreco__20240220T211721Z_reco2.root

Remember: 
to stream a hdf5 file , the variable LD_PRELOAD has to be defined
LD_PRELOAD=$XROOTD_LIB/libXrdPosixPreload.so  


July 2024 
How to find ProtoDUNE files at CERN 
https://github.com/DUNE/data
-
mgmt
-
ops/wiki/Using
-
Rucio
-
to
-
find
-
Protodune
-
files
-
at
-
CERN





How to setup MetaCatenvironment 


Examples of MetaCatqueries

.To list files from a specific run, in the example run  21445 (VD coldbox) 

.Apptainer> metacatquery   "files where 21445 in core.runsand core.data_tier'


.To list first 100 files:


Apptainer>metacatquery  "files where 21445 in core.runsand core.data_tier=raw limit 100'
To check how many files belong to run 21445:
Apptainer> metacatquery  -s "files where 21445 in core.runs"
Files:        304
Total size:   1296713825568 (1.297 TB)


List files from a given justINworkflow (see next section):
metacatquery "files where dune.workflow['workflow_id'] in (1630)'
A given file can also be searched and visualized from the WEB interface




-->


## justIN

- is the new workflow system replacing POMS
- It can be used to process several input  files by submitting batch jobs  on the grid 
- justIN is a workflow system that processes data by satisfying  the requirements of data location/data catalog, rapid code distribution service
and job submission to the grid.

justIN ties together:

1. MetaCat search queries that obtain lists of files to process

2. Rucio knowledge of where replicas of files are

3. a table of site-to-storage __distances__ to make best choices about where to run each type of job





## To process data using justIN: 

You need to provide a jobscript(shell script) with some basic tasks:

- Setup  software environment

- Use rucio to ind where the data is

- Process the data

- Save the output in a defined location




## 


~~~
justin simple-workflow <args...>
~~~
{: .language-bash}

once you run the command, you get the `workflow ID`. 

In case of any problem, you can stop your workflow by running 
~~~
finish-workflow --workflow-id <ID> 
~~~
{: .language-bash}


# Next topics: 


1. Understand how a jobscriptis structured

2. Process data using standard code

3. Process data using customized fclfiles and/or customized code

4. Select the input dataset

5. Specify where your output should go (jobs writing to scratch)


Examples of jobscripts are provided in the [GitHub
production repository](https://github.com/DUNE/dune-prod-utils/blob/main/justIN-examples).

A jobscripts checklist is available in the backup  


Two general remarks: 

> ## Note ALWAYS test code and jobscriptbefore sending jobs to the grid
{: .callout}

> ## For  any large processing  (MC or DATA) producing  large output that has to be shared within the Collaboration, please contact the production group. 
{: .callout}








## Things you can do

- Process data (submit a job to the grid) if you are using code from the base release and you don't actually modify any of it

- Once you have identified what data you want to process, you can see the most recent data (official data) sets available at:  


https://wiki.dunescience.org/wiki/Data_Collections_Manager/data_sets

### Example: Let's say you want to run mergeanafor electron neutrinos, 

#### First: Where is the data?

- In DUNE we provided datasets to easily identify a collection of files 

for example:

`fardet-hd:fardet-hd__fd_mc_2023a_reco2__full-reconstructed__v09_81_00d02__standard_reco2_dune10kt_nu_1x2x6__prodgenie_nue_dune10kt_1x2x6__out1__validation`

Dataset names tend to be self explanatory and includes the type of detector, which fcl files were used to produce it, the software version, data tier, and a tag, in this case, the tag is `validation`.


- Lets try to process mergeanain the first 100 files that in the data sets,

- MetaCat relies on Metacat Query Language (MQL) queries to select a collection of files. In this case to select the first 100 files of a given data set. The query would be something like:


~~~
"files from fardet-hd:fardet-hd__fd_mc_2023a_reco2__full-reconstructed__v09_81_00d02__standard_reco2_dune10kt_nu_1x2x6__prodgenie_nue_dune10kt_1x2x6__out1__validation ordered limit 100 "
~~~
{: .language-bash} 

- The flag 'ordered' is crucial to ensure reproducibility 


### example jobscript

https://github.com/DUNE/dune-prod-utils/blob/main/justIN-examples/submit_ana.jobscript

~~~
# fcl file and DUNE software version/qualifier to be used
FCL_FILE=${FCL_FILE:-standard_ana_dune10kt_1x2x6.fcl}
DUNE_VERSION=${DUNE_VERSION:-v09_81_00d02}
DUNE_QUALIFIER=${DUNE_QUALIFIER:-e26:prof}
~~~
{: .language-bash}

 a bit further down 

~~~
# Setup DUNE environment
source /cvmfs/dune.opensciencegrid.org/products/dune/setup_dune.sh
setup dunesw "$DUNE_VERSION" -q "$DUNE_QUALIFIER"
~~~
{: .language-bash}

and here is how you do the actual processing: 

~~~
# Here is where the LArSoft command is called 
(
# Do the scary preload stuff in a subshell!
export LD_PRELOAD=${XROOTD_LIB}/libXrdPosixPreload.so
echo "$LD_PRELOAD"

lar -c $FCL_FILE $events_option -o $outFile "$pfn" > ${fname}_ana_${now}.log 2>&1
)
~~~
{: .language-bash}

The scary preload is to allow `xroot` to read `hdf5` files. 



'Process data (submit a job to the grid) if you are just using code from the base release and you don't actually modify any of it 


$ USERF=$USER
$ FNALURL='https://fndcadoor.fnal.gov:2880/dune/scratch/users'
$ justinsimple-workflow --mql"files from fardet-hd:fardet-hd__fd_mc_2023a_reco2__full-reconstructed__v09_81_00d02__standard_reco2_dune10kt_nu_1x2x6__prodgenie_nu_dune10kt_1x2x6__out1__validation skip 5 limit 5 ordered " --jobscriptsubmit_ana.jobscript--rss-mb 4000 --output-pattern '*_ana_*.root:$FNALURL/$USERF"
'You can look at your job status by using justIN dashboard https://dunejustin.fnal.gov/dashboard/?method=list-workflows





### Custom fcl file

- Process data (submit a job to the grid) if you are using code from the base release and you want to use a customized FCL file

- To do that, the best is to use the Rapid Code Distribution Service (RCDS) via cvmfs as explained in the tutorial 

- Let's say you have a customized FCL file that you need to run over some datasets. As per instruction from the DUNE justINtutorial you need to tar the files needed and put them in cvmfs.


~~~
$ tar cvzmy_fcls.tar my_fcls
$ source /cvmfs/dune.opensciencegrid.org/products/dune/setup_dune.sh
$ setup justin
$ rm -f /tmp/x509up_u`id -u`
$ kx509
$INPUT_TAR_DIR_LOCAL=`justin-cvmfs-upload my_fcls.tar`
~~~
{: .language-bash}

Wait a few minutes to check the files 

~~~
$ ls -l $INPUT_TAR_DIR_LOCAL
~~~
{: .language-bash}


You can look at the example at https://github.com/DUNE/dune-
prod-utils/blob/main/justIN-examples/submit_local_fcl.jobscript


'The key part of the code is the following 


~~~
justin simple-workflow --mql "files from fardet-hd:fardet-hd__fd_mc_2023a_reco2__full-reconstructed__v09_81_00d02__standard_reco2_dune10kt_nu_1x2x6__prodgenie_nu_dune10kt_1x2x6__out1__validation skip 5 limit 5 ordered ' --jobscript submit_local_fcl.jobscript --rss-mb 4000 --env INPUT_TAR_DIR_LOCAL="$INPUT_TAR_DIR_LOCAL"
~~~
{: .language-bash} 

Things you can do

Image

'Process data (submit a job to the grid) if you are NOT using code from the base release and you want to use customized code

'Probably you are developing some reconstruction algand you want to check the results in a large sample, before committing your software to GitHub 

'You can use your customized software (e.g. local installation of dunereco) and use justINto process the data with your new LArSoftmodule 

'Similar to the previous part, you will need to provide all pieces in a tar file and put them in cvmfs



$ tar cvz my_code.tar my_code
'Here my_code.tar includes a directory with my_fcls files and one with my local products (e.g. local Products_larsoft_v09_85_00_e26_prof) this is similar to what you used to do when using jobsub and using customized code/ 




Things you can do








how to 'navigate' in justINdashboard.
Example: you want to check outputs/logs for jobs from workflow 1850









To access full statistics:
-sites where jobs ran
-storage used for input/output



To access details of each job (see next page)













To access log files






For each file, you see where it was processed and which RucioStorage Element it came from.








How it looks like if there are failed jobs







To list  storage elements (where data can be) 












backup






How to setup MetaCat, Rucioand justIN(on dunegpvm)
first run:
/cvmfs/oasis.opensciencegrid.org/mis/apptainer/current/bin/apptainershell --shell=/bin/bash -B /cvmfs,/exp,/nashome,/pnfs/dune,/opt,/run/user,/etc/hostname,/etc/hosts,/etc/krb5.conf --ipc--pid/cvmfs/singularity.opensciencegrid.org/fermilab/fnal-dev-sl7:latest
Then: 
source /cvmfs/dune.opensciencegrid.org/products/dune/setup_dune.sh
setup python v3_9_15
setup rucio
kx509
export RUCIO_ACCOUNT= $USER
export METACAT_SERVER_URL=https://metacat.fnal.gov:9443/dune_meta_prod/app
export METACAT_AUTH_SERVER_URL=https://metacat.fnal.gov:8143/auth/dune
setup metacat
setup justin
justinversion
rm -f /var/tmp/justin.session.`id-u`
justintime


Links
MetacatWEB interface:    https://metacat.fnal.gov:9443/dune_meta_prod/app/auth/login

justIN: https://dunejustin.fnal.gov/docs/

Slack channels:  #workflow


