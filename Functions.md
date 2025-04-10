# Functions

`analysis` is the main Agatta function, which can be used to perform a standard cladistic or biogeographic analysis from hierarchical matrices or newick files. The analysis function can also return the following two interpretation tools:
1. the global retention index, the retention index per character, per state, and per free-subtree.
2. The procedure for testing character states in the context of taxonomic homology proposed by Cao (2008).

The following figure summarises the Agatta pipeline for the `analysis` function.

<p align="center">
   <img src="https://i.imgur.com/6Cr1Rx1.png" alt="agatta_main" width="550"/>
</p>


The following functions can be used when the user wants to perform only a specific part of the analysis pipeline:

* `hmatrix`: converts a hierarchical matrix into a newick file.
* `tripdec`: decompose input trees into three-item statements and compute their weights.
* `standardisation`: replace the terminals of trees by others. Generally used in biogeographic analysis to replace OTU in phylogenetic trees by areas of endemism.
* `convert`: compute a triplet matrix readable by an external software (PAUP*, TNT, WQFM, wTREE-QMC).
* `fp`: compute a free-paralogy analysis for character trees with repeated leaves.
* `consensus`: compute a consensus tree.
* `support`: compute the retention index (global, per character, per state, per free-paralogy subtree).
* `chartest`: compute the character state test procedure for all character states in three-item analysis.

Finally, the `describetree` function is separate and can be used to obtain simple information about trees, their number of leaves, polytomies, etc. This can be useful when manipulating trees before and after analysis. 

The following section explains how each function works, as also shown using the `help` function, for example `agatta help analysis`. 

