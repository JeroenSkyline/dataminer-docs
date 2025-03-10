---
uid: General_Feature_Release_10.3.3
---

# General Feature Release 10.3.3 – Preview

> [!IMPORTANT]
> We are still working on this release. Some release notes may still be modified or moved to a later release. Check back soon for updates!

> [!TIP]
>
> - For release notes related to DataMiner Cube, see [DataMiner Cube Feature Release 10.3.3](xref:Cube_Feature_Release_10.3.3).
> - For release notes related to the DataMiner web applications, see [DataMiner web apps Feature Release 10.3.3](xref:Web_apps_Feature_Release_10.3.3).
> - For information on how to upgrade DataMiner, see [Upgrading a DataMiner Agent](xref:Upgrading_a_DataMiner_Agent).

## Highlights

*No highlights have been selected for this release yet*

## Other features

#### DataMiner Object Model: New 'ClientReadOnly' and 'AllowMultipleSections' properties [ID_35172]

<!-- MR 10.3.0 - FR 10.3.3 -->

##### ClientReadOnly property added to DomStatusFieldDescriptorLink class

Next to a *ReadOnly* property, the `DomStatusFieldDescriptorLink` class now also has a *ClientReadOnly* property.

- *ReadOnly* determines whether values of the field in question are read-only when the field has a particular status.

  When a field is marked as read-only for a specified status, the values cannot be changed when the `DomInstance` has this status. This also means that if no values were present before transitioning to this status, no values can be added as long as the `DomInstance` continues to have this status.

- *ClientReadOnly* determines whether users are allowed to assign a value to the field in question in the UI.

  Unlike the *ReadOnly* property, this property does allow users to assign a value to the field using the API (e.g. in a script).

If the *ReadOnly* property is true, the value of *ClientReadOnly* is ignored.

##### BREAKING CHANGE: AllowMultipleSections option added to SectionDefinitionLink and DomStatusSectionDefinitionLink classes

An *AllowMultipleSections* property has now been added to the `SectionDefinitionLink` (non-status system) and `DomStatusSectionDefinitionLink` (status system) classes. This property will allow you to define whether a `DomInstance` can have multiple `Sections` for a particular `SectionDefinition`.

When multiple sections are added to a `DomInstance`, and this is not marked as allowed, a `DomInstanceError` will be returned in the TraceData with the `MultipleSectionsNotAllowedForSectionDefinition` error reason. Additionally, in the `InvalidSections` property of the error, you will be able to find the ID(s) of the `SectionDefinition`(s) of which too many sections were found.

Rules that apply with regard to multiple sections:

- Non-status system (`DomDefinition` and `SectionDefinitionLinks`):

  - Multiple `Section`s for the same `SectionDefinition` are allowed on a `DomInstance` if the `SectionDefinition` has a link on the DomDefinition that has the *AllowMultipleSections* property set to true.
  
  - When a `Section` is added to an existing `DomInstance` that contains a ReadOnly field (marked as such in the `FieldDescriptor`), then that field cannot be given a value in the UI. It can only be given a value via the API or a script.

  - There are no limitations with regard to removing `Section`s.

- Status system (DomBehaviorDefinition & DomStatusSectionDefinitionLinks):

  - Multiple `Section`s for the same `SectionDefinition` are allowed in a specific status on one `DomInstance` if, for that status, the `SectionDefinition` has a link on the `DomBehaviorDefinition` that has the *AllowMultipleSections* property set to true.

  - When a `Section` is added, any field marked as *ReadOnly* or *ClientReadOnly* will not be assignable from the UI. However, if they are only marked as *ClientReadOnly*, they will be assignable via the API.

  - Removing a `Section` is not possible when a field of the section in question is marked as *ReadOnly* on the link. When this behavior is required, but you still want to prevent users from assigning a value in the UI, use the new *ClientReadOnly* property instead.

> [!IMPORTANT]
> This is a breaking change. Previously, it was possible to add multiple sections without having to set any property. After upgrading to DataMiner 10.3.3/10.3.0, you will need to update any existing `DomDefinition`s with multiple `Section`s.

##### Each section now has to contain all required fields

Up to now, if there were multiple sections, it was allowed for some of those sections to not contain all the required fields. From now on, every section must contain each and every required field.

#### DataMiner Object Models: Action buttons can now be configured to launch an interactive Automation script when clicked [ID_35226]

<!-- MR 10.4.0 - FR 10.3.3 -->

An action button can now be configured to launch an interactive Automation script when clicked. To do so, set the *IsInteractive* property of the action to true.

