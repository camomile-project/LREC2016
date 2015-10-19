# Multimedia technology benchmark made easy with the CAMOMILE platform. Feedback from the MediaEval 2015 Multimodal Person Discovery task.

# Workflow management for a multimodal technology evaluation involving collaborative annotation

### Johann Poignant<sup>1</sup>, Hervé Bredin<sup>1</sup>, Claude Barras<sup>1</sup>, Mickael Stefas<sup>2</sup>, Pierrick Bruneau<sup>2</sup>, Thomas Tamisier<sup>2</sup>

1. LIMSI, CNRS, Univ. Paris-Sud, Université Paris-Saclay, F-91405 Orsay
2. LIST, Esch-sur-Alzette, Luxembourg


## Introduction

For decades, NIST evaluation campaigns have been driving research in the field
of human language technology [1], recently followed by the CLEF [X], ESTER [X]
and ETAPE [X] initiatives. The concept has been successfully transposed to
other research areas, such as image recognition (ImageNet Large Scale Visual
Recognition Challenge [X]), video (TRECVID [X]) or multimedia indexing
(MediaEval [X]). More generally, evaluation campaigns allow the assessment of
experimental research in fields where human perception and decision must be
reproduced by machine learning algorithms [2].
<!-- on large databases of annotated representative samples. -->
<!-- ref are missing -->


The general workflow of *à la NIST* evaluation campaigns comprises the
following stages [1]: specification of the task; definition of the evaluation
metric and provision of an automatic scoring software; design and annotation of
the training, development and evaluation corpora; definition of evaluation
rules, schedule, protocols and submission formats; sharing of participant
results through system descriptions and workshop communications.

Automatic scoring is made possible by the manual annotation of the data
according to the task definition. Costly and time-consuming, this annotation
step usually is the main bottleneck of evaluation campaigns. When addressing
new tasks in multimodal perception, it becomes challenging (if not impossible)
to pre-annotate the ever-increasing volume of multimedia data. A compromise
has been successfully explored in the TREC and TRECVid campaigns, where
the annotation of a small (but carefully chosen [5]) subset of the test data is
bootstrapped by the participants' submissions.
<!-- However, this adds a dependency between the different phases of the evaluation and makes the general process more complex (???). -->

In this paper, we claim that the CAMOMILE collaborative annotation platform
(developed in the framework of the eponymous CHIST-ERA project) eases the
organization of multimedia technology benchmarks, automating most of the
campaign technical workflow and enabling collaborative (hence faster and
cheaper) annotation of the evaluation data. This is demonstrated through the
successful organization of a new multimedia task at MediaEval 2015, Multimodal
Person Discovery in Broadcast TV [3].

## MediaEval 2015: Multimodal Person Discovery in Broadcast TV

The objective of this new task is to make TV archives fully exploitable and
searchable through people indexing. Participants were provided with a
collection of TV broadcast recordings pre-segmented into shots. Each shot had
to be automatically tagged with the names of people both speaking and appearing
at the same time during the shot.

Since one cannot assume that biometric models of persons of interest are
available at indexing time, the main novelty of the task was that the list of
persons was not provided a priori. Biometric models (either voice or face)
could not be trained on external data. The only way to identify a person was by
finding their name in the audio (using speech transcription - ASR) or visual
(using optical character recognition - OCR) streams and associating them to the
correct person -- making the task completely unsupervised with respect to prior
biometric models.

To ensure that participants followed this strict ``no biometric supervision''
constraint, each hypothesized name had to be backed up by an`` evidence'': a
unique and carefully selected shot prooving that the person actually holds this
name (e.g. a shot showing a text overlay introducing the person by their name).
In real-world conditions, this evidence would help a human annotator
double-check the automatically-generated index, even for people they did not
know beforehand.

Participants were provided with a fully functional baseline system, allowing
them to only focus on some aspects of the task (e.g. speaker diarization) while
still being able to rely on the baseline modules for the other ones (e.g.
optical character recognition). The task was evaluated as a standard
information retrieval task using a metric derived from mean average precision.
Eight teams managed to reach the submission deadline, amounting to a total of
70 submitted runs. For further details about the task, dataset and metrics, the
interested reader can refer to [3].