## Analysis

    Main function of the Agatta python package. Allow to perform a three-item
    analysis, e.g., in the context of systematics phylogenetics, using
    hierarchical characters, or in cladistic biogeography. The cladogram(s)
    obtained by congruence is the tree that maximises the amount of hypotheses
    of cladistic relationships (i.e., the three-item statements) deduced from
    the input trees (the characters).
    The analysis can be performed using a text file containing a hierarchical
    matrix (see section mandatory parameters below for informations about
    the format) or newick rooted trees encoded in a newick, nexus, or nexml
    file.
    There are no constraints on the input trees excepted that they must
    be rooted to perform the analysis. If they are repeated leaves
    (polymorphism), they are automatically removed (several methods
    are implemented, see below).
    Several options are available for analysing the results: the user can
    compute a consensus when several cladograms are optimal, a specific
    character-state testing procedure can be used to test whether each
    character state (i.e., each informative node of a tree) is a synapomorphy
    or an homoplasy, and finally a retention index can be computed to
    obtained the proportion of phylogenetic relationships of characters
    that have been retained in the optimal cladogram.

    Usage:

        agatta analysis <file> [-s -v --analysis=<type> --chartest
                                      --consensus=<type> --parallel=<int>
                                      --pdf --prefix=<file>
                                      --repetitions=<type> --replicates=<int>
                                      --ri --rosetta=<file> --softpath=<path>
                                      --software=<type> --taxarep1=<path>
                                      --weighting=<type> --detailed_tripdec]

        Mandatory parameters:

            <file> Path of the file containing the character trees.
            The trees can be encoded for Agatta in a file in several ways:
              - a hierarchical matrix,
              - A newick file with a single newick tree on each line,
              - A nexus file (extension in .nex),
              - A nexml file (extension in .nexml).
            The following url give more information on how to build input
            files: URL.

        Optionnal parameters:

            -s  Silent mode.
            -v  Verbose mode.
            --analysis=<type>  Type of tree search analysis between an exact
            branch and bound ('bandb') or an heuristic tree search
            ('heuristic'). The heuristic search is only available through
            PAUP*, TNT, WQFM, or wTREE-QMC thus it is mandatory to add the flag
            --software=tnt, --software=paup, --software=wqfm, or 
            software=wtree-qmc (and the flag --softpath accordingly).
            By default the analysis is in branch and bound below 15 terminals
            and heuristic otherwise.
            --chartest  Test and locates all character states on the cladogram.
            Each character state can be a synapomorphy if the hypothesis is
            accepted or an homoplasy if the hypothesis of sameness is rejected.
            Two output files are writen: prefix.chartest gives all locations
            of the states on the cladogram and if the test is passed or not,
            sorted by state, and prefix.chartest_node gives the same
            information sorted by node.
            --consensus=<type>  Compute a consensus which can be a strict
            consensus ('strict') or a reduced cladistic consensus ('rcc',
            Wilkinson 1994) which is able to detect more common information
            in subtrees. By default, the flag without argument produces a
            strict consensus. The output file is prefix.constrict or
            prefix.rcc.
            --parallel=<type>  Option for choosing if the analysis is made
            using multiprocessing or not. This argument can be:
              - 'not' if the user does not wan to use multiprocessing.
              - 'auto' for automatic detection of the number of cpu.
              - any integer corresponding to the number of cpu allowed.
            By default, the analysis is made in parallel using all available
            cpu. This option can be used in the case of very large character
            tree that can saturate the RAM if too many parallel processing are
            active.
            --pdf  Compute a pdf file to visualise character states on
            the cladogram if --chartest is used. One page for each state is
            writen. The file is named prefix.pdf.
            --prefix=<file>  Prefix of all saving files. The complete path can
            be used. By default, the prefix is 'agatta_out' and all files are
            saved in the directory of the first <file>.
            --repetitions=<type>  The removal of repeated leaves in character
            trees is made using the method of free-paralogy subtree.
            Two algorithms are implemented, the original one from Nelson and
            Ladiges (1996) ('FPS') for dealing with paralogy in cladistic
            biogeography, and the algorithm of Rineau et al. (2021) ('TMS')
            designed for all cases of repetitions (not only paralogy).
            If the flag is not used and if repetitions are detected, they are
            automatically removed using Rineau et al. algorithm's.
            The repetition-free character trees are writen in prefix.poly and
            each new tree receives an id, e.g. 1.2 corresponds to the second
            repetition-free subtree computed from the 1st original character
            tree.
            --replicates=<int>  State the number of replicates in case
            of an heuristic tree search. By default the heuristic search is
            launched with 1000 replicates.
            --ri  Compute the retention index of the resulting cladogram and
            a retention index for each character which states the percentage
            of phylogenetic information retained in the optimal cladogram.
            The results are writen in prefix.ri.
            --rosetta=<file>  If the input tree leaves are parts, cladistics
            requires a standardisation step with replacement of parts to
            wholes. It is especially important in cladistic biogeography where
            terminal taxa are replaced by biogeographic areas. The rosetta flag
            replaces leaves of input trees according to a csv file with its
            path given as argument. The csv file is a table with two columns,
            one with the name of the tree leaves and the second with their
            corresponding names to be switched. The results of the
            standardisation are writen in the file prefix.stand.
            --softpath=<path>  Path of the software declared in --software.
            --software=<type>  Choose how to perform the three-item analysis.
            The analysis can be performed using the built-in branch and bound
            in Agatta ('agatta'). 'paup' and 'tnt' can be used for branch and
            bound or heuristic search. 'wqfm' and 'wtree-qmc' are conceived to 
            perform heuristic searches only.
            By default the analysis is made using built-in branch and bound.
            However it works only with very few terminals.
            User should consider to switch the software is the
            analysis time appears to be too long. A prefix.nex file is
            generated if 'paup', a prefix.tnt file for 'tnt', a
            prefix.wqfm for 'wqfm', and a prefix.wtqmc for 'wtree-qmc'.
            --taxarep1=<path>  If the user wants to replace identifiers by real
            leaf names in the result files, this flag can be used with a path
            to a csv file with two columns, the first with the identifiers in
            the actual newick strings and the other with the names the user
            wants.
            --weighting=<type>  Weighting scheme to use on triplets. The
            type of weighting will change the results of the analysis.
            The following schemes can be used:
                - FW: Fractional weighting from Rineau et al. (2021),
                - FWNL: Fractional weighting from Nelson and Ladiges (1992),
                - UW: Uniform weighting from Nelson and Ladiges (1992),
                - MW: Minimal weighting from Wilkinson et al. (2004),
                - AW: Additive weighting : the weight of a triplet in additive
                  weighting corresponds to the number of trees in which the
                  triplet is present,
                - NW: No weighting (all triplets have a weight of 1).
            By default 'FW' is used.
            --detailed_tripdec Compute a detailed csv table showing the
            link between triplet weights and character trees. Each column 
            corresponds to one character (same order as <file>). Each line 
            corresponds to a triplet. The last column and line give the sum
            of all weights of the column or line, respectively.

    Output:

        Four ouput files are writen all the time when using the analysis
        command in addition to optionnal output files.
        The files are:
          - prefix.log is a log file with all the parameters of the analysis.
          - prefix.triplet is a file with all triplets deduced from the input
            character trees. Each row corresponds to one triplet with its
            weight as a fraction and as a float.
          - prefix.taxabloc is a table file with the correspondance between
            leaf identifiers and names given in the input.
          - prefix.tre is a newick file recording all the optimal cladograms
            found during the analysis.
            
    

