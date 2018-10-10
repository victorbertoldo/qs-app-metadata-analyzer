# qs-app-metadata-analyzer
A Qlik Sense application that iterates over app metadata endpoints in combination with the QRS API providing a holistic view of your application metadata across your site.

![Dashboard](https://s3.amazonaws.com/dpi-sse/dpi-qlik-sense-app-metadata-analyzer-server/AppMetaDashboard.png)

## REQUIREMENTS

- Qlik Sense Enterprise June 2018+

## LAYOUT

- [About](#about)
- [Setup](#setup)
- [Thresholds](#threshold-settings)
- [Screenshots](#screenshots)
 
## About

As of the Qlik Sense June 2018 release, a new application level metadata endpoint is available. Data is populated for this endpoint per app post-reload in a June 2018+ environment. This Qlik application iterates over every application metadata endpoint along with several other QRS calls (Nodes, Apps, Proxies, LB audit), ultimately providing a comprehensive dashboard to analyze your application metadata server-wide. This allows you to have a holistic view of the makeup of all of your Qlik applications, eanbling you to have awareness at a granular level of the types of application in your organization. This application is 100% native to Qlik without any installer, and is easy to configure within the Qlik Sense Enterprise environment.

You can view this application metadata within your own June 2018+ environment at:
```
http(s)://{server}/api/v1/apps/{GUID}/data/metadata
```

where *server* is your Qlik Sense Enterprise server and *GUID* is the application ID. Note that the application does not need to be lifted into RAM for the metadata to be accessed.

Data from this endpoint includes:
- server metadata including number of server cores, total server RAM
- reload time
- app RAM base footprint
- field metadata including cardinality, tags, total count, RAM size
- table metadata including fields, rows, key fields, RAM size

Example output from that endpoint can be seen [here](https://s3.amazonaws.com/dpi-sse/dpi-qlik-sense-app-metadata-analyzer-server/metadata_example.json).


## Setup

1. Import the qvf into the QMC.
2. Grant **'Read'** access to the **'monitor_apps_REST_app'** data connection in the QMC to the user that will be reloading the application if you plan on reloading the app from the Hub. Ensure that the user can see this connection in the Data Load Editor. Occasionally this takes a services restart to take effect.
3. Ensure that the Qlik Sense service account has **'RootAdmin'** access. The **'monitor_apps_REST_app'** data connection is reloaded by the service account, and that account needs access to specific resources via the QRS API that are not available otherwise.
4. Reload the application either via a Task in the QMC or as a user other than the service account from the Hub. If you are also logged in as the service account and triggering the reload from the Hub, you may see access pass errors.


## Treshold Settings
There are a number of configurable settings in the **Tresholds & Settings** section of the load script. These thresholds are used to create boolean fields in the data model that are then filterable and highlighted throughout the app. Please set these accordingly to your own standards of what your organization wants to maintain for an easier view of outliers. These settings are:
```
SET vAppDiskSizeThreshold = 524288000; // 500 MB
SET vAppRAMSizeThreshold = 1073751824; // 1 GB
SET vRAMToFileSizeRatioThreshold = 6; // RAM / File Size is typically between 4-6x
SET vAppRecordCountThreshold = 10000000; // Number of records in an app
SET vTableRecordCountThreshold = 10000000; // Number of records in a table
SET vFieldValueCountThreshold = 10000000; // Number of field records
SET vFieldCardinalityThreshold = 1000000; // Number of distinct field values
SET vNoOfFields = 150; // Number of Distinct Fields
SET vReloadTimeThreshold = 1800000; // 30 Minutes
```

## Screenshots
![Dashboard](https://s3.amazonaws.com/dpi-sse/dpi-qlik-sense-app-metadata-analyzer-server/AppMetaDashboard.png)
![Threshold Analysis](https://s3.amazonaws.com/dpi-sse/dpi-qlik-sense-app-metadata-analyzer-server/AppMetaThresholdAnalysis.png)
![App Availability](https://s3.amazonaws.com/dpi-sse/dpi-qlik-sense-app-metadata-analyzer-server/AppMetaAvailability.png)
![App Data Model](https://s3.amazonaws.com/dpi-sse/dpi-qlik-sense-app-metadata-analyzer-server/ApplicationMetadataAnalyzerDataModel.png)