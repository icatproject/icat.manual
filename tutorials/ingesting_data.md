# Ingesting Data into ICAT

## Overview

Topics to be covered here are: basic preparation to make on ICAT before getting started. This will include how to set up Investigations and Users, methods to find data that is ready for ingestion, how to make data searchable so that users can find it, some thoughts about IDS and wether or not to use it, some ideas about making teh ingesting fast and scalebale und finally a closer look at how ISIS ingestion process works.

## Preparing the ICAT

In order to start preparing the ICAT for data ingestion you will need to set up a functional account - typically in a simple or DB authenticator and create rules to give this account create, read and update access on most entities.

You need to create entities highlightend in the diagram below. You will need a single Facility and then one or more InvestigationTypes, DatasetTypes and DatafileFormats. The more of these you create -- as long as you use them -- the more searchable your data will be.

Next you need to create Instruments which can be used for example to give InstrumentScientists access to all data for their instrument via InvestigationInstrument.

All of these entities only need a name apart from DatafileFormat which also requires a version.

You should write some code to do this initial setup of your ICAT so that you have a convenient way of repeating this process in the future.

![](/tutorials/ingesting_data/1_preparing.png)

## Investigation and Users

The next thing you will need in your ICAT before you can actually ingest any data into it will be some Investigations and preferably a list of Users who need access to the data for those investigations.

The best source of this data is a User Office system which you will almost certainly have for your facility. Whether you can actually get access to it or export the data in a sensible manner is beyond the scope of this presentation.

Here on the image below you can see a graphical view of schedules for Astra-Gemini laser and all of this data is available from the User Office system at RAL.

![](/tutorials/ingesting_data/2_investigations.png)

To create Investigations you need to give them a name -- probably just a short identifier like Astra-Gemini ones here. It will also need a visitId if the experiment is split over a number of separate visits and a title. If a short description of the experiment is availbale then use that or you can just re-use the name field. You should also add a startDate although these are not mandatory.

With the Investigation created in ICAT you should then link it to the Instruments being used by creating InvestigationInstruments.

Next create Users. The name field needs to contain a userId prefixed with the authenticator which they will use to login. FullName and email are also useful fields if you want applications like TopCat to be able to send polite emails to users when their download is ready.

Finally link User to Investigation by creating an InvestigationUser. To do this you need to assign a role such as principal or co-investigator, which can be used by the Rules system to give different privileges to different types of users.

## Discovering new data

One key thing your ingestion program will need is a way to discover when there is a new data to be ingested. At RAL we have 3 different ways of doing this. The first is a single input directory into which all files to be ingested must be placed by the acquisition software. This is the system we use for CLF as you can see in the list of files on the image below. This relies on all files having unique names and this means having some kind of logical system for naming files. These CLF files have the date, followed by the shot type and number, followed by the data channel name and the file type.

![](/tutorials/ingesting_data/3_discovering.png)

The method used by ISIS is the filesystem watcher. Basically a process listens for changes on the filesystem which is very convenient but has the downside that if you stop the process you need to have some way of going back and finding any files which were created when the process wasn't running.

The third method, used by Diamond, involves the acquisition software creating a trigger or drop file describing the data to be ingested -- where it is located and some simple metadata about it.

To work out which Investigation the data should be added to some of the options are to use the file name or path, to extract this information from the file itself or the drop file, or if the file timestamp and the User Office data are reliable and the instrument is known then you can look up the Investigation in ICAT itself.

The files themselves cannot be added directly to the Investigation but need to be added to a Dataset which needs to be created within the Investigation. A Dataset simply provides a way of logically grouping files together so typically all the files for a single shot, run or scan.

## Making data searchable

All of the three main entities, Investigation, Dataset and Datafile \(image below\) can have Parameters attached to them and theses should be used to store any useful metadata. The more effort you put into adding metadata the more searchable your data will be.

![](/tutorials/ingesting_data/4_searchable.png)

Parameters are basically name-value pairs of type NUMERIC, STRING, or DATE\__AND_\_TIME.

One important thing to note is that ICAT does not do anything to index the data inside your files, so they are not searchable unless you create DatafileParameters.

## The IDS -- ingesting with or without

Whether you ingest the Datafiles themselves via IDS is likely to depend on factors like the type of storage you are using and where it is located. Basically, if you are making use of the 2 level storage model provided by the IDS or if you are moving the data from one place to another anyway then it is probably best to ingest via the IDS.

If you do however have a large disk storage area that is accessible directly from the data acquisition, then you have the option of leaving the Datafile there and just creating a Datafile entry for it in the ICAT and add any metadata as DatafileParameter.

You will still need an IDS on top of this datastore for downloading via TopCAT etc. but the ingestion process can run much faster if you do not have to pass the files themselves around.

## Performance and scalability

There are a few things you can easily do to speed up your ingestion process and to make it scalable.

You should look to minimise the amount of time that you spend looking things up in ICAT and to do this you should cache the entities that you are using frequently – typically DatasetTypes, DatafileFormats and ParameterTypes – as these are required whenever you create a Dataset or Datafile.

Typically during ingestion you will be using the same Investigations and Datasets over and over again so if you can keep a cache of the ones you have used most recently then that helps too.

You will almost certainly want to make your ingestion program multi-threaded. You can have one thread managing the queue of files to be ingested and distributing these over a configurable number of worker threads which are doing the main ingestion work.

Another way of making the ingestion scalable is to design it around an enterprise queueing system and then run multiple instances of the ingestion program.

## ISIS ingestion -- a closer look

The ISIS ingestion system is based around an ActiveMQ server and this diagram shows the flow of data through the system.  
 The general flow is from the top left to the bottom right \(image below\).

![](/tutorials/ingesting_data/5_ISISingestion.png)

The system starts with the FileWatcher which is kept as simple as possible. It’s only job is to monitor the file system for file creation events and then put the path to that file onto the Initial ICAT Queue. From here a program called LiveMonitor works alongside writeRaw and nxIngest to extract metadata from RAW and NEXUS files into XML files which it then puts on the ICAT Queue. A program called XMLtoICAT takes these XML files and inserts the metadata into ICAT. The Datafiles themselves are not moved – they remain on a large disk storage area where the data acquisition software puts them. Finally XMLtoICAT puts the file paths onto an SDB Queue from where a copy of the files is sent to a dark archive in a product called Safety Deposit Box.

If any of the programs encounter a problem, they put the message onto an error queue – seen here in red – which causes an email alert to be raised. Once the problem has been looked into and corrected the message in the error queue is moved back to the previous queue to be re-processed.

## Conclusion

So to conclude, my top tips would be to do whatever you can to get Investigation and User information imported from a User Office system.

You should decide how searchable your data needs to be and use Parameters accordingly. The more effort you put into storing metadata during ingestion, the easier it will be for users to find data.

Your IDS setup is most likely to be determined by the size and type of filestores available to you and your budget. As you have seen, if you have a large enough disk store available, the files themselves don’t need to be ingested via the IDS.

And finally, make sure that you design performance and scalability into your ingestion program. The number of files that it processes today and the size of them is only going to increase over time.

