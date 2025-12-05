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

### Event-model 

**Lifecycle** Stable.

This defines the schemas of the Bluesky document model. It provides convenience
functions for manipulating documents and building utilities that consume them. 

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

## Supporting Bluesky Projects

### Area-detector-handlers 

**Lifecycle:** Stable, planned for deprecation.

This library provides implementations of the “Handler” interface provided by
Databroker. It will be deprecated with Databroker, superseded by I/O Adapters
in Tiled. 

### Bluesky-adaptive 

**Lifecycle:** The long-term scope of this project has not yet been defined. 

This is an experimental collection of tools designed to support complex
adaptive plans, including integrating with AI agents.

### Bluesky-kafka 

**Lifecycle:** Deprecated, unsupported. 

This originated at NSLS-II, and NSLS-II is moving away from publishing the
Bluesky document stream to Kafka. Instead, for most use cases, Tiled Websockets
will be used for streaming access to Bluesky data. For “edge” use cases, such
as tight-loop adaptive logic, point-to-point communication (e.g. 0MQ) will be
used as a message bus. 

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

### Bluesky-queueserver, bluesky-httpserver, bluesky-queueserver-api 

**Lifecycle:** Experimental, under development.

Refactoring is planned to (i) support websockets for the queue status updates,
(ii) backend for storing queue state, (iii) integrate the HTTP support into RE
Manager, and (iv) redesign the structure to make the queue pluggable. 

### Bluesky-widgets 

**Lifecycle:** The future of this project is currently undecided. 

It contains some alpha-quality data visualization and graphical interface
components supporting Qt and Jupyter widgets. These predate Tiled; they are
likely to be abandoned or completely refactored to fetch data from the Tiled
REST and Websocket APIs rather than consuming Bluesky document streams. 

The repository also contains a Qt-based graphical interface to QueueServer.
This will be maintained, but it may be moved into its own repository. 

Cf. (NSLS-II internal): https://github.com/NSLS2/tiled-qt-tools 

### Finch 

**Lifecycle:** Experimental. Primarily used at ALS for new developments, under
evaluation at NSLS-II. 

This is a library of React components that integrate with Bluesky services,
including Queue Server, Tiled, and the nascent ophyd-websocket /
ophyd-as-a-service. It is expected that facilities will remix these components
to build applications fit to specific requirements. 

### hklpy 

**Lifecycle:** Maintenance only, no new feature expected to be added. On path to be archived.

### hklpy2 

**Lifecycle:** Experimental, undergoing development, in use at APS, under evaluation at NSLS-II. Replaces hklpy package.

### ophyd-websocket / ophyd-as-a-service 

**Lifecycle:** Experimental, early development 

### Scanspec 

**Lifecycle:** Approaching maturity. Expect to stabilize and receive long term support. 
Allows scan paths to be specified in a serializable, efficient and Pythonic way.
This is used by ophyd-async to enable flyscanning.
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

## Notable External Projects

These projects are not kept in the Bluesky GitHub organization or covered by
Bluesky Governance, but they may still have some multi-facility adoption
and may be interesting to Bluesky users.

### Ophyd as a Service implementation

#### bec device server

**Lifecycle:** In active development and in production use at PSI

BEC, a package developed at PSI, uses Ophyd to create device objects on a device server. Redis is used as a message broker and clients to the device server can instantiate multiple instances of the Ophyd objects which have RPC access. Control and monitoring is also possible remotely through a websockets/REST API

