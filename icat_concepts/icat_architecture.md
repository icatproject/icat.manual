# The ICAT Architecture

The ICAT project includes a number of components, the principal ones being:

* **ICAT**: the metadata catalogue itself provides  SOAP and RESTful web service interfaces to an underlying Relation Database Management System \(RDMS\) via an easy to use API. It has powerful search features, a rule based authorization mechanism and it uses plugins for authentication.

* **IDS**: the ICAT Data Service which provides a uniform interface to data catalogued by ICAT.   It can be used to upload and download data and supports a wide range of back-end storage strategies via a plugin mechanism.

* **TopCAT**: a web based GUI able to search across multiple ICAT instances and download data via the IDS.

* **IJP**: the ICAT Job Portal to allow submission of jobs  to HPC resources or compute farms where data selection is based on ICAT information and the jobs make use of the IDS for data access.

* **ICAT  manager : a**a standalone Java client to visualize and manage ICAT instances.

Each components of the ICAT project has its own GitHub repository within an \[icatproject organisation\] \([https://github.com/icatproject](https://github.com/icatproject\)\) containing the code, a small Wiki and the Issue \(bug/ feature request\) List for that component. If you simply want to install components then go to \[installation\] \([http://icatproject.org/installation](http://icatproject.org/installation\)\).

