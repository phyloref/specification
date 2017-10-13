# Phyloreferences in the Web Ontology Language

In the Web Ontology Language (OWL), phyloreferences are punned as both a class and an individual.

In this document, "node specified by *specifier 1*" means the node identified as the taxonomic unit matched by *specifier 1*.

## Phyloreference

**As an individual,** every phyloreference SHOULD be an instance of `phyloref:Phyloreference`.  It MAY include a label and a description. It MAY include a list of specifiers belonging to it, categorized as internal and external specifiers.

- `rdf:type phyloref:Phyloreference`: indicates that this entity is a phyloreference
- `rdfs:label`: provides a label for this phyloreference.
- `phyloref:hasInternalSpecifier`: indicates an `owl:OWLClass` that is an internal specifier for this phyloreference.
- `phyloref:hasExternalSpecifier`: indicates an `owl:OWLClass` that is an external specifier for this phyloreference.
- `phyloref:hasSpecifier`: indicates an `owl:OWLClass` that is a either an internal or external specifier for this phyloreference.

**As a class,** every phyloreference MUST have an equivalentClass that includes a phyloreference definition. These can be built using the following algorithms.

For **branch-based phyloreferences**, we match the intersection of

- for each internal specifier: the specified node or a node that `has_Descendant` ([CDAO_0000174](http://purl.obolibrary.org/obo/CDAO_0000174)) the specified node, and
- for the one external specifier: a node that has a `phyloref:has_Sibling` relationship with the specified node or one of its descendants through a `has_Descendant` ([CDAO_0000174](http://purl.obolibrary.org/obo/CDAO_0000174)) relationship.

Here is an example branch-based phyloreference with internal specifier *Octoblepharum albidum* and external specifier *Octoblepharum albidum*:


    {
      "@type": "owl:Restriction",
      "onProperty": "phyloref:has_Sibling",
      "someValuesFrom": {
        "@type": "owl:Class",
        "unionOf": {
          "@type": "owl:Class",
          "unionOf": [{
            "@id": "https://doi.org/10.1639/0007-2745%282007%29110%5B46%3APOTCWA%5D2.0.CO%3B2#file1_tree1_phyloref1_specifier3",
            "@type": [ "owl:Class", "tbd:ExternalSpecifier" ],
            "unionOf": [{
              "@type": "owl:Class",
              "intersectionOf": [
                { "@id": "obo:CDAO_0000140" },
                {
                  "@type": "owl:Restriction",
                  "hasValue": "Octoblepharum albidum",
                  "onProperty": "phyloref:identifiedAsTaxon"
                }
              ]
          }]
        }, {
          "@type": "owl:Restriction",
          "onProperty": "obo:CDAO_0000174",
          "someValuesFrom": {
            "@id": "https://doi.org/10.1639/0007-2745%282007%29110%5B46%3APOTCWA%5D2.0.CO%3B2#file1_tree1_phyloref1_specifier3",
            "@type": [ "owl:Class", "tbd:ExternalSpecifier" ],
            "unionOf": [{
              "@type": "owl:Class",
              "intersectionOf": [
                { "@id": "obo:CDAO_0000140" },
                { 
                  "@type": "owl:Restriction",
                  "hasValue": "Octoblepharum albidum",
                  "onProperty": "phyloref:identifiedAsTaxon"
                }
              ]
            }]
          }
        }
      }
    }

For **node-based phyloreferences**, we start with nodes identified to the taxonomic unit matched by the first two internal specifiers and find their most recent common ancestor. We then find the most recent common ancestor between that and the node identified to the taxonomic unit matched by the third internal specifier, and then between that ancestor and the node identified to the taxonomic unit matched by the fourth internal specifier, and so on until all specifiers have been used.

To find the most recent common ancestor of two specifiers (say, *specifier 1* and *specifier 2*), we find the union of:

- the intersection of the node specified by *specifier 1* and a Node that `has_Descendant` ([CDAO_0000174](http://purl.obolibrary.org/obo/CDAO_0000174)) the node specified by *specifier 2*, or
- the intersection of the node specified by *specifier 2* and a Node that `has_Descendant` ([CDAO_0000174](http://purl.obolibrary.org/obo/CDAO_0000174)) the node specified by *specifier 1*, or
- the intersection of:
  - A node that `has_Child` ([CDAO_0000149](http://purl.obolibrary.org/obo/CDAO_0000149)) of the intersection of:
    - Node specified by *specifier 1* or a node that `has_Descendant` ([CDAO_0000174](http://purl.obolibrary.org/obo/CDAO_0000174)) the node specified by *specifier 1*, and
    - a node that has a `phyloref:has_Sibling` relationship with the node specified by *specifier 2* or one of its descendants through a `has_Descendant` ([CDAO_0000174](http://purl.obolibrary.org/obo/CDAO_0000174)) relationship.
  - A Node that `has_Child` ([CDAO_0000149](http://purl.obolibrary.org/obo/CDAO_0000149)) of the intersection of:
    - Node specified by *specifier 2* or a node that `has_Descendant` ([CDAO_0000174](http://purl.obolibrary.org/obo/CDAO_0000174)) the node specified by *specifier 2*, and
    - a node that has a `phyloref:has_Sibling` relationship with the node specified by *specifier 1* or one of its descendants through a `has_Descendant` ([CDAO_0000174](http://purl.obolibrary.org/obo/CDAO_0000174)) relationship.

Here is a Manchester syntax example where the internal specifiers are *Rana tarahumarae* and *Rana sierramadrensis*:


    (
      file2_tree1_phyloref10_specifier1 and (has_Descendant some file2_tree1_phyloref10_specifier2)
    ) or (
      file2_tree1_phyloref10_specifier2 and (has_Descendant some file2_tree1_phyloref10_specifier1)
    ) or (
      (has_Child some (
        (
          file2_tree1_phyloref10_specifier1 or (
            has_Descendant some file2_tree1_phyloref10_specifier1
          )
        ) and (
          has_Sibling some (
            (
              file2_tree1_phyloref10_specifier2 or (has_Descendant some file2_tree1_phyloref10_specifier2))
            )
          )
        )
      ) and (has_Child some (
        (
          file2_tree1_phyloref10_specifier2 or (has_Descendant some file2_tree1_phyloref10_specifier2)
        ) and (
          has_Sibling some (
            (
              file2_tree1_phyloref10_specifier1 or (has_Descendant some file2_tree1_phyloref10_specifier1)
             )
          )
        )
      ))
    )

## Qualifiers

TBD


