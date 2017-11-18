# Setting Up

This section describes the common tasks which are performed to set up the ICAT system after installation is complete.

- You may wish to integrate ICAT with your site's existing method of authenticating users. This will enable users to log in with their familiar credentials. To do this you will need to [create an authentication plugin](./create_an_auth_plugin.md).
- If your site stores its data in a system other than a file system, you will need to [create a storage plugin](./create_a_storage_plugin.md) to allow the ICAT Data Service (IDS) to access your files.
- Finally, in order to load data into ICAT, you will want to [write an ingestion script](./write_an_ingestion_script.md) which uses one of the ICAT [APIs](../apis/README.md).
