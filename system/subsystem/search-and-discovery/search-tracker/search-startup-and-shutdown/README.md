# Startup and Shutdown Lifecycle
For a **CoreContainer** there is **one instance** of each of the following:
* **AlfrescoCoreAdminHandler** - The main `CoreAdminHandler` which also acts as reference point for the SolrTrackerScheduler, TrackerRegistry, informationServers.
* **SolrTrackerScheduler** - A decorator of the Quartz Scheduler, jobs are scheduled by core name.
* **TrackerRegistry**- Trackers are registered in the TrackerRegistry by core name.

`AlfrescoCoreAdminHandler` has `startup()` and `shutdown()` methods to manage the lifecycle of these services.


For a **Core** there is **one instance** of each of the following:
* **SolrInformationServer** - An Alfresco specific implementation of an `InformationServer`.  Additionally it provides locking for trackers, aswell as access to `TrackerState` and `TrackerStats`.
* **SOLRAPIClient** - Talks to an Alfresco repository.
* **SolrContentStore** - A content store specific to SOLR's requirements
* **AclTracker, ContentTracker, MetadataTracker, CascadeTracker, CommitTracker** - the trackers.

Startup and registration of these services is done via `SolrCoreLoadListener` and `SolrCoreLoadRegistration`. The `SolrCoreLoadListener` listens for the first searcher to start for a core, it then calls `SolrCoreLoadRegistration.registerForCore`.  Shutdown is handled by `SolrCoreCloseHook`.

It is possible to have 2 instances of a core running with the same name, eg. on `reload()` a new core is created then the old core is shutdown.

## Model Tracker
The `Model Tracker` is a special tracker to track Alfresco Models.  There should be one model tracker.  It currently is started by the very first core to run.