## hmatrix

    hmatrix converts a hierarchical matrix into rooted trees. Each column of
    the matrix corresponds to one tree.

    Usage:

        agatta analysis <file> [-s -v --chardec --prefix=<file>]

        Mandatory parameters:

            <file>  Hierarchical matrix. A complete guide on the hierarchical
            matrix format is available here: URL.

        Optionnal parameters:

            -s  Silent mode.
            -v  Verbose mode.
            --chardec  Decompose each tree into components (one subtree for
            each informative node).
            --prefix=<file>  Prefix of the saving file. The complete path can
            be used. By default, the prefix is 'agatta_out' and all files are
            saved in the directory of the first <file>.

    Output:

        One output file prefix.tre with the trees deduced from the hierarchical
        matrix.
        
    
    
## Tripdec

    Decomposes rooted tree(s) into minimal cladistic statements (triplets
    or three-item statements) stating that two leaves are closer between them
    than to a third. During decomposition the weight of each triplet is
    computed according to a specific weighting scheme. In three-item analysis,
    the weighted triplets are then analysed to compute the cladogram that is
    in agreement with the maximum amout of them.

    Usage:

        agatta tripdec <file> [-s -v --parallel=<int> --prefix=<file> 
                                     --taxarep1=<path> --weighting=<type>
                                     --repetitions=<type> --detailed_tripdec]

        Mandatory parameters:

            <file> Path of the file containing the character trees.
            The trees can be encoded for Agatta in a file in several ways:
              - a hierarchical matrix,
              - A newick file with a single newick tree on each line,
              - A nexus file (extension in .nex),
              - A nexml file (extension in .nexml).
            The following url give more information on how to build input
            files: URL.

        Optionnal parameters:

            -s  Silent mode.
            -v  Verbose mode.
            --parallel=<type>  Option for choosing if the analysis is made
            using multiprocessing or not. This argument can be:
              - 'not' if the user does not wan to use multiprocessing.
              - 'auto' for automatic detection of the number of cpu.
              - any integer corresponding to the number of cpu allowed.
            By default, the analysis is made in parallel using all available
            cpu. This option can be used in the case of very large character
            tree that can saturate the RAM if too many parallel processing are
            active.
            --prefix=<file>  Prefix of all saving files. The complete path can
            be used. By default, the prefix is 'agatta_out' and all files are
            saved in the directory of the first <file>.
            --taxarep1=<path>  If the user wants to replace identifiers by real
            leaf names in the result files, this flag can be used with a path
            to a csv file with two columns, the first with the identifiers in
            the actual newick strings and the other with the names the user
            wants.
            --weighting=<type>  Weighting scheme to use on triplets. The
            type of weighting will change the results of the analysis.
            The following schemes can be used:
                - FW: Fractional weighting from Rineau et al. (2021),
                - FWNL: Fractional weighting from Nelson and Ladiges (1992),
                - UW: Uniform weighting from Nelson and Ladiges (1992),
                - MW: Minimal weighting from Wilkinson et al. (2004),
                - AW: Additive weighting : the weight of a triplet in additive
                  weighting corresponds to the number of trees in which the
                  triplet is present,
                - NW: No weighting (all triplets have a weight of 1).
            By default 'FW' is used.
            --detailed_tripdec Compute a detailed csv table showing the
            link between triplet weights and character trees. Each column 
            corresponds to one character (same order as <file>). Each line 
            corresponds to a triplet. The last column and line give the sum
            of all weights of the column or line, respectively.
            --repetitions=<type>  The removal of repeated leaves in character
            trees is made using the method of free-paralogy subtree.
            Two algorithms are implemented, the original one from Nelson and
            Ladiges (1996) ('FPS') for dealing with paralogy in cladistic
            biogeography, and the algorithm of Rineau et al. (2021) ('TMS')
            designed for all cases of repetitions (not only paralogy).
            If the flag is not used and if repetitions are detected, they are
            automatically removed using Rineau et al. algorithm's.
            The repetition-free character trees are writen in prefix.poly and
            each new tree receives an id, e.g. 1.2 corresponds to the second
            repetition-free subtree computed from the 1st original character
            tree.

    Output:

        Two files are writen after decomposition. The main one is
        prefix.triplet, a file containing all triplets deduced from the input
        character trees. Each row corresponds to one triplet with its
        weight as an integer (if UW, AW, or NW), or as a fraction and as a
        float otherwise. The second file prefix.taxabloc is a table file with
        the correspondance between leaf identifiers and names given in the
        input trees.
        

