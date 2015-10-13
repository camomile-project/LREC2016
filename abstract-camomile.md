# The Camomile collaborative annotation plateform


### Johann Poignant, Mateusz Budnik, Hervé Bredin, Claude Barras, Laurent Besacier, Georges Quénot, Mickael Stefas, Pierrick Bruneau, Thomas Tamisier


## Introduction

Human activity is constantly generating large volumes of heterogeneous data, in particular via the Web. These data can be collected and explored to gain new insights in social sciences, linguistics, economics, behavioural studies as well as artificial intelligence and computer sciences.

In this regard, 3M (multimodal, multimedia, multilingual) data could be seen as a paradigm of sharing an object of study, human data, between many scientific domains. But, to be really useful, these data should be annotated, and available in very large amounts. Annotated data is useful for computer sciences which process human data with statistical-based machine learning methods, but also for social sciences which are more and more using the large corpora available to support new insights, in a way which was not imaginable few years ago.

However, annotating data is costly as it involves a large amount of manual work, and in this regard 3M data, for which we need to annotate different modalities with different levels of abstraction is especially costly. Current annotation framework involves some local manual annotation, with the help sometimes of some automatic tools (mainly pre-segmentation).

We propose a first prototype of collaborative annotation framework on 3M data, in which the manual annotation will be done remotely on many sites, while the final annotation will be localized on the main site. Furthermore, with the same principle, some systems devoted to automatic processing of the modalities (speech, vision) present in the multimedia data will help the manual works, by producing automatic annotations. These automatic annotations are done remotely in each expertise point, which will be then combined locally to produce a meaningful help to the annotators. In order to develop this new annotation concept, we will test it on a practical case study: the problem of person annotation (who is speaking?, who is seen?) in video, which needs collaboration of high level automatic systems dealing with different media (video, speech, audio tracks, OCR, …). The quality of the annotated data will be evaluated through the task of person retrieval.

This new way to envision the annotation process, should lead to some methodologies, tools, instruments and data that are useful for the whole scientific community who have interest in 3M annotated data.


## Camomile REST-API Server

The CAMOMILE CHIST-ERA project aims at developing a prototype of collaborative annotation framework on 3M data, in which the manual annotation is done remotely on many sites. Furthermore, with the same principle, some systems devoted to automatic processing of the modalities (speech, vision) present in the multimedia data help the annotator. A users and groups management as well as the possibility to interact with the server via different clients also help the workflow.

Aiming for a flexible development and use, the collaborative annotation framework is implemented using state of the art web-based technologies, namely libraries and tools developed mostly in Javascript and Python. The ultimate purpose of the platform is to provide on demand overviews and details regarding the 3M data, and the associated automatic and manual annotations. Some views would be dedicated to the fine-grain inspection of annotation files, while some others would serve some higher-level task, such as summarizing the media of a specific corpus according to the exhaustiveness of their associated annotations, or the performance of the algorithms to infer the latter. Multiple users may be involved, synchronously or asynchronously, and with several roles (manual annotators, recognition algorithm developer, adjudicator). Web-based technologies and Javascript already comprise many building blocks suitable for supporting this kind of collaborative behavior. As illustrated in Figure 2, the proposed collaborative annotation framework follows a client/server architecture. This paradigm facilitates the work of multiple users on consistent data sources, as required by the project specifics. The involved server-side technologies rely solely on exchanges via the HTTP protocol, facilitating the design of interoperable software components. The server focuses essentially on data and authentication management tasks, leaving the application logic to the client side. The aim of doing so is to design a general and consistent service, allowing the agile development of browser-based clients, each implemented according to a concrete use-case (e.g., annotation, error analysis, … ).

![REST-API server](figs/archi.png)

**Figure 2: REST-API server**

There are two main reasons that made us implement client/server interfaces using REST services. First, in the REST architecture style, clients and servers exchange representations of resources using a standardized interface and protocol. These principles encourage RESTful applications to be simple, lightweight, and have high performance. Second, RESTful web services typically map the four main HTTP methods to perform predefined operations: GET, PUT, DELETE, and POST. These operations can fulfill the needs of annotating 3M data, such as create, read, update or delete annotations.
Resources are the fundamental concept in any RESTful API, and thus they need to be specified (as collections, tables, relationships between them) before designing REST API services. In our framework, resources are annotations, which are represented in JSON formats, stored in mongodb. Based on the use cases designed in the Camomile project, we specify the following collections (i.e. tables in traditional database systems) for our application: corpus, media, layers, annotations. The corpus collection describes all available corpora. Each corpus contains a set of media and a set of layer. A medium corresponds to a multimedia resource (e.g. a video or audio file). A layer is composed of multiple annotations with the same type (e.g. one layer for manual annotations of speech turns or one layer for annotations of face tracks). An annotation is uniquely defined by a media fragment (e.g. a temporal segment) and attached data (e.g. the name of the current speaker).

