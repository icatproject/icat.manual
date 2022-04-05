Chapter 11: Add Authentication Rules to ICAT
============================================

Overview
--------

ICAT has functionality to control data access. When used to its full extent, this can be very powerful and serves a real-world use case when allowing only particular groups of users to read/write to embargoed data.

For this tutorial, you will create a simple read rule for the `simple/reader` user that reads data from the ICAT Data Service (IDS). This user must be added to ICAT's database and the rules created with an associated grouping (so you can add other users to the rules in the future).

To simplify the commands shown in this file, the `$ICAT_SESSION` environment variable will be used from chapter 9 (adding test data).

Install jq
----------
To make dealing with ICAT API responses slightly eaiser, we will use `jq` to manipulate the JSON output. If you don't have it installed, it can be installed via yum:

```Shell
sudo yum install jq
```

Add simple/reader to icatdb
---------------------------
To add the `simple/reader` user into ICAT's database, send the following request to the ICAT API:

```Shell
curl -k --request POST 'https://localhost:8181/icat/entityManager' --header 'Content-Type: application/x-www-form-urlencoded' --data-urlencode 'sessionId='$ICAT_SESSION'' --data-urlencode 'entities=[
    {
        "User": {
            "name": "simple/reader",
            "fullName": "Simple Reader Account"
        }
    }
]' | jq .[0]
```

This will output the ID of the user that has just been created. For example:

```Shell
502
```

To make it easier to reference that ID in later requests, let's store the value in an environment variable:

```Shell
export READER_USER_ID=502
```

Create grouping
---------------
In ICAT, rules are associated with a group of users so users can be easily make use of multiple rules. Therefore we must create a grouping:

```Shell
curl -k --location --request POST 'https://localhost:8181/icat/entityManager' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'sessionId='$ICAT_SESSION'' \
--data-urlencode 'entities=[
    {
        "Grouping": {
            "name": "simple/reader grouping"
        }
    }
]' | jq .[0]
```

This will output the ID of the grouping that has just been created. For example:

```Shell
31
```

To make it easier to reference that ID in later requests, let's store the value in a new environment variable:

```Shell
export READER_GROUPING_ID=31
```

Create rules
------------
The next step is to create the rules. For the reader account to use the IDS, it will need read access to the `Investigation`, `Dataset` and `Datafile` entities. While creating the rules, we can link them to the grouping we previously created. This can be achieved by sending the following request to the ICAT API:

```Shell
curl -k --request POST 'https://localhost:8181/icat/entityManager' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'sessionId='$ICAT_SESSION'' \
--data-urlencode 'entities=[
    {
        "Rule": {
            "crudFlags": "R",
            "what": "Investigation",
            "grouping": {"id": '$READER_GROUPING_ID'}
        }
    },
    {
        "Rule": {
            "crudFlags": "R",
            "what": "Dataset",
            "grouping": {"id": '$READER_GROUPING_ID'}
        }
    },
    {
        "Rule": {
            "crudFlags": "R",
            "what": "Datafile",
            "grouping": {"id": '$READER_GROUPING_ID'}
        }
    }
]'
```

There's no need to export the IDs into environment variables as they won't be needed in future requests.


Create user group
-----------------
The final step is to link the grouping with the `simple/reader` user. In ICAT, there's an entity called `UserGroup` which links the grouping and the user previously created. To create a new `UserGroup` which links this data together, execute the following request:

```Shell
curl -k --request POST 'https://localhost:8181/icat/entityManager' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'sessionId='$ICAT_SESSION'' \
--data-urlencode 'entities=[
    {
        "UserGroup": {
            "user": {
                "id": '$READER_USER_ID'
            },
            "grouping": {
                "id": '$READER_GROUPING_ID'
            }
        }
    }
]'
```

To check this has worked, let's login to ICAT as the `simple/reader` and query the database:

```Shell
# Login as simple/reader
curl -k --data 'json={"plugin":"simple", "credentials": [{"username":"reader"}, {"password": "readerpw"}]}' -w'\n' https://localhost:8181/icat/session

# Save the session ID in an environment variable
export READER_ICAT_SESSION=999857dd-3b70-4c96-9fd4-a27511b3692d  

# Query for the first 10 datasets - this should return data because of the rule
curl -k --request GET 'https://localhost:8181/icat/entityManager?sessionId='$READER_ICAT_SESSION'&query=SELECT%20o%20FROM%20Dataset%20o%20LIMIT%200,10'

# Query for the first 10 instruments - this should return an empty list because the simple/reader has no read rule on the Instrument entity
curl -k --request GET 'https://localhost:8181/icat/entityManager?sessionId='$READER_ICAT_SESSION'&query=SELECT%20o%20FROM%20Instrument%20o%20LIMIT%200,10'
```
