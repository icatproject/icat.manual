# The ICAT Architecture

The ICAT project includes a number of components, the principal ones being:

* **ICAT**: the metadata catalogue itself provides  SOAP and RESTful web service interfaces to an underlying Relation Database Management System \(RDMS\) via an easy to use API. It has powerful search features, a rule based authorization mechanism and it uses plugins for authentication. The icat-setup tool is provided for initial configuration of an ICAT instance for a facility and a simple editor, ice, is also available. The ICAT manager stand alone application \(described below\) provides similar functionality as ice and presents a user friendly graphical interface. 

* **IDS**: the ICAT Data Service which provides a uniform interface to data catalogued by ICAT.   It exposes a RESTful web service and provides the ability to upload individual files and to download one or more files. When a file is uploaded the metadata are stored in ICAT. ICAT authorization rules for the “Datafile” ICAT are applied to control read/write access to the files in the IDS. If multiple files are requested they are packaged in zip format. The interface has some calls which assume two level storage \(with all data available on a possibly slow system and recently used data on another\) however it will work fine with only one level. The server makes use of a plugin mechanism so that a facility can choose its own directory structure for file storage. A two level file storage example plugin is provided as is an interface to which a plugin must conform if you choose to write your own.

* **TopCAT**: a web-based GUI able to search across multiple ICAT instances and download data via the IDS. 

* **IJP**: the ICAT Job Portal to allow submission of jobs  to HPC resources or compute farms where data selection is based on ICAT information and the jobs make use of the IDS for data access.

* **ICAT  manager : **a standalone Java client to visualize and manage ICAT instances.

Each components of the ICAT project has its own GitHub repository within an \[icatproject organisation\] \([https://github.com/icatproject](https://github.com/icatproject%29%29 containing the code, a small Wiki and the Issue %28bug/ feature request\) \) List for that component. If you simply want to install components then go to \[installation\] \([http://icatproject.org/installation](http://icatproject.org/installation%29\) \).