## Standardisation

    This command is used for standardisation of characters in the context of
    cladistic biogeography, i.e., replacement of leaves using a correspondence
    table. In cladistic theory, the standardisation is the construction of
    character trees (hypotheses of kinship relationship between bearers) based
    on homologies (hypotheses of kinship relationships between parts). The
    standardisation is used to convert phylogenies in areagrams in cladistic
    biogeography. The only currently implemented option for managing MAST is
    their automatic deletion (i.e. MAST do not bear any unambiguous
    biogeographic information).

    Usage:

        agatta standardisation <file> <file> [-s -v --prefix=<file>]

        Mandatory parameters:

            Two <file> arguments are requested with the standardisation
            command. The first <file> is the path of the file containing the
            character trees. The trees can be encoded for Agatta in a file in
            several ways:
              - a hierarchical matrix,
              - A newick file with a single newick tree on each line,
              - A nexus file (extension in .nex),
              - A nexml file (extension in .nexml).
            The following url give more information on how to build input
            files: URL.
            The second <file> argument is the path of a csv table with two
            columns, the first column corresponds to the leaf names of input
            trees, and the second column to corresponding names for
            replacement. FOr example, in cladistic biogeography, taxa are in
            the left column and corresponding areas in the right column.

        Optionnal parameters:

            -s  Silent mode.
            -v  Verbose mode.
            --prefix=<file>  Prefix of the saving files. The complete path can
            be used. By default, the prefix is 'agatta_out' and all files are
            saved in the directory of the first <file>.

    Output:

        One output newick file prefix.stand containing the trees after
        leaf replacement.