When such a button is clicked in a low-code app, the UI of the interactive Automation script will be displayed in a pop-up window on top of the low-code app.

> [!NOTE]
> One button can only execute one action. So, one button can only execute one interactive Automation script.

#### DataMiner Object Models: New field descriptors [ID_35278]

<!-- MR 10.4.0 - FR 10.3.3 -->

Two new field descriptors have been added to the DataMiner Object Models:

- GroupFieldDescriptor: Can be used to define that a field should contain the name of a DataMiner user group.

- UserFieldDescriptor: Can be used to define that a field should contain the name of a DataMiner user. There is a *GroupNames* property that can be used to define which groups the user can be a part of.

#### SLLogCollector will now order the Standalone BPA Executor tool to execute all BPA tests available in the system [ID_35436]

<!-- MR 10.4.0 - FR 10.3.3 -->

Each time the *SLLogCollector* tool is run, it will now order the *Standalone BPA Executor* tool to execute all BPA tests available in the system and store the results in the `C:\Skyline DataMiner\Logging\WatchDog\Reports\Pending Reports` folder.

The names of the files containing the test results will have the following format: `<BPA Name>_<Date(yyyy-MM-dd_HH)>`

## Changes

### Enhancements

#### Security enhancements [ID_34894] [ID_35331]

<!-- RN 34894: MR 10.2.0 [CU12] - FR 10.3.3 -->
<!-- RN 35331: MR 10.3.0 - FR 10.3.3 -->

A number of security enhancements have been made.

#### Elasticsearch: 'Request Entity Too Large (413)' errors will now be prevented when writing data in bulk [ID_34937]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

When data was written to Elasticsearch in bulk, up to now, DataMiner would throw a `Request Entity Too Large (413)` error when the amount of data exceeded the 100 MB limit.

From now on, DataMiner will detect when too much data is being sent in a single request and will split up the data into smaller parts.

#### SLLogCollector: Multiple instances can now be run simultaneously [ID_35204]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

Multiple instances of the SLLogCollector tool can now be run simultaneously.

#### SLAnalytics - Pattern matching: Manually created tags will now be saved as pattern occurrences [ID_35299]

<!-- MR 10.4.0 - FR 10.3.3 -->

From now on, when you define a tag for pattern matching, the pattern you selected will be saved as a pattern occurrence in the Elasticsearch database and highlighted in bright orange, similar to so-called "streaming matches", which are detected while tracking for trend patterns whenever a trended parameter is updated.

#### SLDataGateway: Memory enhancements with regard to average trending [ID_35312]

<!-- MR 10.4.0 - FR 10.3.3 -->

In the SLDataGateway process, a number of memory enhancements have been made with regard to the management of average trend data.

#### SLAnalytics - Behavioral anomaly detection: Suggestion events and alarm events for a DVE child element will now be generated on that same DVE child element [ID_35332]

<!-- MR 10.4.0 - FR 10.3.3 -->

When a behavioral anomaly was detected on a DVE child element, up to now, the suggestion event or the alarm event would be generated on the parent element. From now on, it will be generated on the child element instead.

#### Maps: Markers will now move more gradual when zooming [ID_35337]

<!-- MR 10.4.0 - FR 10.3.3 -->

Because of a number of enhancements, markers will now move more gradual when zooming.

#### Alarm templates - Smart baseline calculations: NullReferenceException prevented & enhanced exception logging [ID_35348]

<!-- MR 10.4.0 - FR 10.3.3 -->

In some cases, a `Baseline Calculation Failed: System.NullReferenceException: Object reference not set to an instance of an object` error would be added to the *SLSmartBaselineManager.txt* log file. The issue causing that error has now been fixed.

Also, log entries indicating an exception thrown during baseline calculations will now include details regarding the element and parameter associated with the exception.

#### Maps: Enhanced zooming behavior [ID_35351]

<!-- MR 10.4.0 - FR 10.3.3 -->

From now on, when you zoom in or out, the data of the previous zoom level will stay visible until the data of the current zoom level has been loaded.

#### DataMiner upgrade: Installation of Microsoft .NET 6.0 [ID_35363]

<!-- MR 10.4.0 - FR 10.3.3 -->

During a DataMiner upgrade, Microsoft .NET 6.0 will now be installed if not installed already.

#### Maps: Zoom range can now be set by means of a slider [ID_35381]

<!-- MR 10.4.0 - FR 10.3.3 -->

The zoom range of a map can now be set by means of a slider.

