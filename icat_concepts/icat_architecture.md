# The ICAT Architecture

The ICAT project includes a number of components, the principal ones being ICAT, IDS \(ICAT Data Service\), TopCat, IJP \(ICAT Job Portal\) and the ICAT manager. Each components has its own GitHub repository within an 

Here is a small description of the principal components:

* **ICAT**: the metadata catalogue itself provides  SOAP and RESTful web service interfaces to an underlying Relation Database Management System \(RDMS\) via an easy to use API. It has powerful search features, a rule based authorization mechanism and it uses plugins for authentication. The icat-setup tool is provided for initial configuration of an ICAT instance for a facility and a simple editor, ice, is also available. The ICAT manager stand alone application \(described below\) provides similar functionality as ice and presents a user friendly graphical interface.

* **IDS**: an ICAT Data Service which provides a uniform interface to data catalogued by ICAT.  It exposes a RESTful web service and provides the ability to upload individual files and to download one or more files. When a file is uploaded the metadata are stored in ICAT. ICAT authorization rules for the “Datafile” table in ICAT are applied to control read/write access to the files in the IDS. If multiple files are requested they are packaged in zip format. The interface has some calls which assume two level storage \(with all data available on a possibly slow system and recently used data on another\) however it will work fine with only one level. The server makes use of a plugin mechanism so that a facility can choose its own directory structure for file storage. A two level file storage example plugin is provided as an interface to which a plugin must conform if you choose to write your own.

* **TopCAT**:  a web-based GUI able to search across multiple ICAT instances, to download and optionally upload data. It makes use of both ICAT core and the IDS.

* **IJP**: an ICAT Job Portal to allow submission of jobs where data selection is based on ICAT information.  Both batch and interactive jobs are supported. It makes use of both ICAT core and the IDS.

* **ICAT  manager**: a standalone Java client to visualize and manage ICAT instances.

If you want to install components then go to \[installation\]\([http://icatproject.org/installation](http://icatproject.org/installation%29%29 %29%29. To get an overview of what is happening across repositories, you can take a look at [waffle]%28https://waffle.io/icatproject%29 %29. To get an overview of what is happening across the repositories tah ea look at [waffle]%28https://waffle.io/icatproject%29%29%29\)\).

![](/assets/ICAT-Components.png)