## Convert
        
    The convert command is intended to compute a triplet matrix readable by an
    external software (currently PAUP*, TNT, WQFM, wTREE-QMC are implemented) 
    from a file containing a hierarchical matrix, a list of trees, or a list of 
    triplets.

    Usage:

        agatta convert <file> [-s -v --analysis=<type> --filetype=<type> --log
                                      --parallel=<int> --prefix=<file>
                                      --replicates=<int> --software=<type>
                                      --taxarep1=<path> --weighting=<type>
                                      --repetitions=<type>]

        Mandatory parameters:

            <file> Path of the file containing character trees or triplets.
            The trees can be encoded for Agatta in a file in several ways:
              - a hierarchical matrix,
              - A newick file with a single newick tree on each line,
              - A nexus file (extension in .nex),
              - A nexml file (extension in .nexml).
            The following url give more information on how to build input
            files: URL.
            The user can also use as input file a triplet file generated from
            the Agatta tripdec command.

        Optionnal parameters:

            -s  Silent mode.
            -v  Verbose mode.
            --analysis=<type>  Type of tree search analysis between an exact
            branch and bound ('bandb') or an heuristic tree search
            ('heuristic'). A line is writen accordingly in the output file.
            By default the analysis is in branch and bound below 15 terminals
            and heuristic otherwise.
            --filetype=<type>  The input file can be a classic file containing
            newick trees (newick file, nexus file, or nexml file) ('trees'), or
            a list of triplets ('triplets') generated using the Agatta tripdec
            command.
            --log  Add a line in the output file to generate a log file during
            the PAUP* or TNT analysis.
            --parallel=<type>  Option for choosing if the triplet decomposition
            is made using multiprocessing or not. This argument can be:
              - 'no' if the user does not want to use multiprocessing.
              - 'auto' for automatic detection of the number of cpu.
              - any integer corresponding to the number of cpu allowed.
            By default, the analysis is made in parallel using all available
            cpu. This option can be used in the case of very large character
            tree that can saturate the RAM if too many parallel processing are
            active.
            --prefix=<file>  Prefix of saving .nex or .tnt file. The complete
            path can be used. By default, the prefix is 'agatta_out' and output
            file is saved in the directory of <file>.
            --replicates=<int>  State the number of replicates in case
            of an heuristic tree search. By default the heuristic search is
            launched with 1000 replicates.
            --software=<type>  Choose how to perform the three-item analysis.
            The analysis can be performed using the built-in branch and bound
            in Agatta ('agatta'). 'paup' and 'tnt' can be used for branch and
            bound or heuristic search. 'wqfm' and 'wtree-qmc' can be used for 
            heuristic search only. By default the analysis is made using
            built-in branch and bound. However it works only with very few
            terminals. User should consider to switch the software is the
            analysis time appears to be too long. A prefix.nex file is
            generated if 'paup', a prefix.tnt file for 'tnt', a prefix.wqfm
            for 'wqfm', and a prefix.wtqmc for 'wtree-qmc'.
            --taxarep1=<path>  If the user wants to replace identifiers by real
            leaf names in the result files, this flag can be used with a path
            to a csv file with two columns, the first with the identifiers in
            the actual newick strings and the other with the names the user
            wants.
            --weighting=<type>  Weighting scheme to use on triplets. The
            type of weighting will change the results of the analysis.
            The following schemes can be used:
                - FW: Fractional weighting from Rineau et al. (2021),
                - FWNL: Fractional weighting from Nelson and Ladiges (1992),
                - UW: Uniform weighting from Nelson and Ladiges (1992),
                - MW: Minimal weighting from Wilkinson et al. (2004),
                - AW: Additive weighting : the weight of a triplet in additive
                  weighting corresponds to the number of trees in which the
                  triplet is present,
                - NW: No weighting (all triplets have a weight of 1).
            By default 'FW' is used.
            --repetitions=<type>  The removal of repeated leaves in character
            trees is made using the method of free-paralogy subtree.
            Two algorithms are implemented, the original one from Nelson and
            Ladiges (1996) ('FPS') for dealing with paralogy in cladistic
            biogeography, and the algorithm of Rineau et al. (2021) ('TMS')
            designed for all cases of repetitions (not only paralogy).
            If the flag is not used and if repetitions are detected, they are
            automatically removed using Rineau et al. algorithm's.
            The repetition-free character trees are writen in prefix.poly and
            each new tree receives an id, e.g. 1.2 corresponds to the second
            repetition-free subtree computed from the 1st original character
            tree.

    Output:

        A triplet matrix file with weights that can be analysed using
        PAUP* or TNT or a triplet file with weights for WQFM or wTREE-QMC.
              