![Data model](figs/model.png)

**Figure 3: Data model**

This Figure 3 illustrates an ER diagram of these collections (tables), where: _id is the identifier of a resource; id_xxx is the identifier of the resource xxx; layer_type indicates the type of the layer, for example “speaker ground truth”. fragment_type is a generic type and describes the type of the fragments, which are annotation units stored in the annotation collection. Fragment_type can currently be segments or rectangles (for face recognition). data_type is the data type of each annotated fragment, and it can be a name of a speaker or a spoken person. The range of supported types is designed to be easily extended to potentially new annotation tasks. History is a list of modifiers and each medium can be accessed via an url. ACL correspond to the user or group rights (read, write, admin) on the resource (the media inherits the rights of the corpus to which it belongs, the annotations inherits the rights of the layers to which it belongs). Group and user are defined by a unique name and a description, a group contains a list of users and the user role can be a simple "user" or "admin" of the server. Finally, a last table is dedicated to queues which correspond to a list of elements. Queues have also a management of users and groups rights.

A documentation with all the routes available on this server can be found at 
[http://camomile-project.github.io/camomile-server](http://camomile-project.github.io/camomile-server)

The source code of the camomile server can be found at
[https://github.com/camomile-project/camomile-server](https://github.com/camomile-project/camomile-server)

## Active learning use case

The above framework can be easily extended with additional functionality to make the annotation process more efficient. To that end, an active learning use case is presented, which can be useful when the full annotation of the whole dataset is impossible due to cost or time constraints. Active learning is a set of algorithms that help with selecting  potentially informative samples for human annotation, while trying to avoid those that are redundant.

In this scenario, the active learning (AL) system is based on the propagation of labels with the use of hierarchical clustering. Ideally, each cluster should correspond to a single person. Normally, the goal is to give a label (be it manually or automatically) to every segment in the dataset and purest clusters as possible.

Automatically generated segments of a multimedia resource are used as queries. These segments are initially clustered using a distance measure dependent on the media (e.g. distance between HOG descriptors for face segments).  Afterwards, a cluster is selected for annotation based on a specific criterion (e.g. size of the cluster or how dense and well separated it is) depending on the annotation scenario. Next, a representative segment from the selected cluster is presented for the user to label. Initially, it is the one closest to the centroid of a cluster, i.e. having the minimal distance to all other segments within it. Once a cluster contains at least one annotated segment, outliers are selected, which are most likely to contain information belonging to a different person.

After a segment is annotated by the user the cluster structure is modified accordingly. For example, two clusters that contain segments with the same name are merged. On the other hand, a cluster which contains segments with different labels is divided. A label assigned to a segment within a cluster is propagated to all of its unlabeled members. 

![AL diagram](figs/AL_diagram.png)

**Figure 4: Active learning system**

The AL system is connected to the collaborative annotation framework via the use of two different types of queues. The interaction between these two systems is shown in Figure 4. An input queue is filled with annotations, i.e. media fragments (see Figure 3), which are suggested by the AL algorithm for annotation. The task for the human annotator can be either identification, if the data field is empty (no name given to the fragment), or verification when a label is already assigned to the media fragment. The latter case can be used to verify the quality of automatically propagated labels or previous annotations done by humans. The most recent label given to a fragment is stored in the data field, while all the previous ones are added to the history list. This mechanism can be used for data cleaning and conflict resolution. From the input queue the annotations are distributed among the human annotators. Once a media fragment is assigned to a user, it is removed from the queue. 

The annotations processed by the users are pushed to the output queue. Because the media fragments are automatically generated, some may be noisy or may not contain any information related to the task (speech segments with only music or silence for example). They can be skipped by the user and will be stored separately and not be used by the AL system. The named annotations are removed from the output queue and used in the next step of the system. 



