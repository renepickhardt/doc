# Ciation Matching - Literature Overview

## Systms

* [CiteSeerX](http://citeseerx.ist.psu.edu/index)
* [Arnetminer](http://arnetminer.org/)
* [Researchgate](http://researchgate.net/)

## Authors

* [Lee Giles](http://clgiles.ist.psu.edu/new_stuff.shtml)
* Wrappen und Wrapper Learning.  A "wrapper" is a piece of software
  that allows to extract certain fields from structured documents like
  HTML documents.
  * [Georg Gottlob](http://www.informatik.uni-trier.de/~ley/pers/hd/g/Gottlob:Georg)
  * [Craig Knoblock](http://www.informatik.uni-trier.de/~ley/pers/hd/k/Knoblock:Craig_A=)
  * [Kushmerick](http://www.informatik.uni-trier.de/~ley/pers/hd/k/Kushmerick:Nicholas)

# Reviews

## [Lee Giles - Autonomous Citation Matching](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.26.3920&rep=rep1&type=pdf)

Aim: Recognize different forms of citations to the same paper. 

Example: The following three citations all refer to the same papers.

* Rosenblatt, F. (1961). Principles of Neurodynamics: Preceptrons and
  the theory of brain mechanisms. Spartan Books. Washington D.C.
* F. Rosenblatt (1962)
* F. Rosenblatt. Principles of Neurodynamics. 1962

I.e.: Giles et. al. attack the poblem of *reference clustering*. They
refer to the "Open Journal Project" for matching citations against
known bibliographic data.

### Approaches

1. Edit distance.
2. Word frequency and word occurrence (tf0idf)
3. Subfields or struncture. (Title, year of publication, author)
4. Probabilistic model to identify subfields.
5. Baseline: Count number of matching words

The cited paper investigates algorithms from the first three classes.

### Used Normalizations

- conversion to lower case
- conversion of hyphens to spcaes
- removal of citation tags (e.g. '[3]')
- expansion of common abbreviations (e.g. 'conf.', 'proc.')
- removal of extraneous words (e.g. 'accepted for publication')

### Results

"An algorithm based on word and phrase matching was found to perform
best"

"An algorithm based on extracting the subfields of the citations
produced relatively poor accuracy, but has the advantage of being very
efficient."


## [Open Journal Project - Citation Linking](http://dl.acm.org/citation.cfm?id=263804)

Titile: Citation Linking: Improving Access to Online Journals
Authors: Hichcock, et. al.


It introduces/uses a novel "distributed linking service"
[DLS](http://www.w3.org/Conferences/WWW4/Papers/178/) that should
automatically annotate a given articles (web ressource) with
hyperlinks. These links comprise, but are note limited to citations of
other articles.

Overview about earlier linking services, such as 'forward reference'
indices.

Cultural aspects of scientific publishing with the rise of the
internet are discussed.

The paper is very old and does not go into details about the technical
implementation of the DLS.

## Fedoryszak - Methodolgy for evaluating citation parsing and matching

Citation Matching can be separated in two tasks:

1. Cluster bibliographic references (Reference Clustering)
2. Link them with referenced documents (Citation Matching)

Instances of more general problems in databases:

1. Duplicate Detection [Duplicate record detection: A survey](https://www.cs.purdue.edu/homes/ake/pub/survey2.pdf)
2. Record Linkage [Christen](http://www.sundaychennai.com/IEEE%202012%20Dotnet%20Basepaper/A%20Survey%20of%20Indexing%20Techniques%20for%20Scalable%20Record%20Linkage%20and%20Deduplication.pdf)

The citation matching task can be further subdivided into:

1. _Segmentation_, the deconstruction of reference strings into functional pieces such as 'author names', 'title', ect.
2. _Entity resolution_, the clustering of records for the same document.

But several authors depart from such a division.

Two training datasets available for citation matching:

1. Lawrence, Giles: 'CiteSeer set'
2. McCallum: '[Cora set](https://people.cs.umass.edu/~mccallum/data.html)' 

The paper propses a method of automated training set geneeration, and
a wider set of evaluation metrics.

As training set metadata from 1400 randomly selected articles from the
Spanish Digital Mathematics Library was used. The citation data was
artificially created by filling in the meta-data into bibtex and
rendering them in different formats.

Based on this dataset an inhouse citation matcher 'YADDA2' was
evaluated. Some details and references about the implementation were
given. However, I was not able to find the software on the net.

## [Day, Tsai, et. al. - A knowlesdge-based approach to citation extraction]()

The paper is about extracting citations from documents not about the
matching problem.

Many interesing references are given.


# Glossary

* Reference. The textual reference inside an article to another
article.
  Typically these references are found in the section "References" in
a paper.
* Citaion. 'A citation is a performative act linking two entities,
  taking the simple form "EntityA cites EntityB"' c.f. [DS2013]

# References

[DS2013] David Shotton - Metadata Model for the Open Citations Corpus,
2013