## fp

    The free-paralogy analysis is a method used to manage repetitions
    (polymorphism in phylogenetics) in the context of cladistic analysis
    using hierarchical characters. Free-paralogy subtree analysis build
    subtrees to avoid repetition of leaves. Two distinct algorithm for building
    subtrees are currently implemented in Agatta (see --repetitions).

    Usage:

        agatta fp <file> [-s -v --prefix=<file> --repetitions=<type>
                                 --taxarep1=<path>]

        Mandatory parameters:

            <file> Path of the file containing the character trees.
            The trees can be encoded for Agatta in a file in several ways:
              - a hierarchical matrix,
              - A newick file with a single newick tree on each line,
              - A nexus file (extension in .nex),
              - A nexml file (extension in .nexml).
            The following url give more information on how to build input
            files: URL.

        Optionnal parameters:

            -s  Silent mode.
            -v  Verbose mode.
            --prefix=<file>  Prefix of all saving files. The complete path can
            be used. By default, the prefix is 'agatta_out' and all files are
            saved in the directory of the first <file>.
            --repetitions=<type>  Two algorithms are implemented, the original
            one from Nelson and Ladiges (1996) ('FPS') for dealing with
            paralogy in cladistic biogeography, and the algorithm of
            Rineau et al. (2021) ('TMS') designed for all cases of
            repetitions (not only paralogy).
            If the flag is not used and if repetitions are detected, they are
            automatically removed using Rineau et al. algorithm's.
            --taxarep1=<path>  If the user wants to replace identifiers by real
            leaf names in the result files, this flag can be used with a path
            to a csv file with two columns, the first with the identifiers in
            the actual newick strings and the other with the names the user
            wants.

    Output:

        One newick file prefix.poly with a list of subtrees free of repeated
        leaves. Each subtree is labelled with the number of the original tree
        and the number of the subtree generated from the original tree,
        e.g. 1.2 corresponds to the second repetition-free subtree computed
        from the 1st original character tree.

## consensus

    Compute a consensus of several equally optimal trees. Two types of
    consensus are available: a strict consensus, that displays only the clades
    common to all trees, and a reduced cladistic consensus (rcc), that displays
    subtrees that are common to all trees (or in other words, the rcc
    generates all triplets common to all optimal trees and combines them
    into the bigest trees).

    Usage:

        agatta consensus <file> [-s -v --consensus=<type> --prefix=<file>
                                       --taxarep1=<path>]
        Mandatory parameters:

            <file> Path of the file containing the equally optimal trees.
            The trees can be encoded for Agatta in a file in several ways:
              - a hierarchical matrix,
              - A newick file with a single newick tree on each line,
              - A nexus file (extension in .nex),
              - A nexml file (extension in .nexml).
            The following url give more information on how to build input
            files: URL.

        Optionnal parameters:

            -s  Silent mode.
            -v  Verbose mode.
            --consensus=<type>  Compute a consensus which can be a strict
            consensus ('strict') or a reduced cladistic consensus ('rcc',
            Wilkinson 1994) which is able to detect more common information
            in subtrees. By default, the flag without argument produces a
            strict consensus.
            --prefix=<file>  Prefix of all saving files. The complete path can
            be used. By default, the prefix is 'agatta_out' and all files are
            saved in the directory of the first <file>.
            --taxarep1=<path>  If the user wants to replace identifiers by real
            leaf names in the result files, this flag can be used with a path
            to a csv file with two columns, the first with the identifiers in
            the actual newick strings and the other with the names the user
            wants.

    Output:

        One newick file containing the strict consensus prefix.constrict or
        a list of common subtrees (the rcc profile) prefix.rcc.



