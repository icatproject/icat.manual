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

For more detail on the CSMD see the CSMD Information Page \(http://icatproject-contrib.github.io/CSMD/\).



