# Overview of Bluesky Projects

This document is maintained by the Bluesky Technical Steering Committee. It
provides an overview of the role of each project and its stage in the
development lifecycle.

## Core Bluesky Projects

These are foundational projects, shared by several facilities.

### Bluesky

>![NOTE]
> The name of this specific package, `bluesky`, became the name by which the
> overall project is known.

**Lifecycle:** Stable, open to new features and bug fixes.

This is the orchestration engine for experiment procedures (Bluesky "plans").

It is compatible with the devices implemented in Ophyd and Ophyd-Async
(including a mixture of both simultaneously). It is also compatible with
third-party libraries that implemented the expected interfaces.

### Ophyd (original)

**Lifecycle:** Stable, open to bug-fixes, unlikely to receive new features.
Expect long-term support. 

Although Ophyd-Async is receiving most of the new development attention, Ophyd
is expected to be maintained and supported for the full lifecycle of the
bluesky project. There are thousands of instances of ophyd devices at multiple
facilities. It would be require a significant effort to convert these, so
NSLS-II is committed to continue to maintain Ophyd even if most new deployments
adopt Ophyd-Async. 

### Ophyd-Async 

**Lifecycle:** Approaching maturity, still undergoing rapid development. Expected
to stabilize and receive long term support. 

This is a separate re-implementation of Ophyd, with a distinct design that
incorporates lessons from a decade of running Ophyd and employs Python language
features that were not available when Ophyd was implemented.

### Databroker 

**Lifecycle:** Stable, planned for deprecation (see below for timelines). It is
succeeded by Tiled with bluesky-tiled-plugins. (The package
bluesky-tiled-plugins is currently developed within the Databroker repository,
but it will soon move into its own repository.) 

Databroker is no longer recommended for new users or facilities adopting
Bluesky. Instead, Tiled is recommended as the canonical way to persist and
access data and metadata from Bluesky. 

Databroker now serves two purposes that remain relevant for some users and some
faciilities. First, it contains code adapting the legacy MongoDB-based Bluesky
document storage to Tiled—effectively a server-side plugin for Tiled. Second,
it wraps the Tiled Python client to provide an API backward-compatible with
legacy Databroker user code. If you do not have MongoDB-based Bluesky storage
and you do not have legacy Databroker user code, you do not need Databroker. 

Databroker will be maintained by NSLS-II through April 2027 at minimum to
support the transition from MongoDB-based document storage to PostgreSQL-based
storage. The Python user interface may be maintained longer still, depending on
the need. 

### Tiled 

**Lifecycle**: Approaching maturity.  Expect to stabilize and receive long-term
support.

This provides an HTTP REST API to saved data and a WebSockets API to streaming
(live) data. It aims to be the canonical way to store and securely access data
from Bluesky.

The project has more general aims beyond Bluesky: unlike its predecessor
(Databroker) it is not designed around the Bluesky document model. Instead, it
addresses data in terms of generic "arrays" and "tables" and nested containers
thereof. In fact, the word `bluesky` does not appear in the codebase. Thus, it
is suitable not only for raw data from Bluesky, but for data from other
acquisition systems, and for processed or analyzed data products as well.

### Event-model 

**Lifecycle** Stable.

This defines the schemas of the Bluesky document model. It provides convenience
functions for manipulating documents and building utilities that consume them. 

## Supporting Bluesky Projects

### Bluesky-tiled-plugins 

**Lifecycle:** Approaching maturity, undergoing rapid development. Expect to
stabilize and receive long term support.

This is a collection of utilities that integrate Tiled with Bluesky.

- The `TiledWriter` consumes Bluesky documents and makes HTTP REST calls to
  save their data and metadata in Tiled.
- Special client objects provide Bluesky-specific convenience methods and
  reprs that display useful Bluesky-specific metadata.
- Custom search queries provide convenient ways to search on standard
  Bluesky metadata, like `TimeRange` and `ScanID`.
- An exporter converts back from Tiled's arrays and tables to Bluesky
  documents, for "replaying" old data as a Bluesky document stream.

### Area-detector-handlers 

**Lifecycle:** Stable, planned for deprecation.

This library provides implementations of the “Handler” interface provided by
Databroker. It will be deprecated with Databroker, superseded by I/O Adapters
in Tiled. 

### Scanspec 

**Lifecycle:** Approaching maturity. Expect to stabilize and receive long term support. 

### Databroker-pack 

**Lifecycle:** Experimental, deprecated, not recommended for use.

Databroker-pack is unmaintained and incompatible with Databroker 2.0. It is not
recommended for use. There is not yet a direct replacement, but this
functionality is planned in Tiled. 

### Suitcase 

**Lifecycle**: Stable, operational but no longer actively developed or
generally recommended.