## Support

    The support command gathers several triplet metrics and indices that are
    intended to compare trees between them. The retention index measures the
    amount of cladistic relationships from the input characters retained in
    the optimal cladogram (or a consensus). The triplet distance measures
    the distance in terms of triplets between two trees. The ITRI compares
    in terms of triplets a tree relatively to a reference tree (used to
    measure efficiency of methods using simulations).
    All these metrics handle weighting schemes.

    Usage:

        agatta support <file> <file> [-s -v --index=<type> --prefix=<file>
                                       --taxarep1=<path> --taxarep2=<path>
                                       --weighting=<type> --repetitions=<type>]

        Mandatory parameters:

            Two <file> arguments are requested which represents the path of
            tree files. Both <file> must contain newick tree(s) (excepted in
            ri mode, see below).
            The trees can be stored in newick files, nexus files (.nex), or
            nexml files (.nexml). Hierarchical matrices are not handled by the
            support command.
            The requested files depend of the --index flag:
                --index=ri: the retention index compares the cladogram to its
                characters. The first <file> contains one tree considered as
                the optimal cladogram (or a consensus); the second <file>
                contains the input character trees used to construct the
                cladogram and can be a newick file or a hierarchical matrix.
                --index=tripdistance: compute various measures for comparison 
                between two trees t1 and t2. Can be used in simulation 
                studies to compare a reconstructed tree to a true tree.

        Optionnal parameters:

            -s  Silent mode.
            -v  Verbose mode.
            --index  The index flag specifies which measure to use to compare
            rooted trees between retention index ('ri'), or inter-tree 
            retention index/triplet distance ('tripdistance'). More 
            informations on the input file in the mandatory parameters section.
            --taxarep1=<path>  If the user wants to replace identifiers by real
            leaf names in the result files, this flag can be used with a path
            to a csv file with two columns, the first with the identifiers in
            the actual newick strings and the other with the names the user
            wants.
            --taxarep2=<path>  Idem as --taxarep1 for the second <file>.
            --weighting=<type>  Weighting scheme to use on triplets. The
            type of weighting will change the results of the analysis.
            The following schemes can be used:
                - FW: Fractional weighting from Rineau et al. (2021),
                - FWNL: Fractional weighting from Nelson and Ladiges (1992),
                - UW: Uniform weighting from Nelson and Ladiges (1992),
                - MW: Minimal weighting from Wilkinson et al. (2004),
                - AW: Additive weighting : the weight of a triplet in additive
                  weighting corresponds to the number of trees in which the
                  triplet is present,
                - NW: No weighting (all triplets have a weight of 1).
            By default 'FW' is used.
            --repetitions=<type> The removal of repeated leaves in character
            trees is made using the method of free-paralogy subtree.
            Two algorithms are implemented, the original one from Nelson and
            Ladiges (1996) ('FPS') for dealing with paralogy in cladistic
            biogeography, and the algorithm of Rineau et al. (2021) ('TMS')
            designed for all cases of repetitions (not only paralogy).
            If the flag is not used and if repetitions are detected, they are
            automatically removed using Rineau et al. algorithm's.
            The repetition-free character trees are writen in prefix.poly and
            each new tree receives an id, e.g. 1.2 corresponds to the second
            repetition-free subtree computed from the 1st original character
            tree. 

    Output:

        One ouput file is writen the results of the tree comparisons:
            - For ri, a global retention index stating the overall information
            content of all character trees retained in the cladogram is writen,
            plus a ri for each character, a ri for each character state, and a 
            ri for each subtree if polymorphism allowing to cut tcharacter 
            trees in subtrees present.
            - For tripdistance, several values are computed given two trees t1 
            (or reference/true tree) and t2 (or reconstructed tree) ('number of 
            triplets' can refer to a sum of triplet weights depending of the
            weighting scheme chosen). The interpetation differs if one wants to 
            compare two equal topologies or if the comparison involves a 
            reference tree and a tree to be compared with (in this case, the 
            significance is added in parentheses):
                
                * Number of triplets in t1 (relevant elements)
                
                * Number of triplets in t2 (retreived elements)
                
                * Number of triplets both in t1 and t2 (true positives)
                  Note that t1 triplets present in t2 and t2 triplets also in 
                  t1 may differ because of the weighting)
            
                * Number of triplets in t2 but not in t1 (false positives) 
                
                * Number of triplets in t1 but not in t2 (false negatives) 
                
                * ITRI(t1,t2) (Precision: (t2 triplets present in t1)/t2) 
                  amount of triplets from the reconstructed tree that are true.) 
            
                * ITRI(t2,t1) (Recall: (t1 triplets present in t2)/t1) amount 
                  of true triplets that are present in the reconstructed tree) 
            
                * Triplet distance (F1-score: harmonic mean of precision and 
                  recall (2 * Precision * Recall) / (Precision + Recall))

        All calculations are made using a specific weighting scheme
        (option --weighting).

