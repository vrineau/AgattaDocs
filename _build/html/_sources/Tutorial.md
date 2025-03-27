# Tutorial

Agatta is a three-item analysis python 3 program for estimating an optimal tree given a set of input rooted trees. Agatta can be used for all types of phylogenetic analysis: in morphology, with characters coded as hierarchies, in gene supertree analysis, or in total evidence with a mix of morphological characters and gene trees. The software can also be used for cladistic biogeography analysis where an areagram is obtained by congruence of phylogenies, and can be used whenever the goal is to congruence several rooted trees in order to obtain one or several optimal trees.

[comment]: <> (More detailed documentation is available in **GitHub wiki**. Further information and benchmarks can be found in **paper**.)

For any questions, issues, or bug report, please use the [issues github page](https://github.com/vrineau/Agatta/issues). Please subscribe to the mailing list for updates.

## Before you start

Refer to readme in addition to this tutorial. 

Before starting this tutorial, please download all [datasets](https://github.com/vrineau/Agatta/tree/main/data).

The tutorial requires the installation of the following programs:
* PAUP* (part 'command-line binaries' for Linux et MacOS): https://paup.phylosolutions.com/get-paup/
* Python 3.8 to 3.11: https://www.python.org/downloads/. Beginner tip: make sure that python can be called directly from the command prompt, so that you don't have to write the whole path from the python executable every time you want to run a command. This can be made by ticking the box '[Add Python 3 to PATH](https://miro.medium.com/max/1344/0*7nOyowsPsGI19pZT.png)' when installing Python 3 for the first time. If the 'pip' and 'python' commands doesn't work, reinstall python and tick the checkbox.

### Installation

Agatta currently has no GUI. You need to run it through command-line.

Open a terminal. Once the terminal opens, do

```
pip install Agatta
```

Python 3.7 or higher must be installed prior to Agatta (if pip is not recognised as a valid command, you may need to add Python to environment variables; an option is to rerun the Python installer and check the box `Add Python 3.6 to PATH`). To check that installation has worked properly, do

```
agatta --version
```

which should display current version number.

## Help

To get help, do
```
agatta help
```
displays the main Agatta commands:

```
Usage:
    agatta analysis         <file>        [options]
    agatta tripdec          <file>        [options]
    agatta convert          <file>        [options]
    agatta fp               <file>        [options]
    agatta consensus        <file>        [options]
    agatta describetree     <file>        [options]
    agatta hmatrix          <file>        [options]
    agatta support          <file> <file> [options]
    agatta chartest         <file> <file> [options]
    agatta standardisation  <file> <file> [options]
    agatta help             <command>
    agatta -h | --help
    agatta --version
```

For a specific command, for example if you want more information about `analysis`, do

```
agatta help analysis
```

## Example 1: three-taxon analysis

We will next run Agatta on several input datasets. Commands line must be run in the same order as in this tutorial. 

From the`data` directory, run

```
agatta analysis song_gen.txt --software=paup --prefix=rslt_song --softpath=/path/to/paup/paup4a168_ubuntu64
```

The `--softpath` flag is useless for windows users who have installed PAUP*.

This test file (source: [ASTRAL github page](https://github.com/smirarab/ASTRAL/tree/master/main/test_data)) is based on the Song et. al. dataset for molecular analysis of mammal species. 

The analysis command performs a three-item analysis on the set of trees stored in the input file. The trees are decomposed into triplets and then an algorithm will search for the optimal tree(s) (the trees in agreement with the maximum of triplets) which are stored in `rslt_song.tre` (the name `rslt_song` is set by the `prefix` flag above). All trees resulting from analyses with Agatta are written in newick format. [Figtree](http://tree.bio.ed.ac.uk/software/figtree/) is an example of software allowing to visualize trees in newick format (it works under Linux, Windows, and Mac).

On the same dataset, the `tripdec` command performs only the first part of `analysis` (tree decomposition into triplets)

```
agatta tripdec song_gen.txt --prefix=triplist_song
```

A `.triplet` file is systematically generated which gives the list of triples and their associated weight.

The convert command requires a .triplet file and converts it, in this case to a PAUP* matrix (flag `--software`).

```
agatta convert triplist_song.triplet --taxarep1=triplist_song.taxabloc --filetype=triplets --prefix=tmrp_song --software=paup
```

## Example 2: interpretation of the results


A hierarchical matrix can be used as input file instead of newick trees. We will use the matrix from Gill and Leis (2019) designed for three-taxon analysis of fishes. The `hmatrix` command converts the matrix into a list of corresponding rooted trees

```
agatta hmatrix gill_morpho.hmatrix --prefix=hmatrix_gill
```

A complete analysis from a hierarchical matrix can be performed in the same way as with newick files 

```
agatta analysis gill_morpho.hmatrix --prefix=rslt_gill --software=paup --softpath=/path/to/paup/paup4a168_ubuntu64 --analysis=heuristic
```

The following command gives various information about one or more rooted trees in newick format

```
agatta describetree rslt_gill.tre
```

The `support` command with the `--index=ri` flag gives the global and per-character retention index. The results are displayed at the command prompt and are also saved in the `rslt_gill.ri` file.

```
agatta support rslt_gill.tre rslt_gill.input.tre --index=ri --prefix=rslt_gill
```

Testing character states in 3ia as described by Cao (2008) and modified by Rineau (2017) can be performed using `chartest`
```
agatta chartest rslt_gill.tre rslt_gill.input.tre --prefix=chartest_gill
```
The previous results are written in two files. However, reading them may appear difficult. The `--pdf` flag allows the visualisation of the character test results.

```
agatta chartest rslt_gill.tre rslt_gill.input.tre --prefix=chartest_gill --pdf
```
Below is an example with character 1, state 1 (#1.1):
    
<img src="https://i.imgur.com/qMmmOb9.png" alt="agatta_logotype" width="850"/>

The green/red circles shows the localisation of the synapomorphy/homoplasies.
    
## Example 3: biogeographic analysis

The source files for this third example are from a cladistic biogeographic analysis performed in three-taxon analysis (Dowding et al. 2021; analysis 1 for Devonian fishes, brachiopods, phyllocarids and trilobites). 
    
The following command line performs a full biogeographic analysis 
```
agatta analysis dowding_biogeo.txt --prefix=rslt_dowding --software=paup --softpath=/path/to/paup/paup4a168_ubuntu64 --rosetta=dowding_biogeo_map.csv --replicates=100 --consensus=strict --analysis=heuristic
```    
Note that the biogeographic analysis requires a mapping table (csv file) with two columns, the left one with the taxa and the right one with the corresponding areas
    
> AJ5,Cordillerian_M
> AE2,Uralian_E
> P12,Uralian_E
> P16,Uralian_E
> P17,Uralian_E
> P12,Uralian_M

Here the separator is a comma, it can also be a semicolon, a space, etc. The path to the input mapping file must be given to Agatta using the flag `--rosetta`. 

We can use the `fp` command to remove duplications from trees by Triplet Maximisation Subtree (TMS) or Free-Paralogy Subtree (FPS) analysis (TMS by default, for FPS add the flag `--repetitions=FPS`).

```
agatta fp dowding_biogeo.txt
```

Automatic removal of duplications is always performed for triplet decomposition as decomposition cannot be performed if duplication instances are still present.

Standardisation is the step that allows to move from homologies to characters, and in this case in cladistic biogeography to move from phylogenies to areagrams. It is automatically performed when using `analysis`. The command `standardisation` do only that part
```
agatta standardisation dowding_biogeo.txt dowding_biogeo_map.csv --prefix=standardisation_dowding
```

The file `results_dowding.tre` contains more than 2000 optimal cladograms. A consensus can then be produced to synthetise this huge nomber of trees
```
agatta consensus rslt_dowding.tre --prefix=consensus_dowding
```

A reduced cladistic consensus can also be performed using the `--consensus=rcc` flag (Wilkinson 1994).

## Conclusion

All commands used herein have been conceived as modules. All of them are parts of the Agatta software, and can be synthetised into this workflow.

<p align="center">
   <img src="https://i.imgur.com/6Cr1Rx1.png" alt="agatta_main" width="550"/>
</p>


## References

* Dowding, E. M., Ebach, M. C., & Mavrodiev, E. V. (2021). Validating marine Devonian biogeography: a study in bioregionalization. Palaeontology, 1-15. https://doi.org/10.1111/pala.12578
* Gill, A. C., & Leis, J. M. (2019). Phylogenetic position of the fish genera Lobotes, Datnioides and Hapalogenys, with a reappraisal of acanthuriform composition and relationships based on adult and larval morphology. Zootaxa, 4680(1), 1-81. https://doi.org/10.11646/zootaxa.4680.1.1
* Song, S., Liu, L., Edwards, S. V., & Wu, S. (2012). Resolving conflict in eutherian mammal phylogeny using phylogenomics and the multispecies coalescent model. Proceedings of the National Academy of Sciences, 109(37), 14942-14947. https://doi.org/10.1073/pnas.1211733109
* Wilkinson, M. (1994). Common cladistic information and its consensus representation: reduced Adams and reduced cladistic consensus trees and profiles. Systematic Biology, 43(3), 343-368. https://doi.org/10.1093/sysbio/43.3.343