## CAMOMILE + Person Discovery = <3

The CAMOMILE platform was initially developed for supporting collaborative
annotation of multimodal, multilingual and multimedia data [4]. The data model
was kept intentionally simple and generic, with four types of resources:
corpus, medium, layer and annotation.

A corpus is a set of media (e.g. the
evaluation corpus made of all test videos). An annotation is defined by a
fragment of a medium (e.g. a shot) with an attached metadata (e.g. the name
of the current speaker). Finally, a layer is an homogeneous set of annotations,
sharing the same fragment type and the same metadata type (e.g. a complete run
submitted by one participant). All these resources are accessible through a
RESTful API (clients in Python and Javascript are readily available), with user
authentication and permission management.

A generic queueing mechanism is also
available on the CAMOMILE backend as a means to control the workflow. The
CAMOMILE platform is distributed as open-source software at the following
address: <http://github.com/camomile-project/camomile-server>.

### Automating the benchmarking workflow

The upper part of Figure 1 depicts the technical workflow of the proposed
evaluation campaign.

![REST-API server](figs/workflow.png)

*Figure 1 - Workflow automation with the CAMOMILE platform*

The lower parts of Figure 1 summarize how we relied on the CAMOMILE platform
and its Python and Javascript clients to automate most steps.

We developed Python scripts and web interfaces to implement
the workflow of the task (see the lower part of the figure 1).

**Registration.** After the task was advertised through the MediaEval call for
participation, we relied on MediaEval standard registration procedure (i.e.
filling an online form and signing dataset usage agreements) to gather the list
of participating teams. Through a web interface, users and groups management
features of the CAMOMILE platform were used to create one group per team and
one user account for each team member.

**Distribution.**  Due to technical (limited internet bandwith) or copyright
concerns (datasets distributed by third parties), the development and
evaluation datasets were not distributed through the CAMOMILE platform.
Instead, ELDA and INA took care of sending the datasets to the participants.
Nevertheless, corresponding corpora (for the development and the test sets)
and layers (for each video) were created as CAMOMILE resources with READ
permissions for each team.

**Submission.**  While the standard MediaEval submission procedure is to ask
participating teams to upload their runs into a shared online directory, we
chose to distribute to all participants a submission management tool, based on
the CAMOMILE Python client. This command line tool would automatically check
the format of the submission files, authenticate users with their CAMOMILE
credentials and creates a new layer (and associated annotations) for each
submission, with read/write permissions to (and only to) every team member.

**Evaluation.** Finally, another Python script would score submitted runs on a subset of the annotations. The scores obtained were displayed in a leaderboard updated every 6 hours. This information allowed the participants to tune their algorithms during the development phase of the campaign.


For other task organisation, some of these modules can be re-used as they are (users and groups management) or adapted (annotation interfaces, monitoring of annotations, leaderboard)
The collaborative annotation is specific, though.

### Collaborative annotation