## Chartest

    The chartest command is intended to use the character state testing
    procedure for hierarchical characters. Each character state
    (an informative node in a rooted tree) is tested against a cladogram:
    if the test fails, the character state hypothesis (an hypothesis of clade)
    is rejected, and the state becomes homoplasic; otherwise, if the state pass
    the test, it becomes a synapomorphy that supports a specific node of the
    cladogram.
    
    This method has been set up to avoid the problems of parsimony

    Usage:

        agatta chartest <file> <file> [-s -v --pdf --prefix=<file> 
                                       --taxarep1=<path> --taxarep2=<path> 
                                       --repetitions=<type>]

        Mandatory parameters:

            <file> Path of files containing the character trees.
            The trees can be encoded for Agatta in a file in several ways:
              - a hierarchical matrix,
              - A newick file with a single newick tree on each line,
              - A nexus file (extension in .nex),
              - A nexml file (extension in .nexml).
            The following url give more information on how to build input
            files: URL.
            The first <file> contains only one tree: the optimal cladogram or
            the consensus tree resulting from the analysis of a set of
            character trees. The second <file> contains the set of character
            trees.

        Optionnal parameters:

            -s  Silent mode.
            -v  Verbose mode.
            --pdf  Compute a pdf file to visualise character states on
            the cladogram if --chartest is used. One page for each state is
            writen. The file is named prefix.pdf.
            --prefix=<file>  Prefix of all saving files. The complete path can
            be used. By default, the prefix is 'agatta_out' and all files are
            saved in the directory of the first <file>.
            --taxarep1=<path>  If the user wants to replace identifiers by real
            leaf names in the result files, this flag can be used with a path
            to a csv file with two columns, the first with the identifiers in
            the actual newick strings and the other with the names the user
            wants.
            --taxarep2=<path>  Idem as --taxarep1 for the second <file>.
            --repetitions=<type>  The removal of repeated leaves in character
            trees is made using the method of free-paralogy subtree.
            Two algorithms are implemented, the original one from Nelson and
            Ladiges (1996) ('FPS') for dealing with paralogy in cladistic
            biogeography, and the algorithm of Rineau et al. (2021) ('TMS')
            designed for all cases of repetitions (not only paralogy).
            If the flag is not used and if repetitions are detected, they are
            automatically removed using Rineau et al. algorithm's.
            The repetition-free character trees are writen in prefix.poly and
            each new tree receives an id, e.g. 1.2 corresponds to the second
            repetition-free subtree computed from the 1st original character
            tree.
    Output:

        Two output files are writen: prefix.chartest gives all locations
        of the states on the cladogram and if the test is passed or not,
        sorted by state, and prefix.chartest_node gives the same
        information sorted by node. For better visualisation, the flag --pdf
        allow to generate a pdf file with a page for each character state with
        the resulting location and results.

## describetree

    The describetree command output basic informations on a list of
    rooted trees. The informations writen are related to the number of
    nodes, terminals, internal nodes, symmetric nodes, apical nodes, to the
    sesolution of the tree, number of dichotomies, polytomies.

    Usage:

        agatta describetree <file> [-s -v --prefix=<file> --showtaxanames]

        Mandatory parameters:

            <file> Path of the file containing rooted trees.
            The trees can be encoded for Agatta in a file in several ways:
              - a hierarchical matrix,
              - A newick file with a single newick tree on each line,
              - A nexus file (extension in .nex),
              - A nexml file (extension in .nexml).
            THe following url give more information on how to build input
            files: URL.

        Optionnal parameters:

            -s  Silent mode.
            -v  Verbose mode.
            --prefix=<file>  Prefix of the saving file. The complete path can
            be used. By default, the prefix is 'agatta_out' and all files are
            saved in the directory of the first <file>.
            --showtaxanames  The list of all tree leaves is writen in the
            output file.

    Output:

        One output file prefix.dt with descriptors for each tree.

# References

* Cao, N. (2008). Analyse à trois éléments et anatomie du bois des Fagales Engl (Doctoral dissertation, Paris, Muséum national d'histoire naturelle).