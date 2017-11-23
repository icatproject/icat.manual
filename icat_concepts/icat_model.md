---
title: todo
layout: default
---

# Core Scientific Metadata Model \(CSMD\)

The model is a study-data oriented model which has been developed at STFC over many years. It captures high level information about scientific studies and the data that they produce.  The ICAT schema finds its origins in CSMD.

The CSMD is developed to support data collected within a facility’s scientific workflow. However the model is also designed to be generic across scientific disciplines and has application beyond facilities science, particularly in the “structural sciences” \(such as chemistry, material science, earth science, and biochemistry\) which are concerned with analysing the structure of substances, and perform systematic experimental analyses on samples of those materials.

The model is organised around a notion of Studies, a study being a body of scientific work on a particular subject of investigation. During a study, a scientist would perform a number of investigations e.g. experiments, observations, measurements and simulations. Results from these investigations usually run through different stages: raw data, analysed or derived data and end results suitable for publication. The model has a hierarchical model of the structure of scientific research programmes, projects and studies, and also a generic model of the organisation of data sets into collections and files.

The core entities of the CSMD for a study are summarised as follows.

* Investigation. The fundamental unit of the study, including a title, abstract, dates, and unique identifiers referencing the particular study. Also associated with the investigation are the facility and instrument used to collect data.
* Investigator. The people involved in the study, together with their institution and role in the investigation \(e.g. principle investigator, research student\).
* Topic and Keyword. Controlled and uncontrolled vocabulary to annotate and index the investigation.
* Publication. References to publications associated with \(motivating or derived from\) the investigation.
* Sample. Information on the material sample under investigation within the study. The model has fields for a sample's name, chemical formula and any associated special information, such as specific safety information on a toxic material.
* Dataset. One or more datasets can be associated with an investigation, representing different runs or analyses on the sample. Initially a raw data set can be attached to the investigation, but subsequently, analysed datasets can also be added.
* Datafile. The CSMD takes a hierarchical view of data holdings, as data sets may contain other dataset as well as units of storage, typically datafiles. Each datafile has more detailed information, including its name, version, location, data format, creation and modification time, and fixity information such as a Checksum.
* Parameter. Parameters describe measureable quantities associated with the investigation, such as temperature, pressure, or scattering angle, describing either the parameters of the sample, the environment the data was collected in, or the parameters being measured. Parameters can be associated at different levels, such as the sample, dataset or the datafile, and have names, units, values, and allowable data ranges.
* Authorisation: the CSMD can associate conditions on investigations and data sets, so that user specified access conditions can be specified. Thus the authorisation entity can record which user in which role can access data on specific investigations.

### The Metadata Structure

The metadata within the general structure is laid in a series of classes and subclasses. We do not describe the whole model in detail for reasons of space, but rather select some areas of particular interest.

#### Modelling Scientific Activity

The data model describes scientific activities at different levels: the main unit is the Study, which optionally can lie in a context of a science research programme, governed by policies. Each study has an Investigator that describes who is undertaking the activity, and the Study Information that captures the details of this particular study. Studies include particular scientific investigations.

* Policy: the company or government policies which initiate Programmes of work.
* Programmes: related studies that have a common theme which are usually funded and resourced directly or with an intermediary organisation under the rubric of the programme.
* Studies: Studies investigate some aspect of science and have a Principal Investigator and/or institution, co-investigators and some specific purpose. e.g. an application for time on a facility such as ISIS.
* Investigations are studies or parts of studies that have links directly to data holdings, as described above. More specific types of investigations may include the following.
* Experiments: investigations into the physical behaviour of the environment usually to test a hypothesis, typically involving an instrument operating under some instrumental settings and environmental conditions, and generating data sets in files. E.g. the subjection of a material to bombardment by X-Rays of known frequency recording the resulting diffraction pattern.
* Measurements: investigations that record the state of some aspect of the environment over a sequence of points in time and space, using some passive detector, e.g. the measurement of temperature at a point on the earth surface taken hourly using a thermometer of known accuracy.
* Simulations: investigations that test a model of part of the world, and a computer simulation of the state space of that model. This will typically involve some simulation package with some initial parameters, and generate a dataset representing the result of the simulation.

  Each investigation has a particular purpose and uses a particular set up of instruments or computer systems.

  Classes within the model have several fields. For example, investigator has a name, address, status, institution and role within the study. For reasons of space we do not provide a complete description of all the available classes within the metadata model. For illustration, we consider the Study class. Within a Study, there are several fields, as in table below.

