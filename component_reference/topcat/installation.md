# TopCAT Installation Guide

## Compatibility

TopCAT requires Java 8+, icat.server 4.6+ and ids.server 1.5.0.+

## Prerequisites

* The TopCAT distribution: 
  [topcat-2.3.5-distro.zip](https://repo.icatproject.org/repo/org/icatproject/topcat/2.3.5/topcat-2.3.5-distro.zip)
* A suitable deployed container (here assumed to be glassfish) to support a web application. Testing has been carried out with Glassfish 4.0. 
  [Glassfish installation instructions](http://icatproject.org/installation/glassfish/)
   are available.
* A database as described in 
  [Database installation instructions](http://icatproject.org/installation/database/)
   installed on the server.
* Python (version 2.4 to 2.7) installed on the server.

## Summary of Steps

1. Please follow the 
   [generic installation instructions](http://icatproject.org/installation/component/)
2. Check that it works by going to https://example.com:8181 or whatever is the name of your server.

## Schema upgrade

If you are using an older version of Topcat you may need to apply some migrations to your database. These can be found in the “migrations/” directory within the Topcat release.

### Upgrade 2.1.0 schema to 2.2.0

For MySQL:

      ALTER TABLE `DOWNLOAD` ADD `THE_SIZE` bigint(20) DEFAULT NULL;


For Oracle:

```
  ALTER TABLE DOWNLOAD ADD THE_SIZE NUMBER(19, 0) DEFAULT 0 NOT NULL;

```

## Configuration Overview

Topcat is configured in two main places:

* **the backend**
   - i.e. files which topcat server reads/uses
* **the frontend**
   - i.e. files which the browser reads/uses

### Backend configuration

The backend configuration consists of two files:

* **topcat-setup.properties**
   - used to configure the application container e.g. database connection and mail server etc…
* **topcat.properties**
   - used to configure Topcat (application) specific settings e.g. email messages or IDS polling intervals.

### Frontend configuration

The frontend configuration consists of three files:

* **topcat.json**
   - used to configure the structure and behaviour of the frontend e.g. grid columns and ICAT data hierarchy.
* **lang.json**
   - used to changed the words on topcat; either the actual language e.g. from English to French, or to substitute words that make more sense to your facility e.g. “Investigator” to “Scientist”
* **topcat.css**
   - allows you to change Topcat’s styling the page colours etc…

## topcat-setup.properties

Note: ’\#’s are comments.  \# Derby Database

```
  !db.target   = derby
  !db.driver   = org.apache.derby.jdbc.ClientDataSource
  !db.url      = jdbc:derby:topcat;create\\=true
  !db.username = APP
  !db.password = APP

  # MySQL Database
  db.target      = mysql
  db.url         = jdbc:mysql://localhost:3306/topcat
  db.driver      = com.mysql.jdbc.jdbc2.optional.MysqlDataSource
  db.username    = icat
  db.password    = icat

  secure         = true
  container      = Glassfish
  home           = /home/fisher/pf/glassfish4
  port           = 4848

  # Email setup see http://docs.oracle.com/cd/E26576_01/doc.312/e24938/create-javamail-resource.htm#GSRFM00035
  mail.host=smtp.example.com
  mail.user=user@example.com
  mail.from=no-reply@example.com

  #see https://javamail.java.net/nonav/docs/api/ for list of properties
  mail.property=mail.smtp.port="25":mail.smtp.from="no-reply@example.com"

```

## topcat.properties

Note: ’\#’s are comments.

```
  # enable send email
  mail.enable=true

  # The email subject. Tokens available are:
  # ${userName} - user username
  # ${email} - user email
  # ${facilityName} - the facility key (defined in frontend config)
  # ${preparedId} - the prepared Id of the download request
  # ${fileName} - the download name
  # ${size} - the download size
  # ${downloadUrl} - the download url
  mail.subject=TopCAT Download ${fileName} Ready

  # The email body message for https downloads. All subject tokens as above are available.
  mail.body.https=Hi ${userName},\n\nYour ${size} download ${fileName} has been prepared and is ready for download at ${downloadUrl}.\n\nThank you for using TopCAT.

  # The email body message for https downloads. All subject tokens as above are available.
  mail.body.globus=Hi ${userName}, \n\nYour ${size} Globus download ${fileName} is ready. Please see https:/example.com/#/globus-faq for more information on how to download using Globus.\n\nThank you for using TopCAT

  # The email body message for smartclient downloads. All subject tokens as above are available.
  mail.body.smartclient=Hi ${userName}, \n\nYour ${size} SmartClient download ${fileName} is ready. Please check your smartclient home directory for your files.\n\nThank you for using TopCAT

  # The email body message for SCARF downloads. All subject tokens as above are available.
  mail.body.scarf=Hi ${userName}, \n\nYour ${size} SCARF download ${fileName} is ready. Please see https:/example.com/#/scarf-faq for more information on how to download using SCARF.\n\nThank you for using TopCAT

  # The maximum number of datafiles for a getStatus call to the IDS for two level storage
  ids.getStatus.max=100

  # The delay in seconds before polling starts. This delay is to gives the ids a chance to do its thing before we query it
  poll.delay=600

  # The wait time in seconds between each poll to the IDS.
  poll.interval.wait=600

  # A list of usernames that can use the admin REST API and Topcat admin user interface
  adminUserNames=simple/root, uows/elz087, asd345, ldap/fgh123

```

## topcat.json

### High level overview

A high level overview of the topcat.json file is as follows:

```
  {
```

```
    "site": {...},
    "facilities": {...}
  }

```

from the above example there are two attributes defined:

* “site” - configures anything anything global across facilities e.g. the cart, information pages or branding etc.
* “facilities” - configures anything specific to a facility e.g. authentication, column headings or download transport (i.e. delivery) methods.

### Medium level overview

If we expand the attributes in high level overview we can get a medium overview as follows:

```
  {    
      "site": {
          "topcatUrl": "https://example.com",
          "home" : "my-data",
          "enableEuCookieLaw" : true,
          "paging" : {...},
          "breadcrumb": {...},
          "serviceStatus": {...},
          "maintenanceMode": {...},
          "search": {...},
          "browse":{...},
          "cart":{...},
          "myDownloads":{...},
          "pages" : [...]
      },
      "facilities": [
          {
              "name": "DLS",
              "title": "Diamond Light Source",
              "icatUrl": "https://example.com",
              "idsUrl": "https://example.com",
              "idsUploadDatafileFormat": "example_datafile_format_name",
              "idsUploadDatasetType": "example_dataset_type_name",
              "idsUploadMaxTotalFileSize": 10000000000,
              "hierarchy": [...],
              "authenticationTypes": [...],
              "downloadTransportTypes": [...],
              "admin":{...},
              "myData": {...},
              "browse":{...}
          }
      ]
  }

```

from the above following attributes defined:

* “site”
  * “topcatUrl” - the path to a valid Topcat REST API (optional).
  * “home” - the section the user gets redirected to after logging in can be “my-data”, “browse” or “search”
  * “enableEuCookieLaw” - will show a cookie policy banner which the user can dismiss.
  * “paging” - specifies the paging type configuration i.e. for either paged or infinite scroll
  * “breadcrumb” - specifies global breadcrumb options
  * “search” - specifies the structure of the search results and search fields for searching across facilities.
  * “browse” - specifies the structure of the root of “browse” section of the site. This only applies to a Topcat with multiple facilities.
  * “cart” - specifies the structure of the cart.
  * “myDownloads” - specifies the structure of ‘downloads’ dialog box.
  * “pages” - allows you to define information pages e.g. “About Us” or “Contact” etc…
* “facilities” - an array of facility configuration objects.
  * \[facility\]
    * “name” - The facility name as it appears on the (Icat) database. Used as a key to reference the facility details.
    * “title” - A user friendly title that will appear in the tabs etc…
    * “icatUrl” - A URL to a valid Icat REST API (optional - if not explicity specified it will obtain this url from the IDS server below)
    * “idsUrl” - A URL to a valid IDS REST API
    * “idsUploadDatafileFormat” - The name of the Icat DatafileFormat entity that will be associated with the uploaded files.
    * “idsUploadDatasetType” - The name of the Icat DatasetType entity that will be associated with dataset that gets created when uploading files at the dataset level.
    * idsUploadMaxTotalFileSize" - The maximum total amount a user can upload in one go in bytes.
    * “hierarchy” - the entity heierachy of the browse section.
    * “authenticationTypes” - specifies the authentication plugins to be used e.g. LDAP (optional - if not explicity specified it will obtain them from the icat server)
    * “downloadTransportTypes” - specifies the download delivery methods e.g. ‘https’ (via a browser) or ‘globus’ (a type of glorified ftp to deal with large files).
    * “admin” - defines the structure of admin interface. This interface is only available to admin users (specified in topcat.properties).
    * “myData” - specifies the structure of the “My Data” section.
    * “browse” - specifies the structure of the hierachical browse section.

### topcat.json: “site” &gt; “paging”

Specifies the paging type configuration i.e. for either paged or infinite scroll.

An example configuration for infinite scrolling:

```
{
```

```
  "site": {
    "paging" : {
        "pagingType": "scroll",
        "scrollPageSize": 50,
        "scrollRowFromEnd": 10
    }
  }
}

```

* “pagingType” - can be either “page” or “scroll”.
* “scrollPageSize” - the number rows to return with each request.
* “scrollRowFromEnd” - the row from the end which after scrolling past will trigger another page request to load more rows.

An example configuration for standard page based pagination:

```
{
  "site": {
    "paging" : {
        "pagingType": "page",
        "paginationNumberOfRows": 10,
        "paginationPageSizes": [
            10,
            25,
            50,
            100,
            1000
        ]
    }
  }
}

```

* “pagingType” - can be either “page” or “scroll”.
* “paginationNumberOfRows” - the default number of rows per page
* “paginationPageSizes” - the possible page sizes the user can select.

### topcat.json: “site” &gt; “breadcrumb”

Specifies global breadcrumb options.

An example breadcrumb configuration:

```
{
  "site": {
    "breadcrumb": {
        "maxTitleLength": 30
    }
  }
}

```

* “maxTitleLength” - the maximum length after which it will get truncated with ellipses.

### topcat.json: “site” &gt; “search”

Specifies the structure of the search results and search fields for searching across facilities.

An example (partial) configuration:

```
{
```

```
  "site": {
    "search": {
        "enableParameters": false,
        "enableSamples": false,
        "gridOptions": {
            "investigation": {...},
            "dataset": {...},
            "datafile": {...}
        }
    }
  }
}

```

* “enableParameters” - specifies whether the parameter search feature should be enabled or not. Can be true or false.
* “enableSamples” - specifies whether the sample search feature should be enabled or not. Can be true or false.
* gridOptions
  * investigation - the grid options for the “investigation” Icat entity type. See “gridOptions configuration” for more information.
  * dataset - the grid options for the “dataset” Icat entity type. See “gridOptions configuration” for more information.
  * datafile - the grid options for the “datafile” Icat entity type. See “gridOptions configuration” for more information.

### topcat.json: “site” &gt; “browse”

Specifies the structure of the root of “browse” section of the site. This only applies to a Topcat with multiple facilities.

An example (partial) configuration:

```
{
  "site": {
    "browse":{
        "gridOptions": {...},
        "metaTabs": [...]
    }
  }
}

```

* the grid options for the “facility” Icat entity type. See “gridOptions configuration” for more information.
* the meta tabs for the “facility” Icat entity type. See “metaTabs configuration” for more information.

### topcat.json “site” &gt; “cart”

Specifies the structure of the cart.

An example (partial) configuration:

```
{
  "site": {
    "cart":{
        "maxDatafileCount": 1000000,
        "maxTotalSize": 1000000000000,
        "gridOptions": {...}
    }
  }
}

```

* maxDatafileCount - the maximum number of datafiles that can be added to a cart, this includes all files in the hierachy of each entity e.g. if you added a dataset to the cart this includes all the files inside that dataset.
* maxTotalSize - the total size of the potentual download.
* “gridOptions” - the grid options for the “cartItem” Topcat entity type. See “gridOptions configuration” for more information.

### topcat.json: “site” &gt; “myDownloads”

Specifies the structure of ‘downloads’ dialog box.

An example (partial) configuration:

```
{
```

```
  "site": {
    "myDownloads":{
        "gridOptions": {...}
    }
  }
}

```

* “gridOptions” - the grid options for the “download” Topcat entity type. See “gridOptions configuration” for more information.

### topcat.json: “site” &gt; “pages”

Allows you to define information pages e.g. “About Us” or “Contact” etc…

An example configuration:

```
{
  "site": {
    "pages" : [
      {
        "url" : "/about",
        "stateName": "about",
        "addToNavBar": {
            "linkLabel" : "MAIN_NAVIGATION.ABOUT",
            "align" : "left"
        },
        "contents": "PAGE.ABOUT.HTML"
      }
    ]
  }
}

```

* “url” - The url of the page.
* “stateName” - A unique token that represents that state.
* “addToNavBar” - Specified whether or not as link should be added to the top bar.
  * “linkLabel” - the label (i.e. text) for the link.
  * “align” - whether the link should be on the “left” or “right” hand side.
* “contents” - the html contents of the page specified in lang.json file. If not defined it will try and work the translation variable based on the stateName. e.g.
  * stateName: “about” -
    &gt;
     contents: “PAGE.ABOUT.HTML”
  * stateName: “globus-help” -
    &gt;
     contents: “PAGE.GLOBUS\_HELP.HTML”

### topcat.json: “facilities” &gt; \[facility\] &gt; “hierachy”

The entity heierachy of the browse section.

An example configuration:

```
{
  "facilities": [
    {
      "hierarchy": [
        "facility",
        "proposal",
        "investigation",
        "dataset",
        "datafile"
      ]
    }
  ]
}

```

The above configuration will make the user browse via “facility” &gt; “proposal” &gt; “investigation” &gt; “dataset” &gt; “datafile”.

### topcat.json “facilities” &gt; \[facility\] &gt; “authenticationTypes”

Specifies the authentication plugins to be used e.g. LDAP

An example configuration:

```
{
  "facilities": [
    {
      "authenticationTypes": [
          {
              "title": "Simple",
              "plugin": "simple"
          },
          {
              "title": "DB",
              "plugin": "db"
          }
      ]
    }
  ]
}

```

* title - the title of the plugin as it appears on the login page authentication types dropdown menu.
* name - a name that is used to programmatically reference the plugin on the Icat server.

### topcat.json: “facilities” &gt; \[facility\] &gt; “downloadTransportTypes”

Specifies the download delivery methods e.g. ‘https’ (via a browser) or ‘globus’ (a type of glorified ftp to deal with large files).

An example configuration:

```
{
  "facilities": [
    {
      "downloadTransportTypes": [
          {
              "type" : "https",
              "idsUrl": "https://fdsgos11.fds.rl.ac.uk"
          },
          {
              "type" : "globus",
              "idsUrl": "https://fdsgos11.fds.rl.ac.uk"
          },
          {
              "type" : "scarf",
              "idsUrl": "https://dls-ids01.scarf.rl.ac.uk"
          }
      ]
    ]
  }
}

```

* type - the download method the will be used to retrieve the download.
* idsUrl - a url to a valid IDS API that can handle the particular download transport type.

### topcat.json: “facilities” &gt; \[facility\] &gt; “admin”

Defines the structure of admin interface. This interface is only available to admin users (specified in topcat.properties).

An example (partial) configuration:

```
{
  "facilities": [
    {
      "admin":{
        "gridOptions": {...}
      }
    }
  ]
}

```

* “gridOptions” - the grid options for the “download” Topcat entity type. See “gridOptions configuration” for more information.

### topcat.json: “facilities” &gt; \[facility\] &gt; “myData”

Specifies the structure of the “My Data” section.

An example (partial) configuration:

```
{
  "facilities": [
    {
      "myData": {
          "entityType" : "investigation",
          "gridOptions": {...}
      }
    }
  ]
}

```

* “entityType” - the entity type can be either “investigation”, “dataset” or “datafile”.
* “gridOptions” - the grid options for the Icat entity type (specified). See “gridOptions configuration” for more information.

### topcat.json: “facilities” &gt; \[facility\] &gt; “browse”

Specifies the structure of the hierachical browse section.

An example (partial) configuration:

```
{
```

```
  "facilities": [
    {
      "browse": {
        "investigation": {
            "gridOptions": {...},
            "metaTabs": [...]
        },
        "proposal": {...},
        "dataset": {...},
        "datafile": {...}
      }
    }
  ]
}

```

* “gridOptions” - the grid options for the current Icat entity type. See “gridOptions configuration” for more information.
* “metaTabs” - the meta tabs for the current Icat entity type. See “metaTabs configuration” for more information.

### topcat.json: gridOptions configuration

General principles configuring the gridOptions.

An example configuration:

```
  "gridOptions": {
    "enableSelection": true,
    "enableUpload": true,
    "columnDefs": [
        {
            "field": "visitId",
            "link": true,
            "breadcrumb": true
        },
        {
            "field": "size"
        },
        {
            "field": "datasetCount"
        },
        {
            "field": "datafileCount"
        },
        {
            "field": "investigationInstrument.fullName"
        },
        {
            "field": "startDate",
            "excludeFuture": true,
            "sort": {
              "direction": "desc",
              "priority": 1
            }
        },
        {
            "field": "endDate"
        }
    ]
  }

```

* “enableSelection” - whether or not the entity can be added to the cart. This only applies to “investigation”, “dataset” or “datafile”.
* “enableUpload” - whether or not a user can upload files at this at this level (i.e. enable an “Upload” button). This only applies to “dataset” or “datafile”.
  * “columnDefs” - the column definitions of the grid.
    * “field” - The field you want to display in the column.
      * the dot notation e.g. “investigationInstrument.fullName” refers to “\[variable name\].\[field name\]” - see the “standard jpql variables” for more info.
      * there are also 3 special field names which get loaded in asynchronously.
        * size - the sum of all the descendant files fileSize (applies to the investigation and dataset entities).
        * datasetCount - the total number of descendant datasets (applies to the investigation entity).
        * datafileCount - the total number of descendant datafiles (applies to the investigation and dataset entities).
    * “link” - whether or not to link to the next entity in the hierachy.
    * “breadcrumb” - whether or not you want to use the field value as title in the breadcrumb. By default this will be ‘title’ or ‘name’ if the entity has these fields.
    * “excludeFuture” - will preload the date ‘to’ filter with the current time, thus elliminating any rows in the future.
    * “sort” - allows the sorting of the rows by this particular column.
      * “direction” - the direction the rows should be sorted by can be “asc” (ascending) or “desc” (descending).
      * “priority” - if sorting by multiple columns, you can set the order in which rows get sorted by e.g. “title” then “startDate”.

### topcat.json: metaTabs configuration

General principles configuring the metaTabs.

An example configuration:

```
"metaTabs": [
    {
        "title": "METATABS.DATASET.TABTITLE",
        "items": [
            {
                "field": "name"
            },
            {
                "field": "description"
            },
            {
                "label": "METATABS.DATASET.START_DATE",
                "field": "startDate",
                "template": "{{content.value | date:'yyyy-MM-dd'}}"
            },
            {
                "label": "METATABS.DATASET.END_DATE",
                "field": "endDate",
                "template": "{{content.value | date:'yyyy-MM-dd'}}"
            }
        ]
    },
    {
        "title": "METATABS.DATASET_TYPE.TABTITLE",
        "items": [
            {
                "field": "datasetType.name"
            },
            {
                "field": "datasetType.description"
            }
        ]
    }
]

```

* “title” - the title of the tab.
  * items - the items to be displayed inside the metatab
    * “field” - the field to be displayed.
    * “label” - a label to describe what the field is. By default this will field name.
    * “template” - a custom (angular) template to display the field.

### topcat.json: standard jpql variables

Topcat defines the following jpql variables - which if used from any icat entity type will automatically work out any joins required to create a jpql statement for fetching the data from icat:

* **datafile** - referencing the relative datafile.
* **datafileParameter**
   - referencing the relative datafile.parameters.
* **datafileParameterType**
   - referencing the relative datafile.parameters.type.
* **dataset**
   - referencing the relative dataset.
* **datasetParameter**
   - referencing the relative dataset.parameters.
* **datasetParameterType**
   - referencing the relative dataset.parameters.type.
* **datasetSample**
   - referencing the relative dataset.samples.
* **datasetType**
   - referencing the relative dataset.type.
* **facility**
   - referencing the relative facility.
* **facilityCycle**
   - referencing the relative facility.facilityCycles.
* **instrument**
   - referencing the relative facility.instruments.
* **instrumentScientist**
   - referencing the relative instrument.instrumentScientists.user.
* **instrumentScientistPivot**
   - referencing the relative instrument.instrumentScientists.
* **investigation**
   - referencing the relative investigation.
* **investigationGroup**
   - referencing the relative investigation.investigationGroups.grouping.
* **investigationInstrument**
   - referencing the relative investigation.investigationInstruments.instrument.
* **investigationInstrumentPivot**
   - referencing the relative investigation.investigationInstruments.
* **investigationParameter**
   - referencing the relative investigation.parameters.
* **investigationParameterType**
   - referencing the relative investigation.parameters.type.
* **investigationSample**
   - referencing the relative investigation.samples.
* **investigationUser**
   - referencing the relative investigation.investigationUsers.user.
* **investigationUserPivot**
   - referencing the relative investigation.investigationUsers.
* **publication**
   - referencing the relative investigation.publications.

To get a better understanding of what these jpql variables refer to please view the [icat schema documentation](https://icatproject.org/mvn/site/icat/server/4.6.1/schema.html).

## lang.json

A hierachy of translation strings. Self explanatory.

## topcat.css

A standard css file adding in custom css rules.

