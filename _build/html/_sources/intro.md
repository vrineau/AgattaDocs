<img src="https://github.com/vrineau/agatta/blob/main/agatta/agatta.png?raw=true" alt="agatta_logotype" width="700"/>

# Introduction to Agatta

Agatta is a python package for three-item analysis (Nelson and Platnick, 1991) and other rooted tree applications. Three-item analysis can be considered as a supertree method (Kitching et al., 1998). The input trees are decomposed into triplets, then coded using triplet matrix representation. Finally, a tree is estimated on the matrix using a congruence maximisation criterion. Agatta implements several triplet weighting schemes, and is technically close to the Weighted Minimum Triplet Inconsistency problem (Byrka et al. 2010), a triplet equivalent of the Weighted Minimum Quartet Inconsistency problem (Lafond and Scornavacca, 2019).

Three-item analysis was originally conceived under the cladistic theory, and can be used for reconstructing the evolutionary history of various entities. The characters (input trees) can be morphological characters coded hierarchicaly (Cao et al., 2008), rooted gene trees (with or without duplication events) or any type of evolutionary hypotheses that can be represented as rooted trees. Three-item analysis is also used in vicariance biogeography (Morrone, 2008), where phylogenies are used as input trees to build areagrams representing relationships between biogeographical areas.

Agatta is user-friendly and offers many specific features to help pre or postprocessing rooted trees. Such features include decomposing trees with duplicated tips into duplication-free subtrees, consensus methods, several triplet-based support metrics, precise tree description, character state test and mapping, and file reformating. 

## Installation and requirements

Agatta works on Windows, Unix/Linux and macOS.

This package requires Python version 3.8 or higher.  It has been tested for 3.8, 3.9, 3.10, and 3.11 using ubuntu 20.04, macos 13, and windows 11.

Agatta and all its dependencies can be installed using `pip install Agatta` through the shell. More explanations for installing Agatta are available in the [tutorial](intro.md).

The package implements a pipeline for efficient heuristic searches that requires the installation of WQFM (https://github.com/Mahim1997/wQFM-2020), TNT (http://www.lillo.org.ar/phylogeny/tnt/), wTREE-QMC (https://github.com/molloy-lab/TREE-QMC), or a PAUP* command-line binary (http://phylosolutions.com/paup-test).

## Documentation and Support

A complete tutorial and documentation is [available on the documentation website](intro.md).

The official repository is on [GitHub](https://github.com/vrineau/Agatta). Agatta is published on [Pypi](https://pypi.org/project/Agatta/).

An help for all commands is available directly using `agatta help`.

## Bug report

Contact valentin.rineau@sorbonne-universite.fr or post on [Agatta issues page](https://github.com/vrineau/Agatta/issues).

## License

The code is currently licensed under the GNU General Public License version 3.

## Citation

When using Agatta, please cite us (temporarily) as

> Rineau, V., Zaharias, P. (2025) Agatta (Version 0.9.0) [Software]. Github. https://github.com/vrineau/Agatta.

## References

* Byrka, J., Guillemot, S., & Jansson, J. (2010). New results on optimizing rooted triplets consistency. *Discrete Applied Mathematics*, 158(11), 1136-1147.
* Cao, N., Zaragüeta i Bagils, R., & Vignes-Lebbe, R. (2007). Hierarchical representation of hypotheses of homology. *Geodiversitas*, 29(1), 5-15.
* Kitching, I. J., Forey, P., Humphries, C., & Williams, D. (1998). *Cladistics: the theory and practice of parsimony analysis.* Oxford University Press.
* Lafond, M., & Scornavacca, C. (2019). On the weighted quartet consensus problem. *Theoretical Computer Science*, 769, 1-17.
* Morrone, J. J. (2008). *Evolutionary biogeography: an integrative approach with case studies*. Columbia University Press.
* Nelson, G., & Platnick, N. I. (1991). Three‐taxon statements: a more precise use of parsimony? *Cladistics*, 7(4), 351-366.
* Rineau, V., Zaragüeta i Bagils, R., & Bardin, J. (2021). Information content of trees: three-taxon statements, inference rules and dependency. *Biological Journal of the Linnean Society*, 133(4), 1152-1170.

*Graphic chart by Malcolm T Sanders*