| ID | The key of the Study |
| :--- | :--- |
| NAME | Unique name given to the study. |
| PURPOSE | Description of purpose of study, an abstract of why these investigations are brought together. |
| STATUS | Ongoing or complete, as there could be additional investigations planned in the future which could be applicable to this study. |
| RELATED MATERIAL | Information related to the study. This could be related studies in other facilities, or on similar samples. |
| STUDY\_CREATION\_DATE | When the study was created. |
| STUDY\_MANAGER | The user who has created the study; this may not be the investigator, but rather a member of the facilities staff. |

Further links in the study relate to the specific investigation. An investigation has fields for the investigators involved, together with their role and their contact details, and also references to the facility and instrument used to capture the data.

#### Modelling scientific data holdings

Investigations are characterised by the generation of a particular set of data on the analysis of a sample, initially raw data, but then further data representing analysed data. Other data may also be associated with the investigation, such as calibration data. Each data set may have different parameters set. The model of data holdings used in the model needs to accommodate this complexity.

In CSMD each investigation is associated with metadata describing the data holding associated with that investigation. The metadata format given here is designed for use on general scientific data holdings, describing data logically which may be physically moved around. Thus, data holdings have three layers: the experiment, the logical data, and the physical files. Data holdings are considered as hierarchies, with data sets, which can contain sub-datasets which can be broken down into individual logical data files, generalised in the model as Atomic Data Objects \(ADOs\), as they may not be held in file store, but in for example databases. At each level of granularity, metadata can be provided giving representation information \(as in OAIS\) at the appropriate level of the data holding. At each stage of the data collection process, data is stored in a set of physical files with a physical location.

It is possible that there may be different versions of the data sets in the holding. In a general data portal, all stages of the process should be stored and made available as reviewers of the data holdings may wish to determine the nature of the analysis performed, and other scientist may wish to use the raw data to perform different analyses. Thus type markers \('raw', 'intermediate', 'final'\) need to be kept with data sets and ADOs and relationships between them recorded.

The model distinguishes between the logical data holding, describing the data objects and their structural hierarchy, and the data location. The data location provides a mapping between the identifiers used in the data definition component of the metadata model, and the actual URL's of the files. This can provide facilities for describing mirror location for the whole structure, and also for individual files.

#### Parameters

Parameters can be associated with data holdings, data sets, or ADOs. The same metadata item is used to represent either experimental conditions and measured items stored as data points in the data collection, but are distinguished via a parameter type qualifier \('fixed' or 'measured'\). Each parameter has a set of fields describing its name \(e.g. temperature, pressure\), its value \(if fixed in as an input parameter\), the units of measurement used to qualify the data points, the range of values over-which a parameter can take and the error margin expected on the value.

## Reference implementation

The ICAT system for cataloguing facility-generated experimental data has been in development within STFC over several years for in use at both the ISIS Facility and the Diamond Light Source. It forms part of an infrastructure supporting data management across the scientific lifecycle, and is now an open-source development project.

### The ICAT Infrastructure

An integrated approach has been taken to provide data infrastructure within STFC. A core component is an information catalogue - the ICAT - which collates metadata about the experiment from different stages of the experimental lifecycle by integrating with a number of different systems supporting that stage, from proposal to publication. Thus systems which could be integrated across the lifecycle would include:

