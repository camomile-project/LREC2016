
# The Camomile Framework: a toolkit to manage workflow for annotation and evaluation campaign

### Johann Poignant, Hervé Bredin, Claude Barras, … 

Evaluation has been a driving force for research in the field of human language technology for decades, thanks to the efforts of NIST [1] followed by eg. the CLEF initiative or campaigns like ESTER and ETAPE. The concept has been successfully transposed to the image processing and more generally to the multimodal communities with the CLEAR, TRECVID, REPERE or MediaEval campaigns, to cite a few significant ones. More generally, it is fitted to the assessment of experimental research in fields where the human perception and decision is to be reproduced through machine learning on large data bases of annotated representative samples [2]. 

The general methodology described by NIST is the following [1]: clear specification of the task; definition of an evaluation metric and public diffusion of an automatic scoring software; design of the training, development and evaluation corpora; definition of evaluation rules, schedule, protocols and submission formats; and finally sharing of participant results through system descriptions and workshop communications.
The automatic scoring is made possible thanks to a manual annotation of the data according to the task definition, which is the most time consuming and thus costly and limiting factor of the evaluation campaigns.
When addressing new goals in multimodal perception, we face the challenge of an ever increasing volume of data which can not be extensively annotated in advance. A promising solution has been explored in the TRECVID campaigns, where the annotation is performed after the submission of the participants, according to the results of the systems and can be limited to the annotation of a representative sample of the data. However, this adds a dependency between the different phases of the evaluation and makes the general process more complex.

In the context of the CHIST-ERA CAMOMILE project on collaborative annotation of multi-modal, multi-lingual and multi-media documents, we addressed the problem of multimodal person recognition and organized a task on Multimodal Person Discovery in Broadcast TV at MediaEval 2015 [3]. The workflow of the evaluation campaign is classical and can be divided in 5 steps (see Figure 1). First, participants register to the task, raising the need for users and groups management. Secondly, data are released to participants, then, participants process the data with their algorithms and submit hypotheses. In parallel, manual annotations are done, possibly taking into account the participant submissions. These annotations can also in turn be used to train or tune the algorithms. Finally, the scoring is performed with the possibility to show the performance evolution during the development phase via a leaderboard.

![REST-API server](figs/workflow.png)

**Figure 1: A classical workflow for an evaluation campaign**

As already noted, this process implies numerous information transfers between the evaluation organizer, participants and annotators. If a human intervention is needed to process the submission of a participant and return the scoring, it will prevent a comprehensive and fast overview of the state of the campaign (number of submissions and their versioning, annotations status, evolution of the scores …). The annotation has also to be performed on different sites in a collaborative way. The coordination of the annotations and the scoring of the different submissions could be largely automated, relying on the CAMOMILE framework, specific annotation interfaces and a set of background services which are all distributed in open source.

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

[1] A. F. Martin, J. S. Garofolo, J. G. Fiscus, A. N. Le, D. S. Pallett, M. A. Przybocki, et G. A. Sanders, NIST Language Technology Evaluation Cookbook. In LREC, 2004.

[2] E. Geoffrois, An Economic View on Human Language Technology Evaluation. In LREC, 2008.

[3] J. Poignant, H. Bredin, and C. Barras. Multimodal Person Discovery in Broadcast TV at MediaEval 2015. In MEDIAEVAL, 2015.

[4] J. Poignant et al. The CAMOMILE collaborative annotation framework. Submitted to LREC 2016. 

