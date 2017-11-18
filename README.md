# The ICAT Manual

TODO: combine content from `about.md` and the http://icatproject.org home page


## about.md
The ICAT software takes data from large scientific facilities - such as particle accelerators - and catalogues and indexes it so scientists can find the data they need and share it with their team. A simple REST interface allows scientific instrument designers to quickly upload their data to the system for indexing and cataloging. TopCat, ICAT's easy to use web interface, allows scientists and collaborators to effortlessly search and download their data. ICAT's secure authentication system allows principal investigators to control their data and ensures users can only access data which they are allowed to access. (Mention DOIs here?)

ICAT is in use at some of the world's largest scientific facilities (list some?) enabling access to millions of (pounds or dollars) worth of scientific results. The software is free & open-source and has been developed & maintained by an international collaboration for over 10 years.

### What does ICAT do?
For a more detailed explanation of what ICAT does and the actors, entities and workflows it models, see the [ICAT Model](about/icat_model.md) page.

### Who uses ICAT?
ICAT is used at many large scientific facilities. To find out how ICAT is supporting great science, see our case studies:
- [CLF](about/clf.md)
- [HZB](about/hzb.md)
- [ISIS](about/isis.md)
- [OCTOPUS](about/octopus.md)

### Who develops ICAT?
ICAT is developed by an international collaboration. (TODO: list them/link to them). There are monthly meetings to discuss development, yearly face-to-face meetings and a steering committee which agrees the roadmap. To find out more, see our [Collaboration](about/collaboration.md) page.

## icatproject.org home page
HOME
The ICAT Project
The ICAT project provides a metadata catalogue and related components to support  Large Facility experimental data, linking all aspects of the research chain from proposal through to publication.  It includes a number of components, the principal ones being:

ICAT: the metadata catalogue itself provides  SOAP and RESTful web service interfaces to an underlying database via an easy to use API. It has powerful search features, a rule based authorization mechanism and it uses plugins for authentication.
IDS: the ICAT Data Service which provides a uniform interface to data catalogued by ICAT.   It can be used to upload and download data and supports a wide range of back-end storage strategies via a plugin mechanism
TopCAT: a web based GUI able to search across multiple ICAT instances and download data via the IDS.
IJP: the ICAT Job Portal to allow submission of jobs  to HPC resources or compute farms where data selection is based on ICAT information and the jobs make use of the IDS for data access.
