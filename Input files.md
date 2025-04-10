# Input files

Agatta accepts two file formats for cladistic analysis. Characters can be coded as a taxon/character matrix called a hierarchical matrix, or they can be coded directly as a list of trees in newick format. Since the fundamental principle of three-item analysis (3ia) is to code characters as hierarchical trees, break them down into three-item statements and recompose them in order to find the optimal tree (the one that maximises the number of three-item statements), the two representations are equivalent. Hierarchical matrices correspond to the classic use of taxon/character matrices in cladistics. Trees in the newick format are generally more commonly used to code gene trees obtained by other analyses as in other supertree methods.

## Hierarchical matrix

### Structure de la matrice

The use of hierarchical matrices was first proposed by Cao et al. (2007). The example below is part of the example file available [here](https://github.com/vrineau/Agatta/blob/main/data/gill_morpho.hmatrix). It is a matrix taken from Gill and Leis (2019) analysed in 3ia.

|       | (0,(1)) | (0,(1)) | (0,(1)) | (0,(1)) |
|----------------|---------|---------|---------|---------|
| Lobotes        | 0       | 1       | 0       | 0       | 0 |
| Datnioides     | 0       | 1       | 0       | 0       | 0 |
| Hapalogenys    | 0       | 1       | 0       | 0       | 0 |
| Chaetodontidae | 1       | 1       | 1       | 0       | 0 |
| Pomacanthidae  | 1       | 0       | 1       | 0       | 0 |
| Drepaneidae    | 1       | 1       | 1       | 0       | 0 |

The first column corresponds to the OTUs (operational taxonomic units) of the taxonomic sampling chosen by the user. All symbols are accepted except the semicolon.

Each subsequent column corresponds to a character, with the number in each cell representing the character state associated with its OTU. For example, *Lobotes* has the state 0 for the character in the first column.

|  | (0,(1)) | 
| -------- | -------- |
| Lobotes     | 0     | 

The first line corresponds to the structure of each character in the matrix. As characters are designed as hierarchies, this structure is represented using the newick format (without the final semicolon) representing the ordering between the different character states. All the character states in the column must be present in the structure. 

Let's take the example of the first character, coded `(0,(1))`. This is a binary character with states 0 and 1. Here, 0 is the root state, i.e. the ancestral state. Since state 1 is included in 0, this indicates that 1 derives from 0. State 1 is therefore the derived state. It indicates that Chaetodontidae, Pomacanthidae and Drepaneidae are grouped together in relation to *Lobotes*, *Datnioides* and *Hapalogenys*. Character 1 corresponds to the following cladistic hypothesis:

`(Lobotes, Datnioides, Hapalogenys,(Chaetodontidae, Pomacanthidae, Drepaneidae)`


### Ancestral/derived states: criteria

It is up to the user to specify the ancestral state and the derived state for each character. Generally, the choice is made according to an outgroup criterion. 

This choice can be made for all the characters by choosing a taxon which would be considered as the extra-group. However, the `hmatrix` format allows ancestral states to be defined character by character if the user so wishes.

As each character is independent, the user can, for example, consider that for character 1, a given taxon can be chosen as the outgroup, but that another taxon is chosen as the outgroup for character 2.

> :memo: **Note:** Optimal trees obtained by three-element analysis are dichotomous and do not have a basal trichotomy, the basal trichotomy being an artefact linked to the a posteriori rooting of an unrooted tree. As the characters are hierarchical, they are rooted trees, and their analysis directly produces a rooted cladogram. Given this way of coding, it is not at all obligatory for the outgroup taxa to be included in the sampling. They can be included if the user wishes to test the monophyly of the ingroup. 

In addition, it is possible to mix criteria, with the decision resting with the user, who has the systematic knowledge of his group to choose the relevant criterion for each character. For example, it is possible to choose an outgroup criterion for certain characters, and an ontogenetic criterion for others. In any case, Agatta allows all possibilities of hierarchical coding.


### Multi-state characters

We have presented the simplest case of binary characters. However, Agatta allows as many states as desired to be encoded for each character. In the case of a character with more than two states, the logic remains the same. For example, we could decide that the first character also has a state 2 derived from state 1:

|       | (0,(1,(2))) | 
|----------------|---------|
| Lobotes        | 0       | 
| Datnioides     | 0       |
| Hapalogenys    | 0       |
| Chaetodontidae | 1       |
| Pomacanthidae  | 2       |
| Drepaneidae    | 2       |

This suggests the existence of an additional clade:

`(Lobotes, Datnioides, Hapalogenys,(Chaetodontidae, (Pomacanthidae, Drepaneidae))`

> :warning: **Important:** For a character state to be informative in 3ia, it is sufficient for at least one OTU to have the ancestral state and two OTUs to have the derived state, saying that two taxa are more closely related to each other than any is to a third.

It is also possible to decide that state 2 is not ultimately derived from state 1:

|       | (0,(1),(2)) | 
|----------------|---------|
| Lobotes        | 0       | 
| Datnioides     | 0       |
| Hapalogenys    | 1       |
| Chaetodontidae | 1       |
| Pomacanthidae  | 2       |
| Drepaneidae    | 2       |

This involves (changing the state of Chaetodontidae so that state 1 remains informative):

`(Lobotes, Datnioides, (Hapalogenys,Chaetodontidae), (Pomacanthidae, Drepaneidae))`

 See also Faure-Brac et al. (2020) for another empirical example of hierarchical coding.

## Polymorphism, missing and inapplicable data

The `hmatrix` format allows differentiated treatment for the three issues of coding that are polymorphism, non-applicable data and missing data (Zaragüeta Bagils and Bourdon 2007). 

In the case of polymorphism, character states must be entered separated by a comma:

|  | (0,(1)) | 
| -------- | -------- |
| Lobotes     | 0,1     | 

The polymorphism is then processed using the free-paralogy subtree analysis method (Nelson and Ladiges 1996). By default, the TMS algorithm of Rineau et al. (2022) is used to perform the free-paralogy while retaining as much cladistic information as possible, but the original algorithm of Nelson and Ladiges (1996) is also implemented (`--repetitions==FPS`).

In the case of missing data, a question mark is used to indicate that the state is unknown:

|  | (0,(1)) | 
| -------- | -------- |
| Lobotes     | ?     | 

In this case, *Lobotes* does not participate in the character and does not generate three-item statements. 

Finally, non-applicable data implies that no character state is satisfactory. In this case, we recommend following the procedure of Zaragueta and Bourdon (2007) and coding the non-applicable as the root state (here 0). Indeed, for a hierarchical character, the root state does not necessarily need to be defined, as the root state simply corresponds to the root of any phylogenetic hypothesis. Since non-applicability implies that no derived state corresponds, then the OTU must be connected to the root. Unlike missing data, non-applicable is positive information, so this encoding makes it possible to translate this positive knowledge into three-item statements.

## File formatting

The hierarchical matrix file must be in csv format. You can create a hierarchical matrix directly in Excel and then save the file as a csv. 

<p align="center">
   <img src="https://imgur.com/1NTXU2m" alt="agatta_excel" width="300"/>
</p>

Commas are not allowed as separators, as they are used for polymorphism, so we recommend using a semicolon as a separator. If you are using Excel, save the file in **CSV: (separator: semicolon) (*.csv)** format. Opening the file with a text editor gives you:

```
;(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1,(2)));(0,(1,(2)));(0,(1,(2)));(0,(1,(2)));(0,(1));(0,(1,(2)));(0,(1,(2)));(0,(1));(0,(1));(0,(1,(2)));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1));(0,(1))
Lobotes;0;1;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;1;0;0;0;0;0;0;0;1;0;0;0;0;0;0;0;0;0;0;0;0;1;1;1;1;0;0;0
Datnioides;0;1;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;0;0;0;0;0;0;0;1;0;0;0;0;0;0;0;0;0;0;0;0;?;1;1;1;0;0;0
Hapalogenys;0;1;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;1;1;1;0;0;0;0;0;0;0;0;1;0;0;0;0;0;0;0;0;0;0;0;0;1;1;1;1;0;0;0
Chaetodontidae;1;1;1;0;0;0;0;2;0;0;0;0;0;0;1;0;0;0;0;0;0;0;0;0;1;0;0;0;0;0;0;1;1;1;1;0;1;0;1;0;0;0;0;1;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;1;0;0;0
Pomacanthidae;1;0;1;0;0;0;0;1;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;1;0;1;0;0;0;0;0;0;0;0;1;1;0;0;0;0;0;0;1;0;0;0;0;0;0;0;0;0
Drepaneidae;1;1;1;0;0;0;0;2;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;0;0;0;0;0;0;0;0;0;0;1;1;0;0;0;0;0;0;1;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;0;0;0
Ephippidae;1;1;0;1;1;1;1;2;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;0;0;0;0;0;1;0;0;0;0;0;1;0;0;0;0;0;0;1;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;0;0;0
Leiognathidae;1;1;0;1;1;0;0;2;0;1;0;0;0;1;1;0;0;0;0;0;0;0;0;1;1;0;0;0;0;0;0;1;1;1;1;1;1;0;1;0;0;0;0;1;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;0;0;0
Scatophagidae;1;1;0;1;1;1;1;2;1;1;1;1;0;0;1;0;0;0;0;0;0;0;0;0;1;0;0;0;0;0;0;0;1;1;1;0;1;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;0;1;1;0;0;0
Antigonia;1;1;0;1;1;1;0;0;2;1;2;0;1;0;2;1;0;0;0;0;0;0;0;1;1;0;0;0;1;0;0;0;1;1;1;1;1;1;0;0;0;0;0;1;1;1;1;0;0;1;0;0;1;1;0;0;0;0;0;1;0;0;0
Capros;1;0;0;1;1;0;0;0;2;1;2;0;1;0;1;1;0;0;1;0;0;0;1;?;1;0;0;0;1;0;1;0;1;1;1;0;1;1;0;0;1;0;1;1;1;1;1;1;0;1;0;1;1;1;0;0;1;0;0;1;1;1;0
Siganidae;1;1;0;1;1;2;2;2;1;1;0;1;1;1;1;1;1;1;1;1;0;0;0;0;1;0;0;0;0;0;1;1;1;1;1;0;1;1;0;0;1;1;1;1;1;1;1;1;0;0;0;0;0;0;0;0;1;0;0;0;0;0;1
Luvaridae;1;1;0;0;0;1;2;2;2;1;1;0;1;1;2;1;1;1;1;0;1;1;1;1;0;0;1;0;1;1;0;1;1;1;1;1;1;0;0;0;0;1;1;1;1;1;1;0;1;1;1;1;1;1;0;0;1;0;0;0;1;0;0
Zanclidae;1;1;0;1;1;2;2;2;2;1;1;0;1;1;2;1;1;0;0;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;0;1;0;0;1;0;0;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;0;0;0;1;0;0
Acanthuridae;1;1;0;1;1;2;2;2;2;1;1;0;1;1;2;1;1;1;1;1;1;1;1;?;1;1;1;1;1;1;1;1;1;1;1;0;1;0;0;1;0;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;0;0;0;0;1;1
```

Once saved in the correct format, check the file extension. 

> :warning: **Important:** In order for Agatta to automatically recognise that it is a hierarchical matrix, you must change the .csv extension to `.hmatrix`.

It is also possible with Agatta to analyse several hierarchical matrices combined in a single cladistic analysis. In this case, simply give the file names one after the other

```
  agatta analysis file1.hmatrix file2.hmatrix ...
```

## Newick file

The newick format is the second format readable by Agatta. The trees you wish to analyse must be **rooted**. The newick file must only contain the list of trees, each line of the file corresponding to a tree. The example file here is that of Song et al. (2012) available [here](https://github.com/vrineau/Agatta/blob/main/data/song_gen.txt).

Here are the first lines of the file:

```
(Chicken:0.131279775345,(((((((Marmoset:0.0204923957271,((Orangutan:0.00584046083478,((Human:0.00233080025572,Chimpanzee:0.00254878680241):0.000769089390244,Gorilla:0.00176992228314):0.00288531375061):0.00467903448512,Macaque:0.00908469530327):0.00734594094106):0.0296837843809,(Galagos:0.0600055740335,Mouse_Lemur:0.0694590103929):0.0196895108756):0.0085007567437,(Tree_Shrew:0.0534460635684,((((Mouse:0.0349199386903,Rat:0.0345660857178):0.0897288064714,Kangaroo_Rat:0.0649361322722):0.00930431451076,(Guinea_Pig:0.0711517239764,Squirrel:0.0481034437826):0.00498901022113):0.00991061551553,(Tarsier:0.217182450829,(Rabbit:0.0631257980439,Pika:0.087313728905):0.0291647109858):0.0339406584933):0.0030207288372):0.00372275015446):0.00872816165234,((((Microbat:0.0696356706177,Megabat:0.0390727680795):0.0086854921458,(Horse:0.0461656721413,((Dolphin:0.0272954336484,Cow:0.0443204506945):0.0116990944027,(Alpaca:0.051306106068,Pig:0.046407562946):0.00563985340054):0.0247170466469):0.00304339918494):0.00358264486119,(Dog:0.0580489915606,Cat:0.0519621580488):0.0244766206527):0.00494622940371,(Shrew:0.0804342808451,Hedgehog:0.139409366682):0.0210030742124):0.0130522958142):0.00809030960191,((Lesser_Hedgehog_Tenrec:0.156305507638,(Hyrax:0.064198126165,Elephant:0.0292820820713):0.00798950914849):0.0213905471889,(Sloth:0.0383674324752,Armadillos:0.0398080414024):0.0192051567243):0.00316664009477):0.054936328385,Platypus:0.599083136441):0.0419332947834,(Opossum:0.0426931845392,Wallaby:0.0489905851887):0.0753994602564):0.131279775345);
(Chicken:0.13184348409,(((Wallaby:0.0403884889125,Opossum:0.0424549058291):0.0623712854935,Platypus:0.106934421683):0.0265468149082,(((Lesser_Hedgehog_Tenrec:0.0763243234611,(Hyrax:0.0439837309698,Elephant:0.0154182537039):0.00814539614046):0.00927252801896,(((((Cow:0.0253768995556,(Dolphin:0.0137381500408,Pig:0.0515071568701):0.00214247375384):0.00369849062439,Alpaca:0.0327041327444):0.0129939134522,((Microbat:0.0488318249748,(Dog:0.0251914464999,Cat:0.019077962803):0.0156030468259):0.00191908404652,Horse:0.031403746682):0.00190573500035):0.000789098343179,(Megabat:0.0306925352959,(Hedgehog:0.0955938274852,Shrew:0.109121497038):0.0170079661056):0.00357223557112):0.00782417078979,(((Pika:0.0525511270699,Rabbit:0.0390937774654):0.0249464279599,(((Rat:0.0415344954862,Mouse:0.0399640478488):0.0965731665767,Guinea_Pig:0.07749120868):0.0148299066203,(Squirrel:0.0502430198655,Kangaroo_Rat:0.0753162798415):0.00416568798231):0.00545532042101):0.00328679821596,(Tree_Shrew:0.0426560599163,((Tarsier:0.0395620026083,((Macaque:0.00608570187988,(Gorilla:0.00224422775399,(Orangutan:0.00178158136896,(Human:0.00113846667515,Chimpanzee:0.000802993594136):0.000483490555002):2.51049141848e-06):0.00347997654954):0.00821095330186,Marmoset:0.0174481410002):0.0135122407118):0.00305192625139,(Mouse_Lemur:0.0161443473009,Galagos:0.0451624040731):0.0145777943743):0.00233717476908):0.00310699260183):0.00188081676512):0.00884283494833):0.00800323823718,(Sloth:0.0286313631651,Armadillos:0.0298203979513):0.00653477704791):0.100297249941):0.13184348409);
(Chicken:0.200683834898,((((((Hyrax:0.116256868083,Elephant:0.0380072794168):0.0136744583945,Lesser_Hedgehog_Tenrec:0.0757357089314):0.0152092268668,(Armadillos:0.0330496450788,Sloth:0.0390897018537):0.0223747307328):0.00255549065834,((((Dog:0.0380707200472,Cat:0.0219888008619):0.0146010837503,((Megabat:0.026849094959,(Shrew:0.0812377176667,Microbat:0.0385531787149):0.0207936007114):0.0074741804449,(Dolphin:0.0235088724544,(Pig:0.0346116329606,(Cow:0.0431980682584,Alpaca:0.0323155644264):0.00294880915206):0.00313415983936):0.00994396974098):0.00246798483566):0.00165823867717,(Horse:0.037894764365,Hedgehog:0.137100615862):0.00546346949291):0.0118186651971,((((Guinea_Pig:0.0495811955458,Kangaroo_Rat:0.0678327630112):0.00709683048706,(Squirrel:0.0377275552318,(Mouse:0.0291923462182,Rat:0.0305524733839):0.116284752903):0.00823842575795):0.00774685443388,(Pika:0.0595468891827,Rabbit:0.0317487035181):0.0375754712606):0.0038912895723,(Tarsier:0.0516873435361,((Marmoset:0.0311370484253,((Orangutan:0.0152083457523,(Gorilla:0.00129651459821,(Human:0.00245267408963,Chimpanzee:0.0030493338097):0.00121090801175):0.00248508181011):0.00382150588066,Macaque:0.0111332008693):0.00577878387711):0.0137031584176,((Galagos:0.0612751243277,Mouse_Lemur:0.0229549694479):0.0047800435683,Tree_Shrew:0.183791419534):0.00190591845589):0.00370001159991):0.0016619546699):0.00626408623059):0.00747947925016):0.0885583652299,(Wallaby:0.0394521811681,Opossum:0.0396475744035):0.106106659053):0.0500235726093,Platypus:0.107980129272):0.200683834898);
(Chicken:0.363547638048,(Platypus:0.281150828862,((Kangaroo_Rat:0.227154947424,(Squirrel:0.0671153562038,((Guinea_Pig:0.12219947807,(Mouse:0.0285161251895,Rat:0.0429846047319):0.085590856818):0.0140633344345,(((Pika:0.0858655571682,Rabbit:0.044473495109):0.0275024940273,(((Tarsier:0.0556802134764,((((Gorilla:1.85580720032e-06,(Chimpanzee:0.00352263735379,Human:0.00210876209409):1.85580720032e-06):0.00195715763511,Orangutan:0.0456676117485):0.00894974100682,Macaque:0.0174908800859):0.00817949575529,Marmoset:0.021105563553):0.0124318568927):0.00509050125734,(Mouse_Lemur:0.0382624798616,Galagos:0.0510280610378):0.00679236389045):0.00332142940613,((((Pig:0.134682668683,(Dolphin:0.0275396377239,Cow:0.0627157724198):0.00746789098307):0.00249029049972,Alpaca:0.0885204171047):0.0124760191532,((Horse:0.0767624652867,(Microbat:0.0405942950832,Megabat:0.0525907665956):0.00926098341272):0.00122479425368,((Shrew:0.11653369162,(Dog:0.0289418502698,Cat:0.0327153402309):0.0191229766832):0.00359470401594,Hedgehog:0.10121580374):0.00102357688644):0.00201060361489):0.00833002542791,(Lesser_Hedgehog_Tenrec:0.114841148989,(Elephant:0.023590024411,Hyrax:0.0905907317276):0.0271765866075):0.0153063247616):0.00703229766306):0.00231005504781):0.00188518271632,((Sloth:0.0488322211506,Armadillos:0.0405879799233):0.0160024505494,Tree_Shrew:0.0606139860793):1.85580720032e-06):0.00486065707291):0.00856501710121):0.0164564018957):0.0582168167265,(Opossum:0.040186539964,Wallaby:0.0490793403688):0.0637562719933):0.0711142632663):0.363547638048);
(Chicken:0.0872510956827,(Platypus:0.0916288538349,(((Armadillos:0.0232895399499,Sloth:0.0265141705103):0.0062947219024,((((((Cat:0.0109115852854,Dog:0.02699168323):0.00646605982703,(Shrew:0.0549090853451,Hedgehog:0.0577385128521):0.00221771946408):0.00058852239009,(Horse:0.0267251866821,(Alpaca:0.0239421690218,(Pig:0.020842146481,(Cow:0.0318794216178,Dolphin:0.00796368324917):0.00660965147222):0.00270509379588):0.00698846088656):0.000757566101585):0.00111659914861,(Microbat:0.0276314885711,Megabat:0.0229243908415):0.00301268258327):0.0051566331974,(((Rabbit:0.0278450399236,Pika:0.0611711026038):0.0145936465378,(Kangaroo_Rat:0.0385707854864,(((Mouse:0.0148402720551,Rat:0.0292991044371):0.0587207832435,Guinea_Pig:0.0240753243326):0.00870220278605,Squirrel:0.0325047490768):0.00151051853278):0.00245728595295):0.000835398494625,((Tree_Shrew:0.0552870969104,(Marmoset:0.0152446328987,(Macaque:0.00417253367676,(Orangutan:0.00199286961427,((Gorilla:0.00198804193292,Chimpanzee:0.000543933950245):0.00200862822564,Human:0.00394477113939):0.00489498205984):0.00274365141538):0.00235501951006):0.00743395447991):0.00516839205442,((Galagos:0.0310090103588,Mouse_Lemur:0.0177229801708):0.00580934445442,Tarsier:0.0270569479657):0.00108522165996):0.00230590785071):0.00382110603812):0.00412040752982,(Lesser_Hedgehog_Tenrec:0.07681892226,(Hyrax:0.0352997058422,Elephant:0.0255963744333):0.00622402483904):0.00366942698962):0.00225650284946):0.0640034221309,(Opossum:0.0448248621967,Wallaby:0.0203584121407):0.0369839192823):0.0253256436945):0.0872510956827);
(Chicken:0.0872523646315,(Platypus:0.0916301259207,(((Sloth:0.0265145897885,Armadillos:0.023289909088):0.00629479597571,((Lesser_Hedgehog_Tenrec:0.0768204862882,(Hyrax:0.035300420892,Elephant:0.0255969008558):0.00622397719316):0.0036696290263,((((Pika:0.0611721550847,Rabbit:0.0278455247986):0.014593882291,(Kangaroo_Rat:0.038571522695,(Squirrel:0.0325052334648,(Guinea_Pig:0.0240757198167,(Mouse:0.0148404490322,Rat:0.0292995961211):0.0587217346377):0.00870228559982):0.00151058896588):0.00245731687145):0.000835422642716,(((Marmoset:0.0152448737567,(Macaque:0.00417259735364,(Orangutan:0.00199289871858,(Human:0.00394484509718,(Gorilla:0.00198807233434,Chimpanzee:0.000543942780207):0.00200862237432):0.00489509096801):0.00274370255366):0.00235505447377):0.00743408515979,Tree_Shrew:0.0552879989092):0.00516849444974,(Tarsier:0.0270573707338,(Galagos:0.0310095302094,Mouse_Lemur:0.017723279285):0.00580940192562):0.0010852573422):0.0023059247951):0.00382112890082,((Microbat:0.0276318928733,Megabat:0.0229247321523):0.00301272976514,(((Dog:0.0269920866368,Cat:0.0109117340273):0.00646615984747,(Hedgehog:0.0577393219705,Shrew:0.0549096852912):0.00221783481722):0.000588532814399,((((Dolphin:0.00796380082548,Cow:0.0318799610589):0.00660978674886,Pig:0.0208424741514):0.00270512213928,Alpaca:0.0239425164456):0.00698856657087,Horse:0.0267255867517):0.000757572059446):0.00111661410035):0.00515670631913):0.00412046934232):0.0022565402445):0.064004430021,(Opossum:0.044825426684,Wallaby:0.0203588158256):0.0369843254961):0.0253261189247):0.0872523646315);
(Chicken:0.136546931027,(Platypus:0.179266807368,((Wallaby:0.0742084449676,Opossum:0.0154310882909):0.0563039240019,(((Hyrax:0.129644086888,Elephant:0.031659251204):0.0143268807163,Lesser_Hedgehog_Tenrec:0.128650372269):0.0409400050467,((Sloth:0.0401945330851,Armadillos:0.0703578488285):0.00539384033009,(((((Kangaroo_Rat:0.132683890288,(Mouse:0.038105296902,Rat:0.0165828191058):0.10420602639):0.021357026115,(Squirrel:0.0570591934752,Guinea_Pig:0.123450064728):0.0015978986721):0.0135204598064,(((Shrew:0.329997957366,Rabbit:0.0479721226934):0.0264631661777,Pika:0.0898570404907):0.0829986291266,Tree_Shrew:0.103886397388):0.0235443949706):0.00722020890642,(Tarsier:0.0427092341119,((Marmoset:0.0283861371785,(Macaque:0.0102085121417,(Orangutan:0.00575016333401,(Gorilla:0.00143134576028,(Chimpanzee:0.00658928262202,Human:0.00182469940144):0.000171146114819):0.00603167067159):0.00319899297089):0.00809430176493):0.0234798973117,(Mouse_Lemur:0.0266103355474,Galagos:0.0456978386923):0.0164229849235):0.00207529168367):0.00654968462797):0.00714769766229,(Horse:0.0396169605171,(((Microbat:0.047595849708,Megabat:0.059835503721):0.0120860579436,(((Pig:0.0651120644546,Dolphin:0.0329684262625):0.00414757197852,Cow:0.0595758833579):0.0053884852643,(Hedgehog:0.189313098613,Alpaca:0.0227612515551):0.00742119716421):0.00975692022533):0.00323658354899,(Dog:0.0526762139792,Cat:0.0368973379242):0.0224968449919):0.00675354444647):0.0166734796818):0.0123173852589):0.00255899406954):0.117092071635):0.0630748039873):0.136546931027);
(Chicken:0.0962057786784,((((Lesser_Hedgehog_Tenrec:0.121912841265,(Elephant:0.0247970277425,Hyrax:0.0528046634988):0.00627475514904):0.0157583040789,((Sloth:0.030639141293,Armadillos:0.0236870304191):0.0117810606426,(((Tree_Shrew:0.0506820568419,((Galagos:0.0443624057793,Mouse_Lemur:0.0123110676846):0.0113617905403,(Tarsier:0.0334895625167,(Marmoset:0.0190487929822,(Macaque:0.00926549680305,((Gorilla:0.00206666209068,(Human:0.00473629891953,Chimpanzee:0.00224099711542):0.00069270288697):0.00235139733674,Orangutan:0.0066390788931):0.0026306908598):0.00444484174803):0.0160250964103):0.00295332919304):0.00369348636522):0.00114317695241,((Guinea_Pig:0.0544832402943,((Kangaroo_Rat:0.066095903567,(Rat:0.0325671723434,Mouse:0.0312417881819):0.0626008124186):0.0105178198062,Squirrel:0.0432258565473):0.00148358181329):0.00640845292154,(Rabbit:0.0387544256894,Pika:0.0566856796269):0.0271900267401):0.00148040204008):0.00273987639206,(((Microbat:0.0633058799598,Megabat:0.0318241610842):0.0100337894072,(((Alpaca:0.0521655408767,((Dolphin:0.0215303597651,Cow:0.0376235570243):0.00646827245532,Pig:0.0347679516373):0.00296697102487):0.0107914080416,(Hedgehog:0.0744350347666,Horse:0.0196597718449):0.00322418581917):0.000383888684706,Shrew:0.0886761520204):0.000435098958772):0.000759807979492,(Cat:0.028430848992,Dog:0.0870898990636):0.00920563592386):0.00426228974108):0.00418604886634):0.00330750718834):0.0603937607064,(Wallaby:0.0307058542951,Opossum:0.037966745428):0.0564655354223):0.0239513314234,Platypus:0.144307625541):0.0962057786784);
(Chicken:0.199796810723,(Platypus:0.201537316295,(((((Hyrax:0.10501974541,Elephant:0.0451963450468):0.0316943222706,Lesser_Hedgehog_Tenrec:0.167400430827):0.00983114630382,(Armadillos:0.0756595378885,Sloth:0.0685625499476):0.0267767875275):0.00594499947469,(((Pika:0.170949541065,Rabbit:0.0757359210191):0.0705864653424,((((Galagos:0.0879337114072,Mouse_Lemur:0.0548214689459):0.0135964151469,(Marmoset:0.0349718629994,(Macaque:0.0134672951683,(Orangutan:0.00326726698019,(Gorilla:0.00684618802512,(Chimpanzee:0.00294919459027,Human:0.00488066360111):0.00103060086267):0.00494195443167):0.00602603844384):0.0111160977497):0.033440776734):0.00692701689399,Tarsier:0.102366917184):0.0156760876188,(Tree_Shrew:0.111443952422,(((Mouse:0.064858341486,Rat:0.0353372424265):0.133890997176,Kangaroo_Rat:0.0979315353103):0.0115172399649,(Squirrel:0.096686096584,Guinea_Pig:0.309699051274):0.0116783220478):0.011291738737):1.59629207173e-06):0.00720517873077):0.00807878308857,(((Cat:0.0306106744805,Dog:0.15641086277):0.0379620242673,((Microbat:0.0498582966141,Megabat:0.0514130854864):0.0101379537554,(Shrew:0.203365404698,Hedgehog:0.0750407103774):0.0321725446815):0.00209122344249):0.00422583105747,(Horse:0.127368673363,(((Cow:0.0747271996394,Dolphin:0.0411039528561):0.0132138037828,Pig:0.04864252937):0.00556265644297,Alpaca:0.0426332868432):0.0260327456733):0.000809575142079):0.0132559950395):0.0101046253078):0.130144877085,(Opossum:0.0953789646792,Wallaby:0.0749079886358):0.136520826472):0.0899630675968):0.199796810723);
(Chicken:0.131324902577,(Platypus:0.115931817925,(Wallaby:0.0243069979749,(((Cat:0.0121107086226,(Tarsier:0.0338299154439,((Tree_Shrew:0.0561925754091,Hedgehog:0.0806593403465):0.00909809874502,Guinea_Pig:0.0802306573812):0.00154441457567):0.0125595588154):0.00208467527621,((Shrew:0.0252299560508,Squirrel:0.0301103816857):0.00372833440828,((((Horse:0.168382201136,(Opossum:0.0849884309416,(Cow:0.0631492904817,Marmoset:0.0413893177713):0.00661696369303):0.013141169556):0.000996699950011,Dog:0.012660929514):0.0535181690978,((Armadillos:0.0191188605399,Sloth:0.0147552062362):0.0105502835479,((((Macaque:0.0105142417943,(Orangutan:0.00132658992773,(Gorilla:0.00921096762721,(Human:0.00343977892986,Chimpanzee:0.00328116592198):0.00154242613997):0.00321327128677):0.00573788783649):0.020027568981,(Lesser_Hedgehog_Tenrec:0.0463157372372,(Kangaroo_Rat:0.057722587663,(Rat:0.00578498230605,Mouse:0.00486393336124):0.0409934846526):0.0114663481255):1.18036861914e-06):0.00443076951761,(Hyrax:0.0303825007704,Elephant:0.0435911258335):0.00698518704561):0.0031513437166,(Mouse_Lemur:0.0218261026561,Galagos:0.0307123985234):0.00341459971514):0.00188863518615):0.00231423926746):0.00098178405633,((Rabbit:0.0674979164195,Pika:0.0444724914265):0.0362724413842,((Alpaca:0.165481804929,Pig:0.00902579360229):0.0135565886552,Dolphin:0.0340832806144):0.0201152089184):0.00239444781427):0.00227958012377):0.000951620155465):0.00227647521544,(Microbat:0.0456639755391,Megabat:0.0116443252161):0.00174774278949):0.0577004093402):0.0361601611508):0.131324902577);
```

This file can be analysed directly by Agatta. Branch lengths are not used by 3ia, but it is not necessary to delete them.

It is quite possible to have OTU repeated within a tree, which corresponds to polymorphism. Polymorphism is managed prior analysis using the free-paralogy subtree analysis method (Nelson and Ladsiges 1996). There are no restrictions on the sampling of each tree. It is not necessary for the trees to have exactly the same taxa (which corresponds here to missing data).

# References

* Gill, A. C., & Leis, J. M. (2019). Phylogenetic position of the fish genera Lobotes, Datnioides and Hapalogenys, with a reappraisal of acanthuriform composition and relationships based on adult and larval morphology. Zootaxa, 4680(1), 1-81. https://doi.org/10.11646/zootaxa.4680.1.1

* Cao, N., Zaragüeta Bagils, R., & Vignes-Lebbe, R. (2007). Hierarchical representation of hypotheses of homology. Geodiversitas, 29(1), 5-15.

* Faure-Brac, M. G., Rineau, V., & Bagils, R. Z. (2020). Rethinking Pleijel’s (1995) characters under a hierarchical point of view. bioRxiv, 2020-10.

* Nelson, G. J., & Ladiges, P. Y. (1996). Paralogy in cladistic biogeography and analysis of paralogy-free subtrees. American Museum novitates, no. 3167.

* Rineau, V., Moncel, M. H., & Zeitoun, V. (2023). Revealing evolutionary patterns behind homogeneity: the case of the Palaeolithic assemblages from Notarchirico (Southern Italy). Journal of Archaeological Method and Theory, 30(1), 203-238.

* Song, S., Liu, L., Edwards, S. V., & Wu, S. (2012). Resolving conflict in eutherian mammal phylogeny using phylogenomics and the multispecies coalescent model. Proceedings of the National Academy of Sciences, 109(37), 14942-14947. https://doi.org/10.1073/pnas.1211733109

* Zaragüeta Bagils, R., & Bourdon, E. (2007). Three-item analysis: Hierarchical representation and treatment of missing and inapplicable data. Comptes Rendus Palevol, 6(6-7), 527-534.