As already noted, this process implies numerous information transfers between the evaluation organizer, participants and annotators. If a human intervention is needed to process the submission of a participant and return the scoring, it will prevent a comprehensive and fast overview of the state of the campaign (number of submissions and their versioning, annotations status, evolution of the scores …). The annotation has also to be performed on different sites in a collaborative way. The coordination of the annotations and the scoring of the different submissions could be largely automated, relying on the CAMOMILE framework, specific annotation interfaces and a set of background services which are all distributed in open source
([https://github.com/camomile-project/camomile-server](https://github.com/camomile-project/camomile-server)]).

For this evaluation, the manual annotation relied on the participant submissions. Regularly, a Python script would fetch all the hypotheses stored on the server and filled a queue with the annotations to do. Two web interfaces were used successively to produce the annotations. The first one was used to check the correctness of hypothesized evidences. When correct, the annotator was asked to draw a bounding box around the face to generate a mugshot later used as a basis for comparison (to overcome the language dependencies for the rest of the annotation process). The first annotation step for evidences was performed by three annotators with around 7337 annotations done (see Table 1). Audio evidences have taken longer en average as we need to listen the whole shot plus 10 seconds around while for image we just have to find the image where the name is written on screen (TODO: define image vs. audio evidence).

<!-- merge audio and image evidence in the table/text ? -->

In the second interface, we asked, for a particular shot, if an hypothesis (via its mugshot) is a speaking face (i.e. a person simultaneously visible and speaking during the shot). Due to the size of the corpus we asked to the participants to help us for these annotations. 20 persons participated for 66089 shots annotated with a median duration of 4.4 seconds. A monitoring interface allowed us to follow the proportion of annotations already done.

|                                              | image evidences | audio evidences |   Label   |
|----------------------------------------------|:---------------:|:---------------:|:---------:|
| # annotator                                  |          3      |         3       |      20   |
| # annotation                                 |       4908      |      2429       |   66089   |
| median duration of the annotation in seconds |        6.6      |      17.6       |     4.4   |

**Table 1: number of annotation and median duration for the two web interface**

For this second annotation step we asked a minimum of 2 annotations per shot and a consensus between annotator to validate the annotation. 28231 shots have been annotated at least once, while 98.7% of them a consensus between annotator was reached and 1.3% not (this proportion should be reduced if we had enough time to add new annotations).

|                     |      # shots   |
|---------------------|:--------------:|
| with at least 2 ann |  28231 (100%)  |
| with a consensus    |  27873 (98.7%) |
| without a consensus |    358 (1.3%)  |

**Table 2: number of shots annotated at least two time**

In the next table we detailed the number of annotations per shot that was done to find the consensus. 15.3% of them required a third annotation to find the consensus, 2.4% required 4 annotations and 0.6% required more than 4 annotations. Which shows that in more than 5000 shots either the annotator was wrong or he was not agreement with the others. Number of cases revealed that the definition of a face speaking is sometimes ambiguous (singer, person doubled, difficulty understanding speech).

| # annotations / shot |     # shot    |
|----------------------|:-------------:|
| 2                    | 22770 (81.7%) |
| 3                    |  4257 (15.3%) |
| 4                    |   658 (2.4%)  |
| >4                   |   188 (0.6%)  |

**table 3: number of annotations per shot with a consensus**

## Conclusions and perspectives

The management of the evaluation campaign described in this paper, including the development of the Python scripts and web interfaces specific to the campaign, was performed within a 6-month period by roughly 2 full-time person. It was made possible by taking advantage of the distributed annotation server with access control along with other inteThe server resisted the load. All the script and interfaces related to this campaign are publicly available. Even if they were designed specifically for the chosen task, we believe that a significant part of the approach is generic and can be ported to a different task involving manual and automatic annotation of audio-visual corpora.

<!-- for the "6-month period by roughly 2 full-time person", I think is less that this, Hervé also worked on the server and I worked on the baseline -->

## Acknowledgements

This work was supported by the French National Agency for Research under grant ANR-12-CHRI-0006-01 (CAMOMILE project). We thank ELDA and INA for supporting the task with development and test datasets.

## References

[1] A. F. Martin, J. S. Garofolo, J. G. Fiscus, A. N. Le, D. S. Pallett, M. A. Przybocki, et G. A. Sanders, NIST Language Technology Evaluation Cookbook. In LREC, 2004.

[2] E. Geoffrois, An Economic View on Human Language Technology Evaluation. In LREC, 2008.

[3] J. Poignant, H. Bredin, and C. Barras. Multimodal Person Discovery in Broadcast TV at MediaEval 2015. In MEDIAEVAL, 2015.

[4] J. Poignant et al. The CAMOMILE collaborative annotation framework. Submitted to LREC 2016.

[5] E. Yilmaz, and J. A. Aslam. Estimating Average Precision with Incomplete and Imperfect Judgments. In Proceedings of the 15th ACM International Conference on Information and Knowledge Management.