* Proposals. Once awarded beam-time at a facility, an entry is created in ICAT that describes the proposed experiment. Thus we can collect descriptive information on who is doing what experiment for what purpose.
* Experiment. Data collected from the experiment will be indexed by ICAT \(with additional experimental conditions\) and made available to the experimental team
* Analysed Data. The end user will have the capability to upload any desired analysed data and associate it with their experiments.
* Publication. Using ICAT the scientist is also able to associate publications with the experiment and reference data from publications.

  The ICAT collects metadata across the experimental lifecycle as automatically as is possible by interacting with a number of other associated systems almost all of which already exist as part of the operating environment. Thus core metadata on teh experiment context is collected from the proposal system, information about parameters from data acquisition, etc. Thus the system is efficiently propagating metadata through the system, maintaining accuracy and completeness, and negating the need for retyping. There are a number of features which ICAT needs to accommodate to support its user community.

* Secure distributed access to data. The user community for facilities is distributed across institutions who wish to access their data when they return to their home institutions. Therefore, ICAT provides a web-based front-end accessible from the scientist's desktop, which allows secure access to their data, while enabling the setting of appropriate access conditions so that it can be shared across experimental teams or collaborators.

* Flexible data searching. Data can be annotated with appropriate tags and keywords, most of which come from the proposal system. These annotations are used to search for data in a meaningful way, via a taxonomy of scientific terms, via resources \(instruments, beam-lines\) provided at facilities, or through sample and experimental parameters, such as temperature, pressure etc
* A scalable architecture. The ICAT infrastructure needs to be able to be scaled to allow rapid access to large volumes of data both in absolute size and also in the number of discrete data files which are indexed.
* An extensible and flexible architecture. The system should be adaptable to local requirements at different facilities and to be tailored to the needs of particular user communities. ICAT has adopted a modular open source approach which allows code to be shared and adapted.
* Integration with analysis tools. Data searching and access should be integrated with data analysis and visualisation tools to further process the data. ICAT allows data access to end user programs via its API.
* Access to high performance resources. In order to analyse the large quantities of data, the ICAT should provide seamless access to high-performance resources, such as computing clusters and large-scale data storage.
* Linking to other scientific outputs. Scientific data is part of a larger science lifecycle which includes other outputs, particularly formal publications, but also more informal forms of scientific communication, such as laboratory notebooks, blogs and newsgroups. ICAT should provide a means to link data with publications.
* Data Policy. Facilities are developing data policies to formalise access to data for the user community. While publically funded data is in principle publically available, in practice it is made available to investigators initially for their research. For example within ISIS, ICAT enforces a 3 year embargo on data \(an additional year can be requested\), and commercially funded data is never made public. The Instrument Scientists can access all data from the beam-line they are responsible for. Calibration data is public, but secondary analysis data that involves additional IPR is private for perpetuity unless explicitly shared by user

  The core component of the ICAT tool set, the ICAT itself, is a database storing the metadata associated with scientific resources. This provides a well defined API that provides a uniform interface to experimental data and a mechanism to link all aspects of research from proposal through to publication. This is published as a web-service interface and allows bindings which could be in any languages so that end user applications can interact with the ICAT.

  A web-based front end \("TopCat"\) provides an alternative standard interface allowing browsing and searching of the catalogue and access to the experimental data, accessible from within the facility and at their home institution.

  The the ICAT interfaces to the data storage system, for example to a virtualised file store on a mass-storage system; this can be tailored to other storage systems - this functionality has been separeated out into the ICAT Data Service \(IDS\). There are also interfaces to the user database and single sign-on systems which control user identification and authentication within the facility. The ICAT can also be linked to other systems which supply it with data, especially the proposal system, initiating investigations. Further interfaces to e-Science services such as high-performance computing or visualisation, to the publications system cross-linking with publication data and software libraries can also be added.

## Conclusions on the Information Model

The information model which has been developed within the CSMD has proven a robust and tested model for capturing information about experiments and the associated raw data. It is simple and reasonably generic, while allowing sufficient detail to provide users with a manageable search and discovery interface.

