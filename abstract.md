# Abstract



# Introduction

Human activity is constantly generating large volumes of heterogeneous data, in particular via the Web. These data can be collected and explored to gain new insights in social sciences, linguistics, economics, behavioural studies as well as artificial intelligence and computer sciences. In this regard, 3M (multimodal, multimedia, multilingual) data could be seen as a paradigm of sharing an object of study, human data, between many scientific domains. But, to be really useful, these data should be annotated, and available in very large amounts. Annotated data is useful for computer sciences which process human data with statistical-based machine learning methods, but also for social sciences which are more and more using the large corpora available to support new insights, in a way which was not imaginable few years ago. However, annotating data is costly as it involves a large amount of manual work, and in this regard 3M data, for which we need to annotate different modalities with different levels of abstraction is especially costly. 

The CAMOMILE CHIST-ERA project aims at developing a prototype of collaborative annotation framework on 3M data, in which the manual annotation will be done remotely on many sites. Furthermore, with the same principle, some systems devoted to automatic processing of the modalities (speech, vision) present in the multimedia data will help the annotator. 

After this short introduction, we present the collaborative annotation framework and then how it has been used during the MediaEval 2015 benchmark for the Person Discovery in Broadcast TV task. Finally we conclude and give some development perspectives.



# Collaborative Annotation Framework: camomile REST-API Server

Aiming for a flexible development and use, the collaborative annotation framework is implemented using state of the art web-based technologies, namely libraries and tools developed mostly in Javascript and Python. The ultimate purpose of the platform is to provide on demand overviews and details regarding the 3M data, and the associated automatic and manual annotations. Some views would be dedicated to the fine-grain inspection of annotation files, while some others would serve some higher-level task, such as summarizing the media of a specific corpus according to the exhaustiveness of their associated annotations, or the performance of the algorithms to infer the latter. Multiple users may be involved, synchronously or asynchronously, and with several roles (manual annotators, recognition algorithm developer, adjudicator). Web-based technologies and Javascript already comprise many building blocks suitable for supporting this kind of collaborative behavior. As illustrated in Figure 1, the proposed collaborative annotation framework follows a client/server architecture. This paradigm facilitates the work of multiple users on consistent data sources, as required by the project specifics. The involved server-side technologies rely solely on exchanges via the HTTP protocol, facilitating the design of interoperable software components. The server, described to a greater extent in the next section, focuses essentially on data and authentication management tasks, leaving the application logic to the client side. The aim of doing so is to design a general and consistent service, allowing the agile development of browser-based clients, each implemented according to a concrete use-case (e.g., annotation, error analysis, … ).

![REST-API server](figs/archi.png)

**Figure 1: REST-API server**

There are two main reasons that made us implement client/server interfaces using REST services. First, in the REST architecture style, clients and servers exchange representations of resources using a standardized interface and protocol. These principles encourage RESTful applications to be simple, lightweight, and have high performance. Second, RESTful web services typically map the four main HTTP methods to perform predefined operations: GET, PUT, DELETE, and POST. These operations can fulfill the needs of annotating 3M data, such as create, read, update or delete annotations.
Resources are the fundamental concept in any RESTful API, and thus they need to be specified (as collections, tables, relationships between them) before designing REST API services. In our framework, resources are annotations, which are represented in JSON formats, stored in mongodb. Based on the use cases designed in the Camomile project, we specify the following collections (i.e. tables in traditional database systems) for our application: corpus, media, layers, annotations. The corpus collection describes all available corpora. Each corpus contains a set of media and a set of layer. A medium corresponds to a multimedia resource (e.g. a video or audio file). A layer is composed of multiple annotations with the same type (e.g. one layer for manual annotations of speech turns or one layer for annotations of face tracks). An annotation is uniquely defined by a media fragment (e.g. a temporal segment) and attached data (e.g. the name of the current speaker).

![Data model](figs/model.png)

**Figure 2: Data model**

