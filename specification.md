# Phyloreference specification

# Introduction

The goal of this specification is to provide a working vocabulary for storing and sharing machine-interpretable phylogenetic clade definitions called "phyloreferences". It defines what a phyloreference is as an information artifact, and the minimum information necessary to find nodes in a phylogeny that satisfy a phyloreference. It provides canonical examples of phyloreferences that maximize their human readability while being efficiently processable and interpretable by computers. It also explains what a phyloreference represents, how it relates to phylogenetic clade definitions and to clades. We also provide an implementation of this specification in the Web Ontology Language (OWL).

# Definitions

## Document-specific definitions

The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119). To summarize:

- MUST/MUST NOT/REQUIRED: Absolute requirements. For example, a definition that does not meet all requirements at this level cannot be termed a phyloreference.
- SHALL/SHALL NOT/SHOULD/SHOULD NOT/RECOMMENDED: These requirements may only be ignored after carefully considering the consequences. For example, a definition that does not meet all requirements at this level is still a phyloreference, but may no longer be useful or meaningful.
- MAY/OPTIONAL: An implementation generating phyloreferences may safely ignore these parts. An implementation consuming phyloreferences MUST be prepared to read and process phyloreferences that include these properties, even if it does not act on that information.

## General definitions of information artifacts

- **Phylogeny:** A connected, [directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph) of Nodes linked by *has_Child* relationships.
  - See also: [`evoinfo:Phylogenetic Tree`](https://evoinfo.nescent.org/Concept_Glossary#Phylogenetic_Tree) , [`evoinfo:Network`](https://evoinfo.nescent.org/Concept_Glossary#Network).
  
- **Node:** An entity that is linked to other Nodes through *has_Child* relationships. A Node may be tied to a Taxonomic Unit through a *has_TU* relationship.
  - See also: [`cdao:Node`](http://purl.obolibrary.org/obo/CDAO_0000140).

- **has_Child:** A directed relationship between two Nodes such that one Node is the direct descendant ("**child**") of the other.
  - See also: [`cdao:has_Child`](http://purl.obolibrary.org/obo/CDAO_0000149).

- **Terminal Node**: A Node that has no children.
  - Also known as **leaf node** or **tip**.
  -  See also: [`evoinfo:Terminal Node`](https://evoinfo.nescent.org/Concept_Glossary#Terminal_Node).

- **Ancestor:** A Node that has one or more Descendants. Ancestors and Descendants are linked through direct or indirect *has_Child* relationships.
  - See also: [`evoinfo:Ancestor`](https://evoinfo.nescent.org/Concept_Glossary#Ancestor).


- **Descendant:** A Node that is has one or more Ancestors. Ancestors and Descendants are linked through direct or indirect *has_Child* relationships.
  - See also: [`evoinfo:Descendant`](https://evoinfo.nescent.org/Concept_Glossary#Descendant).

- **Clade:** A Node and all of its Descendants. May consist of a single Node if it has no Descendants.
  - See also: [`evoinfo:Clade`](https://evoinfo.nescent.org/Concept_Glossary#Clade).

- **Taxonomic Unit:** A set of one or more biological samples. Each sample might have unique identifiers or properties that can be used to match Specifiers.
  - According to CDAO, this is a "unit of analysis that may be tied to a node in a tree and to a row in a character matrix. It subsumes the traditional concepts of 'OTU' and 'HTU'". 
  - The EvoInfo Glossary defines it as the "entities from which [`evoinfo:Character-States`](https://evoinfo.nescent.org/Concept_Glossary#Character-State) are observed and taken as ground truths. In some cases the [`evoinfo:OTU`](https://evoinfo.nescent.org/Concept_Glossary#Operational_Taxonomic_Unit) may be a composite of data drawn from several sources. Note that the use of "taxon" for both an `evoinfo:OTU` and for a class in [`evoinfo:Organismal Taxonomy`](https://evoinfo.nescent.org/Concept_Glossary#Organismal_Taxonomy) is a cause of confusion". 
  - See also: [`cdao:TU`](http://purl.obolibrary.org/obo/CDAO_0000138), [`evoinfo:Operational Taxonomic Unit`](https://evoinfo.nescent.org/Concept_Glossary#Operational_Taxonomic_Unit).

- **has_TU:** A directed relationship between a Node and a Taxonomic Unit.

- **Specifiers:** Specifiers contain information that can be used to match a Taxonomic Unit.

## Project-specific definitions
- **Phyloreference:** A definition constructed so that it can never **resolve to** more than one Node in a single Phylogeny. It denotes a Clade consisting of that Node and all of its descendants. 
  - A phyloreference may have **internal specifiers**, indicating taxonomic units that must be included in the specified clade, or **external specifiers**, indicating taxonomic units that must be excluded from the specified clade.
  - A **qualifier** is a specifier that is neither an internal specifier nor an external specifier. Once a phyloreference has matched to a node, this resolution can be tested to ensure that descendants of the clade include all **internal qualifiers** and exclude all **external qualifiers**. Qualifiers that do not match are ignored, but qualifiers that match incorrectly indicate that the phyloreference has failed to resolve.
    - Note that qualifiers can be implemented in terms of specifiers, so an external qualifier can be implemented as an external specifier. However, there is currently no way of specifying a node-based phyloreference with an external specifier — adding that external specifier automatically converts it into a branch-based definition. Qualifiers can either be implemented as a post-resolution test or as a specifier within explicitly typed phyloreferences, but one of these approaches will need to be chosen.
  - When a phyloreference is applied to a Phylogeny, it may **match** zero or one Nodes on that phylogeny. If it matches exactly one Node, and if the clade descending from that node includes all internal qualifiers and excludes all external qualifiers, the phyloreference is said to **resolve** on that phylogeny. 

# Phyloreference model

Any definition that matches exactly zero or one node on any possible phylogeny meets the definition of a phyloreference. To allow for maximal flexibility, we do not put any constraints on how such a logical definition is constructed, stored or executed. We define certain classes of phyloreferences and provide an OWL-based definition of phyloreferences later in this document, but these do not limit the broadest possible definition of the term "phyloreference". We anticipate new classes of phyloreferences will be discovered that cannot be expressed in OWL or that require custom software resolvers to be written. These will remain "phyloreferences" even if they do not fit any of the patterns defined in this document.

In order to allow phyloreferences to be resolved, however, we do need to place some constraints on the entities they may match. To this end, we require that:

1. Phyloreferences MUST match zero or one nodes in any phylogeny consisting of one or more nodes connected by *has_Child* relationships. The phylogeny MAY have a root node that is the ancestor of every other node in the phylogeny. The phylogeny MUST be connected, directed and acyclic, so:
  1. Every node MUST be connected to every other node through a *has_Child* or analogous relationship. *has_Child* relationships MUST be directed and MUST NOT be directed at themselves, i.e. a node cannot be its own child.
  2. *has_Child* relationships MUST NOT form a cycle, i.e. no node can be its own ancestor or its own descendant.
2. Nodes MUST BE unique to a particular phylogeny, and should not be shared between multiple phylogenies. Matching a node on one phylogeny has no effect on nodes in any other phylogeny.
3. Nodes should be matched via specifiers that provide a specimen, taxon or taxon concept that might be shared by nodes in any phylogeny.

# Matching specifiers

Specifiers match taxonomic units. How exactly this matching is carried out is beyond the scope of this specification. Both specifiers (in phyloreferences) and taxonomic units (in phylogenies) should be specified as precisely as possible, and software attempting to match specifiers can use any information in specifiers, taxonomic units or any other data sources to facilitate the match. For example, a Specifier may be matched to a Taxonomic Unit because they have identical taxonomic identifiers, such as [NCBI Taxonomy ID](https://www.ncbi.nlm.nih.gov/books/NBK21106/def-item/app179/), [ITIS TSNs](https://www.itis.gov/pdf/faq_itis_tsn.pdf) or the scientific name as a string. A more sophisticated matcher might match taxa that are part of the indicated genus, may look up the current taxonomic identifier of a vouchered specimen record, may use precisely ontologised feature or whether the Taxonomic Unit has a particular apomorphy.

For the purposes of reproducibility, software that match specifiers SHOULD record the logic and data used to make that match.

# Phyloreference patterns

Any definition that must resolve to exactly zero or one node on any phylogeny meets the definition of a phyloreference. The definition space of phyloreferences is therefore vast, and we hope that new patterns for defining phyloreferences will be developed and tested in the future. However, we anticipate that most users of phyloreferences would prefer to build definitions based on patterns that are known to have this necessary property rather than attempting to construct phyloreferences from scratch. We focus on three such patterns developed and widely used in the phylogenetic clade definition literature.

## Node-based phyloreferences

A node-based phyloreference matches a node that is the least inclusive ancestor of two or more **included specifiers**. It denotes a clade containing the most recent common ancestor of the included taxonomic units and all of its descendants.

A node-based phyloreference consists of:

1. One or more included specifiers.
2. Any number of included qualifiers.
3. Any number of excluded qualifiers.

## Branch-based phyloreferences

A branch-based phyloreference matches a node that is the most inclusive ancestor of any number of **included specifiers** that is not the ancestor of one **excluded specifiers**. It denotes a clade that includes all the ancestors of the included descendants, excluding those of the excluded descendants, and all of their descendants.

A branch-based phyloreference consists of:

1. One or more included specifier.
2. Any number of included qualifiers.
3. Any number of excluded qualifiers.

## Apomorphy-based phyloreferences

An apomorphy-based phyloreference matches a node that is an ancestor of one **included specifier** and that possesses an **apomorphy** that is homologous to the **included specifier**. It denotes the clade of nodes that share an apomorphy with the included descendant and all their descendants.

An apomorphy-based phyloreference consists of:

1. One included specifier.
2. An apomorphic character possessed by the included specifier.

This phyloreference will never match a node that does not have apomorphy character.

For phylogenies that do not include character-state information, this type of phyloreference will never resolve. For phylogenies that do not include character-state information in taxonomic units associated with ancestral nodes, this type of phyloreference will always match the included descendant.

# Useful quotes
- “The primary determinant of definition type is the author’s conceptualization of the clade.” — [Cantino](http://www.ingentaconnect.com/content/iapt/tax/2007/00000056/00000003/art00020) [*et al*](http://www.ingentaconnect.com/content/iapt/tax/2007/00000056/00000003/art00020)[, 2007](http://www.ingentaconnect.com/content/iapt/tax/2007/00000056/00000003/art00020)

# Relevant reading:
  1. https://evoinfo.nescent.org/Concept_Glossary
  2. https://www.evoio.org/wiki/Phyloreferencing_subgroup
  3. https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2747124/
  4. Examples of other ontology specifications:
    1. https://www.w3.org/TR/annotation-model/
    2. https://www.w3.org/TR/annotation-protocol/
    3. https://www.w3.org/TR/2013/REC-prov-dm-20130430/

