# Phyloreferences and phylogenetic clade definitions

Here is a rough mapping of Cantino *et al* 2007’s terms with ours:

- **Standard node-based definitions**: All internal specifiers are extant — in fact, "The unstated internal specifiers are all extant species (or organisms) that share either a particular apomorphy or a particular relationship (closer to A than to Z) with the one stated specifier A. Thus, contrary to Sereno (2005), these are variants of the node-based, not the branch-based (stem-based), definition type."
- **Branch-modified/stem-modified node-based definition**:  
  - Branch-modified node-based definitions take the form "the most inclusive crown clade containing A but not Z" (or Y or X, etc., as needed) or "the clade stemming from the most recent common ancestor of A and all extant organisms or species that share a more recent common ancestor with A than with Z" (or Y or X, etc., as needed). 
- **Apomorphy-modified node-based definition**: Apomorphy-modified node-based definitions take the form "the most inclusive crown clade exhibiting character M synapomorphic with that in A" (where A is a species or specimen) or "the clade stemming from the most recent common ancestor of A and all extant organisms or species that possess apomorphy M as inherited by A."
- **Total clades**: For total clades, we have used a special kind of branch-based definition (PhyloCode Art. 10.5): "the total clade composed of the crown clade X and all extinct organisms or species that share a more recent common ancestor with X than with any other mutually exclusive (non-nested) crown clade." An abbreviated form of this definition, which we used in our Nomenclatural Treatment, is "the total clade of X", where X is the name of a crown clade. In this definition, the specifiers are only indirectly identified; the internal specifier(s) are those of crown clade X, and the external specifiers are the internal specifiers of all other crown clades that lie outside of X (though in practice, one would only be concerned about the sister crown clade to X in the accepted phylogeny).
  - **Question.** Are we thinking of crown-clades using other crown-clades as mutually exclusive clades? Because we totally can. But in a bifurcating tree, this is implied anyway — is there any way we can automatically find those exclusive clades from a branch-based phyloreference?
- **Least inclusive clade:** node-based definition containing internal specifiers as well as a number of external qualifiers
- **Most inclusive clade**: branch-based definition containing either multiple internal specifiers or one internal specifier with multiple internal qualifiers AND an external specifiers and multiple external qualifiers
  - Not quite the same thing, is it?


- General note: we can’t actually model "The most inclusive crown clade containing Zea mays L. 1753 (/Spermatophyta) but not Phaeoceros laevis (L.) Prosk. 1951 (Anthocerotophyta) or Marchantia polymorpha L. 1753 (Marchantiophyta) or Polytrichum commune Hedw. 1801 (Bryophyta)." just yet." — that’s a "most inclusive clade" with a single internal qualifier!
  - We can’t model "The total clade of /Tracheophyta." yet either 😕 
  - How do we test this, since we don’t have species in this tree?
    - Go straight to Open Tree? But then how do we test this?
  - We don’t have a way to model one-internal-multiple-externals either. How would we find the "exclusionary" case? Can we treat the first one as a specifier and the rest as qualifiers?

Here is a rough mapping of Phylocode 5’s terms with ours:

- Minimum-clade definition = node-based definition
  - Two or more internal specifiers
- Directly-specified ancestor definition = node-based definition
  - "The clade originating in A", where ‘A’ is an internal specifier.
- Maximum-clade definition = branch-based or stem-based definition
  - Largest clade that contains one or more internal specifiers but does not contain **one or more external specifiers**
- Apomorphy-based definition = apomorphy-based definition
  - Specified apomorphy that was inherited by one or more internal specifiers
- **Crown-clade definition:**
  - Crown-clade definition for minimum clade = minimum-clade definition where all the internal specifiers are extant
    - May be used for a directly-specified ancestor to specify a single species
  - Crown-clade definition for maximum clade = maximum-clade definition where:
    1. at least one internal specifier is extant, and
      1. the word "extant" is included before "organisms" under the first wording, OR
      2. the word "crown" is included before "clade" under the third wording
  - Maximum-crown-clade definition = branch-modified or a stem-modified node-based definition
  - Minimum and maximum clades can be modified to define a crown clade, but apomorphy-based definitions should not be used to define a crown clade, as that requires certainty that the defining apomorphy and originated in the same ancestor.
- **Total clades:**
  - Don’t use minimum-clades: you can’t sure that internal specifiers represent the earliest divergence!
  - 