This figure illustrates an ER diagram of these collections (tables), where: _id is the identifier of a resource; id_xxx is the identifier of the resource xxx; layer_type indicates the type of the layer, for example “speaker ground truth”. fragment_type is a generic type and describes the type of the fragments, which are annotation units stored in the annotation collection. Fragment_type can currently be segments or rectangles (for face recognition). data_type is the data type of each annotated fragment, and it can be a name of a speaker or a spoken person. The range of supported types is designed to be easily extended to potentially new annotation tasks. Source stores all information about the layer, including created_date, the path to its location on the server, etc. ACL correspond to the user or group rights on the resource (the media inherits the rights of the corpus to which it belongs, the annotations inherits the rights of the layers to which it belongs). Finally, history is a list of modifiers. To allow the 3M data stored in the above collections to be manipulated, each resource needs to have its own unique URL, used for method (i.e. operation) parametrization.

A documentation with all the routes available on this server can be found at 
[http://camomile-project.github.io/camomile-server](http://camomile-project.github.io/camomile-server)

The source code of the camomile server can be found at
[https://github.com/camomile-project/camomile-server](https://github.com/camomile-project/camomile-server)



# Camomile at MediaEval 2015: Person Dicovery in Broadcast TV

In this section we present how the camomile server has been tested in real conditions with success during the MediaEval 2015 benchmark for the Person Dicovery in Broadcast TV task.

### Motivation of the task

TV archives maintained by national institutions such as the French INA, the Netherlands Institute for Sound \& Vision, or the British Broadcasting Corporation are rapidly growing in size. The need for applications that make these archives searchable has led researchers to devote concerted effort to developing technologies that create indexes.

Indexes that represent the location and identity of people in the archive are indispensable for searching archives. Human nature leads people to be very interested in other people. However, when the content is created or broadcast, it is not always possible to predict which people will be the most important to find in the future.

For this reason, it is not possible to assume that biometric models will always be available at indexing time. For some people, such a model may not be available in advance, simply because they are not (yet) famous. In such cases, it is also possible that archivists annotating content by hand do not even know the name of the person. The goal of this task is to address the challenge of indexing people in the archive, under real-world conditions (i.e. when there is no pre-set list of people to index).

### definition of the task

Participants were provided with a collection of TV broadcast recordings pre-segmented into shots.
Each shot had to be automatically tagged with the names of people both speaking and appearing at the same time during the shot. The main novelty of the task is that the list of persons was not provided a priori, and person biometric models (neither voice nor face) could not be trained on external data. The only way to identify a person was by finding their name in the audio (e.g. using speech transcription -- ASR) or visual (e.g. using optical character recognition -- OCR) streams and associating them to the correct person. This made the task completely unsupervised (i.e. using algorithms not relying on pre-existing labels or biometric models).

Because person names were detected and transcribed automatically, they could contain transcription errors to a certain extent. 

![evidence definition](figs/evidence_def.png =700x)

**Figure 3: definition of evidence**

To ensure that participants followed this strict ``no biometric supervision'' constraint, each hypothesized name had to be backed up by a carefully selected and unique shot prooving that the person actually holds this name: we call this an evidence. In real-world conditions, this evidence would help a human annotator double-check the automatically-generated index, even for people they did not know beforehand.

Two types of evidence were allowed: an image evidence is a shot during which a person is visible, and their name is written on screen; an audio evidence is a shot during which a person is visible, and their name is pronounced at least once during the shot with 5 seconds of tolerance around the shot.

For instance in the Figure 3, shot \#1 is an image evidence for Mr A (because his name and his face are visible simultaneously on screen) while shot \#3 is an audio evidence for Mrs B (because her name is pronounced less than 5 seconds before or after her face is visble on screen).

For further details about the task, dataset and metrics the reader can refer to the task description [1].




### Annotations attendues
![Annotation process](figs/annotation_process.png =550x)

Annotations a posteriori based on participants submissions

- Evidence
- Label


### Processus de gestion des soumissions

figure du processus simplifié

### Client d'annotations


![Annotation client of the evidences](figs/evidence_ui.png =350x)

**Figure 4: Annotation interface for evidences**




![Annotation client of the labels](figs/label_ui.png =350x)

**Figure 5: Annotation interface for labels**

### stat sur les annotations:

- nombre d'annotation
- nombre d'annotateur
- temps moyen
- couverture du corpus



# Conclusion & future works

Improve queue


# References

[1] J. Poignant, H. Bredin, and C. Barras. Multimodal Person Discovery in Broadcast TV at MediaEval 2015. In MEDIAEVAL, 2015.


