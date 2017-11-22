IDS Server Release Notes
========================


1.8.0
-----

Bug fix release - with new style deployment.

* Workaround for oracle planning bug.
* Make the implementation of getSize much more efficient.
* Modified a ContainerResponseFilter to allow Content-Type headers to
  be transmitted properly.
* Provided a restful "version" call.  The getApiVersion call is marked
  as deprecated.
* Make isPrepared more efficient.
* Ensure that Tidier action runs at high priority.
* Plugins need a slightly different interface - hence the move to
  1.8.0.
* File checker for Datasets no longer generates an error for an empty
  Dataset.
* Add list of failed restores to the information returned by
  getServiceStatus.
* Avoid marking files as unavailable when unsure.
* The prepareData call resets flags marking previous restore failures.


1.7.0
-----

Deal with multiple Datafiles with the same location and bug fixes.

* If the icat.url in the ids.properties file contains a recognised
  wsdl suffix it will be removed.  It is better not to include any
  suffix however.
* It is now permissible to have multiple Datafiles with the same
  location.  The IDS has no mechanism to write such as structure
  however it can arise from an icat call to the cloneEntity operation
  or by storing and cataloguing data outside the IDS.  It is possible
  for the getSize call to be inaccurate however as a physical file may
  be counted more than once.


1.6.0
-----

Checks the value of maxEntities from the ICAT it is using and various
improvements and bug fixes.

* No attempt will be made to restore a file when it is already
  on-line.
* The server checks the value of maxEntities from the ICAT it is using
  to avoid generating queries that will fail in ICAT.
* The messages from the file checker have been improved when storage
  is by dataset and a zip file is found to be corrupt.
* Now waits for ICAT connection if ICAT not available at startup.
* Protect against non numeric values being passed to the put call.
* Produces JMS logging for all calls as specified in the
  ids.properties file.
* Moved to the latest icat.utils to support multiple containers.
* Uses logback via slf4j rather than log4j via slf4j.
* A race condition has been avoided which could have resulted in the
  loss of data.
* If writing to secondary storage fails then archive is inhibited for
  that data set or data file.  This is to prevent data loss.
* Code changed to be more efficient when dealing with very large
  investigations or data sets.
* container specified in ids-setup.properties must an entry from
  TargetServer Though only Glassfish is working properly at the
  moment.


1.5.0
-----

New getDatafileIds, getIcatUrl and improved performance of prepareData.

* Added getDatafileIds call.
* Improved performance of prepareData by performing some operations
  asynchronously.
* Changed values of filesCheck.lastIdFile and filesCheck.errorLog to
  be relative to ../data/ids rather than ../data/icat in the
  ids.properties.example file.
* getStatus allows sessionId to be null.


1.4.0
-----

New getIcatUrl and now uses icat 4.5 client.

* Bug fix release to fix handling of corrupt filesCheck.lastIdFile
  file.
* Work with icat 4.5
* Added getIcatUrl call.
* All calls now produce CORS headers.


1.3.1
-----

Bug fix release to fix file handle leak.

* Minor corrections to the documentation.
* Ensure that files are closed when returned individually.
* Remove redundant (and possibly dangerous) line of code from
  DsWriter.


1.3.0
-----

Add getApiVersion call, make major change to prepareData, make it also
support archive by datafile and provide the option of protecting
Datafile.location with a cryptographic hash.

* getApiVersion call added to return version of server.
* ids.properties values: preparedCacheSize1024bytes,
  datasetCacheSize1024bytes, compressDatasetCache, and
  tolerateWrongCompression are no longer used.
* ids.properties value preparedCount has changed its meaning.
* ids.properties value tidyBlockSize has been added.
* ids.properties value maxIdsInQuery has been added.
* The json string returned by getServiceStatus has changed.
* The Datafile.location value can be protected by a cryptographic
  hash. This is enabled by adding a property "key" to the
  ids.properties file.


1.2.0
-----

For two level storage clean up main storage as needed.

* Add parameters: startArchivingLevel1024bytes,
  stopArchivingLevel1024bytes and linkLifetimeSeconds.
* Cope with some obscure file checker errors.
* Datasets are archived when main storage becomes too full.


1.1.0
-----

Provide background file checking and facility control of returned zip
file structure.

* Another file handle leak fixed.
* Compatible with plugin interface 1.1.0.
* Provide background file checking - which requires that the parameter
  checkFiles.parallelCount is set to an integer.  If the integer is
  non zero then other entries must be set in the ids.properties file.
* ids.properties needs a new parameter: plugin.zipMapper.class which
  identifies the code to define the zip file structure.


1.0.1
-----

Bug fix release.

* Files are now being closed properly to avoid running out of file
  handles.


1.0.0
-----

Initial public release.
