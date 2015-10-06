
# The Camomile Framework: a toolkit to manage workflow for annotation and evaluation campaign

### Johann Poignant, Hervé Bredin, Claude Barras, … 

<br/>


Human activity is constantly generating large volumes of heterogeneous data, in particular via the Web. These data can be collected and explored to gain new insights in social sciences, linguistics, economics, behavioural studies as well as artificial intelligence and computer sciences. 3M (multimodal, multimedia, multilingual) data could be seen as a paradigm of sharing an object of study, human data, between many scientific domains. In this regard, evaluation campaigns give a surrounding to evaluate new algorithms that exploit these data. 

A classical workflow for an evaluation campaign can be divided in 5 steps (see Figure 1). First, participants register to a task, therefore users and groups of users should be managed. Secondly, data are release to participants, then, participants process data with their algorithms and submit hypotheses. In parallel, manual annotations are done  with the helps of the participant submissions or not. These annotations can also be used to train or tune algorithms. Finally, the evaluation is performed with the possibility to show the performances obtained via a leaderboard during the campaign.

![REST-API server](figs/workflow.png =550x)

**Figure 1: A classical workflow for evalaution campaign**

However, annotating data is costly as it involves a large amount of manual work, and in this regard 3M data, for which we need to annotate different modalities with different levels of abstraction is especially costly. These annotations can also be done on different site with different tools, which complicates the merger of annotations.

A second issue is the information transfer between organizer, participants and annotators, like after the submission, a human intervention is generally required to evaluate this run and to return the result to the participant, while it can be automatized. This manual work makes impossible a comprehensive and fast overview of the state of the campaign (number of submission of versioning of the submission, annotations status, evolution of the scores …).


## Camomile REST-API Server

The CAMOMILE CHIST-ERA project aims at developing a prototype of collaborative annotation framework on 3M data, in which the manual annotation is done remotely on many sites. Furthermore, with the same principle, some systems devoted to automatic processing of the modalities (speech, vision) present in the multimedia data help the annotator. A users and groups management as well as the possibility to interact with the server via different clients also help the workflow.

Aiming for a flexible development and use, the collaborative annotation framework is implemented using state of the art web-based technologies, namely libraries and tools developed mostly in Javascript and Python. The ultimate purpose of the platform is to provide on demand overviews and details regarding the 3M data, and the associated automatic and manual annotations. Some views would be dedicated to the fine-grain inspection of annotation files, while some others would serve some higher-level task, such as summarizing the media of a specific corpus according to the exhaustiveness of their associated annotations, or the performance of the algorithms to infer the latter. Multiple users may be involved, synchronously or asynchronously, and with several roles (manual annotators, recognition algorithm developer, adjudicator). Web-based technologies and Javascript already comprise many building blocks suitable for supporting this kind of collaborative behavior. As illustrated in Figure 2, the proposed collaborative annotation framework follows a client/server architecture. This paradigm facilitates the work of multiple users on consistent data sources, as required by the project specifics. The involved server-side technologies rely solely on exchanges via the HTTP protocol, facilitating the design of interoperable software components. The server focuses essentially on data and authentication management tasks, leaving the application logic to the client side. The aim of doing so is to design a general and consistent service, allowing the agile development of browser-based clients, each implemented according to a concrete use-case (e.g., annotation, error analysis, … ).

![REST-API server](figs/archi.png =500x)

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


## Camomile at MediaEval 2015: Person Dicovery in Broadcast TV task

The camomile server has been tested in real conditions with success during the MediaEval 2015 benchmark for the Person Dicovery in Broadcast TV task. This task try to response to an important issue of TV archivist: How to ensure that archives are exploitable. The need for applications that make these archives searchable has led researchers to devote concerted effort to developing technologies that create indexes. Indexes that represent the location and identity of people in the archive are indispensable for searching archives. Human nature leads people to be very interested in other people. However, when the content is created or broadcast, it is not always possible to predict which people will be the most important to find in the future. For this reason, it is not possible to assume that biometric models will always be available at indexing time. For some people, such a model may not be available in advance, simply because they are not (yet) famous. In such cases, it is also possible that archivists annotating content by hand do not even know the name of the person. The goal of this task is to address the challenge of indexing people in the archive, under real-world conditions (i.e. when there is no pre-set list of people to index).

Participants were provided with a collection of TV broadcast recordings pre-segmented into shots. Each shot had to be automatically tagged with the names of people both speaking and appearing at the same time during the shot. The main novelty of the task is that the list of persons was not provided a priori, and person biometric models (neither voice nor face) could not be trained on external data. The only way to identify a person was by finding their name in the audio (e.g. using speech transcription -- ASR) or visual (e.g. using optical character recognition -- OCR) streams and associating them to the correct person. This made the task completely unsupervised (i.e. using algorithms not relying on pre-existing labels or biometric models). To ensure that participants followed this strict ``no biometric supervision'' constraint, each hypothesized name had to be backed up by a carefully selected and unique shot prooving that the person actually holds this name: we call this an evidence (e.g. a shot where a person is visible and its name is written on screen). In real-world conditions, this evidence would help a human annotator double-check the automatically-generated index, even for people they did not know beforehand.

For further details about the task, dataset and metrics the reader can refer to the task description [1].

Around the serveur, we have developed python scripts and web interfaces to adapt the workflow for this task (see figure 4).

![workflow for Person Discovery task](figs/Camomile_at_MediaEvalv2.png =350x)

**Figure 4: workflow for Person Discovery task**

After the participant registration, we used a web interface to create user accounts and groups for each team. Then, participants used the submission script to submit or update their runs. This script, in addition to check the correctness of the submission, creates a new layer and sends the hypotheses (as annotations in the data model, Figure 3) to the camomile server. 

For this task, we based the manual annotation on participant submissions. A robot has read all hypotheses on the server and has filled a queue with annotations to do. Two web interfaces were used successively to produce annotations. The first one was used to check the correctness of evidences, for the correct ones, we asked the annotator to extract a mugshot. The second interface asked, for a particular shot, if an hypothesis (via its mugshot) is a speaking face. A monitoring interface allowed us to follow the proportion of annotations already done.

A last robot has evaluated the runs on a subpart of annotations already done. The scores obtained have been displayed in a leaderboard updated every 6 hours. This information has helped participants to tune their algorithms. 



## References

[1] J. Poignant, H. Bredin, and C. Barras. Multimodal Person Discovery in Broadcast TV at MediaEval 2015. In MEDIAEVAL, 2015.