### Fixes

#### DataMiner Taskbar Utility: Problem when stopping DataMiner [ID_34790]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

Up to now, when you right-clicked the *DataMiner Taskbar Utility* system tray icon and selected *Stop DataMiner* while keeping the SHIFT button pressed, the *SLWatchdog* process would incorrectly also be stopped. In a Failover setup, this would prevent the backup agent from acquiring the virtual IP address.

Also, after DataMiner had been stopped, up to now, the *SLXml*, *SLLog* and *SLDataGateway* processes would incorrectly start up again.

#### Problem with SLLog when logging large entries regarding failed Elasticsearch query requests/responses [ID_35037]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

Up to now, an error could occur in SLLog when adding large entries regarding failed Elasticsearch query requests/responses.

#### When a direct view table was updated, the wrong columns could be updated in the source element [ID_35075]

<!-- MR 10.3.0 - FR 10.3.3 -->

When a direct view table was updated while one of the source elements was stopped, due to a column translation issue, the wrong columns could be updated in that source element the moment it was started again.

#### SLDataGateway would leak memory when offloading average trend data for DVE elements [ID_35167]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

The SLDataGateway process would leak memory when offloading average trend data for DVE elements.

#### Service & Resource Management: Setting a new function file to active would incorrectly not cause the function DVEs of elements using a production version of the protocol to be updated [ID_35178]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

When a new function file was set to active, up to now, the function DVEs of elements using a production version of the protocol in question would incorrectly not be updated.

#### Problem with CassandraBackup.exe when the configuration file of a general database of type 'Cassandra' had a DBServer element that contained multiple host addresses [ID_35253]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

When, in the configuration file of a general database of type "Cassandra", the `<DBServer>` element contained multiple host addresses, up to now, the *CassandraBackup.exe* tool would parse those host addresses incorrectly and would not take any database backup.

From now on, when the `<DBServer>` element contains multiple host addresses including that of the local database, the *CassandraBackup.exe* tool will take a backup of the local database.

#### SLDataGateway could end up with an excessive number of HealthMonitor.Refresh threads [ID_35286]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

In some cases, the SLDataGateway process could end up with an excessive number of *HealthMonitor.Refresh* threads.

#### DataMiner Object Models: Permission checks for DOM modules requiring view permission 'None' were too strict [ID_35305]

<!-- MR 10.3.0 - FR 10.3.3 -->

If a DOM module is created without specifying *SecuritySettings*, the view permission is set to "None".

Up to now, the check to determine whether a user had the view permission set to "None", would only return true for the Administrator or users in the Administrator group. From now on, when the required view permission is "None", permission checks will no longer be performed.

#### External authentication via SAML: Issues fixed when using Okta as identity provider [ID_35374]

<!-- MR 10.4.0 - FR 10.3.3 -->

Using Okta as identity provider, it would incorrectly no longer be possible to read out signed assertions. Also, when the group claim setting is enabled in the *DataMiner.xml* file, the user will now be added to the correct groups.

Up to now, in case of a claim mismatch, an exception would be thrown. From now on, an entry containing a clear message will be added to the *SLNet.txt* log file instead.

#### Problem when connecting to Azure AD via a proxy [ID_35382]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

When a DataMiner Agent connected to Azure Active Directory was sealed off from the internet but had access to a proxy, users would not be able to log in due to SLDataMiner failing to use the proxy to connect to Azure Active Directory.

In case a DataMiner Agent has to connect to Azure AD via a proxy, then that proxy has to be configured by means of the following netsh command:

```txt
netsh winhttp set proxy <proxyaddress> <bypasslist>
```

#### Cassandra: TTL setting of spectrum trace data would not be applied correctly [ID_35385]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

In a Cassandra database, the "time to live" (TTL) setting of spectrum trace data would not be applied correctly. As a result, this type of data would never be removed.

#### SLDataGateway would not correctly return errors when querying SLA logger tables in a Cassandra Cluster [ID_35440]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

SLDataGateway would not correctly return errors when querying SLA logger tables in a Cassandra Cluster, causing an error to occur in SLProtocol.

#### Automation: 'engine.RunClientProgram' overload with two parameters would incorrectly always be run synchronously [ID_35476]

<!-- MR 10.2.0 [CU12] - FR 10.3.3 -->

An `engine.RunClientProgram` overload with two parameters, of which the second one controls whether the method is run either synchronously or asynchronously, would incorrectly always be run synchronously.

```csharp
RunClientProgram(String applicationPath, bool waitForCompletion)
```
