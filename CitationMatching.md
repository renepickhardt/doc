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

1. _Segmentation_, the deconstruction of reference strings into
   functional pieces such as 'author names', 'title', ect.
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

## [Day, Tsai, et. al. - A knowlesdge-based approach to citation extraction](http://dns.csie.cyut.edu.tw/~shwu/publication/IRI2005.pdf)

The corresponing presentation is also available ([ppt](http://www.iis.sinica.edu.tw/~myday/slides/Slide2005_IEEE-IRI2005_A_Knowledge-based_Approach_to_Citation_Extraction.ppt))

The paper propses a method for reference segmentation, that uses
ontologies and the INFOMAP knowledge representation tool.

Many interesing references are given.

## [Pasula, et. al. - Identity Uncertainty in Citation Matching](http://www.cs.berkeley.edu/~milch/papers/nipsnewer.pdf)

The paper deals with the problem of reference clustring.

Example:
Citeseer lists more than 100 distict AI textbooks by Russell and
Norving from around 1995.

Very nice related work section:

* Record Linkage in databases
  - Greedy (Fellinki and Sunter - A theory of record linkage, 1969)
  - Global Probabilistic (Cohen et. al. - Hardening soft information sources 2000)
* Data Association: e.g. in 3d spacial data
* Reference Clustering ('ciation grouping')
  - Lawrence, Giles - Autonomous citation matching (Citeseer), 1999
  - McCallumn - Efficient clustering of high dimensional data, 2000

New global method for reference clustering on the basis of "Relational
Probabilistic Models". Complicated mathematics relying on prior theory
by Koeller.

Training Datasets:

* 1990 Census Data on US Names
* AI Bibliographic data (Bibtext) gathered by the authods
* Hand selected 500 'citations' i.e. references

Not clear: Is the task of reference segmentation also attacked? Yes?

Evaluation on the citeseer dataset: Much better results.

## [McCallum et. al. - Efficient Clustering of High-Dimensional Data Sets](http://www.kamalnigam.com/papers/canopy-kdd00.pdf)

The paper considers the problem of clustering reference strings.
The authors propse a two step process:

1. Pre-cluster the data set into overlapping "Cannopies" using a cheap
   approximate metric (Cos distance of tf-idf word vectors)
2. Fine-grained clustering of the individual Cannopies using a
   customized cost-adjusted, permutation-adjusted edit distance
   between citations.

The methods is evaluated on the 'cora set' and yields dramatic speed
improvements when comared to the naive approach, while yielding
neutral (slightly better) results.

Uses Hidden Markov Models from Cora / Whizbang paper (McCallum,
et. al. - Automating the construction of internet portals).

## [Cohen et. al. - Hardening Soft Information Sources](courses.cs.washington.edu/courses/cse590q/04au/papers/Cohen00.pdf‎)

The paper considers the problem of reference clustering.

Basic idea is that reference data comprises a 'soft database' where
the same entity might have many ids ('Bart Seleman' = 'B. Seleman'),
but underlying there is a 'hard database' which unique ids, which was
distorted by distorted in some sense.

A formal process or hardening a database is introduced, which has
the nice feature of being global, hence unique.

The optimal hardening problem is shown to be NP-hard, and a greedy
hardening algorithm is presented, which is nearly linear in time and
converges to a local optimum.

Key concept is an acyclic-interpretation relation: `I` which relates
distinct references which are likely to be edits of one another `I:
r_1 --> r_2` and assigns a cost to them. The formal hardening maps
each reference to its initial interpretation.


# Glossary

* *Reference* or *reference string*. The textual reference inside an
  article to another article.  Typically these references are found in
  the section "References" in a paper.
* *Reference segmentation*. The deconstruction of reference strings
   into functional pieces such as 'author names', 'title', ect.
* *Reference clustering*. The process of grouping reference strings
  that correspond to the same article.
* *Citaion*. 'A citation is a performative act linking two entities,
  taking the simple form "EntityA cites EntityB"' c.f. [DS2013]



# References

[DS2013] David Shotton - Metadata Model for the Open Citations Corpus,
2013