Description: This is a family of related packages (e.g. suitcase-tiff,
suitcase-csv, suitcase-spec) following the same design pattern. They enable
live-streaming export from Bluesky documents to files. While these packages
continue to work, they are no longer recommended. They are not as user-friendly
or developer-friendly as other approaches: namely, exporting from Tiled, either
in batch or in a streaming mode. 

There is no reason that packages in this pattern cannot be supported. 

Note: The package suitcase-mongo is a special case. It does not export files;
it writes Bluesky documents to MongoDB. It is, in effect, a support library for
Databroker, and it will follow the lifecycle of Databroker. 

### Bluesky-queueserver, bluesky-httpserver, bluesky-queueserver-api 

**Lifecycle:** Experimental, under development.

Refactoring is planned to (i) support websockets for the queue status updates,
(ii) backend for storing queue state, (iii) integrate the HTTP support into RE
Manager, and (iv) redesign the structure to make the queue pluggable. 

### ophyd-websocket / ophyd-as-a-service 

**Lifecycle:** Experimental, early development 

### Finch 

**Lifecycle:** Experimental. Primarily used at ALS for new developments, under
evaluation at NSLS-II. 

This is a library of React components that integrate with Bluesky services,
including Queue Server, Tiled, and the nascent ophyd-websocket /
ophyd-as-a-service. It is expected that facilities will remix these components
to build applications fit to specific requirements. 

### hklpy2 

**Lifecycle:** Experimental, undergoing development, in use at APS, under evaluation at NSLS-II. Replaces hklpy package.

### hklpy 

**Lifecycle:** Maintenance only, no new feature expected to be added. On path to be archived.

### Bluesky-adaptive 

**Lifecycle:** The long-term scope of this project has not yet been defined. 

This is an experimental collection of tools designed to support complex
adaptive plans, including integrating with AI agents.

### Bluesky-widgets 

**Lifecycle:** The future of this project is currently undecided. 

It contains some alpha-quality data visualization and graphical interface
components supporting Qt and Jupyter widgets. These predate Tiled; they are
likely to be abandoned or completely refactored to fetch data from the Tiled
REST and Websocket APIs rather than consuming Bluesky document streams. 

The repository also contains a Qt-based graphical interface to QueueServer.
This will be maintained, but it may be moved into its own repository. 

Cf. (NSLS-II internal): https://github.com/NSLS2/tiled-qt-tools 

### Bluesky-live 

**Lifecycle:** Experimental, on path to be archived. 

Early prototype that informed Tiled design and development. It has significant
performance issues and functionality gaps.   Not recommended for use. 

The core functionality is a stateful version of the consolidator that is now in
bluesky-tiled-plugins. Existing usage of bluesky-live should migrate to that
instead. 

### Bluesky-pods 

**Lifecycle:** Experimental.

The intent is to be able to spin up the entire ecosystem with Podman
pods/containers. The repo will get more developments efforts from the NSLS-II
in the future. 

### Bluesky-kafka 

**Lifecycle:** Deprecated, unsupported. 

This originated at NSLS-II, and NSLS-II is moving away from publishing the
Bluesky document stream to Kafka. Instead, for most use cases, Tiled Websockets
will be used for streaming access to Bluesky data. For “edge” use cases, such
as tight-loop adaptive logic, point-to-point communication (e.g. 0MQ) will be
used as a message bus. 

## Notable External Projects

These projects are not kept in the Bluesky GitHub organization or covered by
Bluesky Governance, but they may still have some multi-facility adoption
and may be interesting to Bluesky users.

### apstools

**Lifecycle:** Stable, open to new features and bug fixes.

[**Source**](https://github.com/BCDA-APS/apstools)

Library of Python tools for use with the Bluesky framework at the APS.  In addition to
ophyd Device classes for mast of EPICS synApps, there are Devices many hardware devices
and software constructs used at APS beam lines.  File writers (RunEngine callbacks) can
write NeXus and SPEC data files.  Used by BITS.

### BITS

**Lifecycle:** Experimental, in use at APS.

[**Source**](https://github.com/BCDA-APS/BITS)

Template for all new deployments of bluesky at APS.

### Guarneri

**Lifecycle:** Experimental, in use at APS.

[**Source**](https://github.com/BCDA-APS/guarneri)

APS package for creating Ophyd and Ophyd-async devices from configuration files.  Used by BITS.

#### happi

...

### ophyd-registry

**Lifecycle:** Experimental, in use at APS.

[**Source**](https://github.com/BCDA-APS/ophyd-registry)

A run-time registry to keep track of, and retrieve, Ophyd objects.  Used by BITS.

### spec2nexus

**Lifecycle:** Stable, open to new features and bug fixes.

[**Source**](https://github.com/prjemian/spec2nexus)

Library for reading SPEC data files in Python.  Cna also write to NeXus data files.  Use by apstools.