[link](https://gitea.psi.ch/bec/bec/src/branch/main/bec_server)

#### as-ophyd-api

**Lifecycle:** In production use at ANSTO

A similar project to ophyd-websockets. This package creates a REST API and websocket interface to ophyd objects. The primary focus being to give REACT clients the ability to make widgets for device monitoring and control.

[link](https://github.com/AustralianSynchrotron/as-ophyd-api)

#### HZDR version of this

...

### Blop

**Lifecycle:** experimental, in active development

A Python library for performing optimization for beamline experiments. It is designed to integrate nicely with the Bluesky ecosystem and primarily acts as a bridge between optimization routines and fine-grained data acquisition and control. Our goal is to provide a simple and practical data-driven optimization interface for beamline experiments.

[link](https://github.com/NSLS-II/blop)

### apstools

**Lifecycle:** Stable, open to new features and bug fixes.

[**Source**](https://github.com/BCDA-APS/apstools)

Library of Python tools for use with the Bluesky framework at the APS.  In addition to
ophyd Device classes for mast of EPICS synApps, there are Devices many hardware devices
and software constructs used at APS beam lines.  File writers (RunEngine callbacks) can
write NeXus and SPEC data files.  Used by BITS.
#### bluesky_nexus

**Lifecycle:** experimental, in development at a DESY, HZB and NSLS-II

In order to create NeXus structures from bluesky runs, it's important to know the NeXus classes of devices. This package uses HAPPI to  add additional metadata to a bluesky start document about the mapping required from devices to their NeXus base class. It includes callbacks to export .nx files from any bluesky run which contain NXcollections of lists of devices with NeXus compliant classes. These are the basis of export to application definitions. 

[link](https://codebase.helmholtz.cloud/hzb/bluesky/core/source/bluesky_nexus)

### PyMca Tiled Integration

@padraic-shafer 

### bluesky_hooks

**Lifecycle**: experimental

This package creates a convenient way of defining plans that should be run when either 1) a particular motor is moved or 2) a particular device is read. In Bliss, Sardana and Spec these are known as `hooks`. Useful if you need to perform a reference measurement every time you use a detector, or move some motor every time in relation to another.

[link](https://codebase.helmholtz.cloud/hzb/bluesky/core/source/bluesky_hooks)

### command line utlity from SIRIUS that interacts with qserver api

I am not sure if there is a public repo of this

### Nomad Camels

**Lifecycle:** in active development, used by multiple smaller laboratory setups

CAMELS provides a graphical user interface (GUI) that can be used to setup instrument control and measurement protocols. The GUI then generates Python code that interfaces with [bluesky](https://blueskyproject.io/) to communicate with the instruments and orchestrate the measurement. CAMELS can also be used to communicate with large-scale, distributed systems implemented with [EPICS](https://epics-controls.org/).

[link](https://fau-lap.github.io/NOMAD-CAMELS/index.html)

### Collections of devices or plans

Various facilities have common packages with Ophyd devices and plans which are used at multiple beamlines. These are often useful resources to look for existing device or plan implementations.

#### pcdsdevices

[link](https://github.com/pcdshub/pcdsdevices)
@tangkong 

#### nslsii

[link](https://github.com/NSLS-II/nslsii)
@tacaswell 

#### Diamond Light Source

@coretl 

#### bessyii_devices

**Lifecycle:** Production use. Migrating towards ophyd async

A collection of Ophyd.v1 devices used in beamlines at BESSY-II [link](https://codebase.helmholtz.cloud/hzb/bluesky/core/source/bessyii_devices)

### beamline package

Various facilities have found it useful to define the collection of plans and device definitions for a beamline or hutch in a package which can be built and tested seperately. There are different implementations at different facilities. 


#### beamlinetools

**Lifecycle**: in production use at BESSY-II

This package template defines the structure of all bluesky deployments at BESSY-II. There are defined locations for device and plan definition and instantiation. Used in both IPython and Queueserver applications. Lacking tests.

[link](https://codebase.helmholtz.cloud/hzb/bluesky/beamlines_endstations/vanilla_beamline/source/beamlinetools)

#### APS

@prjemian 

#### BITS

**Lifecycle:** Experimental, in use at APS.

[**Source**](https://github.com/BCDA-APS/BITS)

Template for all new deployments of bluesky at APS.

### Guarneri

**Lifecycle:** Experimental, in use at APS.

[**Source**](https://github.com/BCDA-APS/guarneri)

APS package for creating Ophyd and Ophyd-async devices from configuration files.  Used by BITS.

#### happi

**Lifecycle:** Mature, Long-term support

Originating at LCLS, "happi" is a tool that establishes a single interface for storing device metadata, searching that metadata, and creating ophyd device instances using that metadata.  "happi" features a flexible container system that allows for the definition and enforcement of a variety of metadata schema.

While primarily used for ophyd devices, happi can be used to instantiate any python class.

Other centers have similar tools, but happi is the most widely used.

### ophyd-registry

**Lifecycle:** Experimental, in use at APS.

[**Source**](https://github.com/BCDA-APS/ophyd-registry)

A run-time registry to keep track of, and retrieve, Ophyd objects.  Used by BITS.

### spec2nexus

**Lifecycle:** Stable, open to new features and bug fixes.

[**Source**](https://github.com/prjemian/spec2nexus)

Library for reading SPEC data files in Python.  Cna also write to NeXus data files.  Use by apstools.
