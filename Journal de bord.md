# 01/02/2019
### API UniProt
**REST**  
On peut obtenir des info via une url :  
The web address for an entry consists of a data set name (e.g. uniprot, uniref, uniparc, taxonomy, ...) and the entry’s unique identifier,
eg. pour P0ACS9
- https://www.uniprot.org/uniprot/P12345
- https://www.uniprot.org/uniprot/P12345.txt

uniprot : références
code accession UniProtKB : https://www.ebi.ac.uk/seqdb/confluence/pages/viewpage.action?pageId=68165098

#### Recherche avancée
Pour **P04483**
Exemple https://www.uniprot.org/uniprot/?query=P04483&format=tab&columns=genes&compress=no&limit=1

URL type : https://www.uniprot.org/uniprot/?query=**Protéine**&format=**format**&columns=**info**&compress=no&limit=**integer**&offset=**integer**
##### format
html | tab | xls | fasta | gff | txt | xml | rdf | list | rss

Préférer le format tab. html renvoie la page entière donc ignore les colonnes. Même principe pour fasta, gff, txt, xml...

La sortie de tab renvoie les infos en colonnes, mais il peut y avoir plusieurs lignes...
##### columns
citation | clusters | comments | domains | domain | ec | id | entry name | existence | families | features | genes | go | go-id | interactor | keywords | last-modified | length | organism | organism-id | pathway | protein names | reviewed | sequence | 3d version | virus hosts
##### includes
? Include isoform sequences when the format parameter is set to fasta.  
Include description of referenced data when the format parameter is set to rdf.  
This parameter is ignored for all other values of the format parameter.  
##### compress
Pour télécharger le fichier dans un tar.gz.
##### limit
Pour limiter le nombre de résultats en retour.
##### offset
Décalle le premier résultat, souvent utilisé avec *limit*.
##### Liens
(https://www.uniprot.org/help/api) ; (https://www.uniprot.org/help/api_retrieve_entries) ; [guide recherche avancé](https://www.uniprot.org/help/api_queries)

## Protéines
### P0ACS9
AcrR d'Escherichia coli.  
Lien (https://www.uniprot.org/uniprot/P0ACS9)
### P04483
TetR d'Escherichia coli (spécifique de la tétracycline)  
Lien (https://www.uniprot.org/uniprot/P04483)
### P0A0N4
QasR de Staphylococcus aureus  
Lien (https://www.uniprot.org/uniprot/P0A0N4)
### Q9AIU0
TtgR de Pseudomonas putida  
Lien (https://www.uniprot.org/uniprot/Q9AIU0)

# 07/02/19
Recherche d'homologue avec BLAST. InterPro pouura servir a l'annotation des séquences.  
> **A Faire** : REST d'InterPro(https://www.ebi.ac.uk/seqdb/confluence/display/JDSAT/Job+Dispatcher+Sequence+Analysis+Tools+Home) pour pouvoir intégrer dans un script.

Choisir une méthode pour télécharger les fichiers du web en Python.
- urllib : urllib.urlretrieve('http://math.univ-toulouse.fr/~besse/Wikistat/pdf/st-intro.pdf', "st-intro.pdf")
- requests ? ou autres...
# 08/02/19
Choix pour le package [requests](http://www.python-requests.org/en/latest/).  
* Réalisation d'une recherche d'homologue avec BLAST et d'un alignement multiple à partir de la séquence fasta des 4 protéines.  
* Implémentation de la fonction de création d'url et de récupération de séquence.
# 14/02/19
> Mise au point :
1. Récupérer nom + séquence
2. (Soumettre à BLAST)
3. InterPro pour les domaines
4. Annoter les séquences
5. Alignement multiple avec (Clustal)[https://www.ebi.ac.uk/Tools/msa/clustalo/]
6. Logo de la protéine

## Application avec P04483
* Création des répertoires
* Soumission de la séquence protéique à InterproScan après l'avoir téléchargée
* Récupération des résultats => *domains.tsv*
* Annoter la séquence à partir des résultats d'interpro
# 15/02/19
Continue de l'application du 14/02
> **A faire** webLogo + Regarder les homologues... analyser les set
# 28/02/19
Avancement du code python pour intégration des domaines et le téléchargement des séquences depuis interprot.
# 01/03/19
REST pour InterProt (domaines) -> comprend pas leur REST
Début des anslyses des homologues.
## Blast
- Via BioPython
- commande type : **`qblast(program, database, sequence, threshold=None, word_size=None, alignments=500, descriptions=500, format_type='XML')`**
- plus de détail avec ’help(NCBIWWW.qblast)’
# 07/03/19
- REST de clustal de l'EBI incompréhensible...
- Alignement multiple par BioPython ?
- implémentation d'une fonction de test de séquence dans uniprot et d'un début de parser de Blast

L'alignements multiples se fera sur les séquences homologues. Et peut-être sur les 4 protéines de bases.
Il faut regarder les séquences qui diffèrrent entre les deux Blast.

Test de l'**API clustal** avec `python clustalo.py --email antoine.heurtel@ibcp.fr --guidetreeout --outfile='fasta' /Users/aheurtel/Documents/Stage/seqProteines.fasta`.

outfile mieux vaut etre un nom et dirige vers un dossier car bcp de fichiers créés.

# 08/03/19
Librairie **interproScan** tester avec la commande `python iprscan.py --email antoine.heurtel@ibcp.fr --outfile='interpro/test' /Users/aheurtel/Documents/Stage/AcR-Ecoli.fasta`. Les fichiers de sorties sont nombreux il faudrait pouvoir les filtrer avant de les télécharger.
- Anlyse des différences entre les Blast.

## Analyse des 2 Blast
Il y a 44 protéines de différences entre les deux blast. ces protéines sont dans la listes 1. Celles de Blast (31) sont dans la liste 2 et celles de biopython (13) dans la liste 3.

Le téléchargement des données de Blast avec l'option Hit table text, retorne un résumé des almignements séparé par des tabulations.


Listes :

1. ’C6DKY6 C3K3D1 Q4K4K9 B5Y006 P39601 P03039 P17446 B7N8L5 B7M2V7 Q0T7M8 Q8FKI7 B7NK49 Q8X6C3 Q1RFM1 B1LIJ9 Q0TKV9 A1AIE5 A6TGE7 Q31U25 A8A771 P0ACU6 A8AKV8 Q5PK70 A9MHG9 Q57H90 Q6D6E1 P03038 A7ML90 P0ACU5 B4EX96 P44757 P32398 A0R4Z6 O34619 Q88FX7 O34643 Q65R67 P43506 B8F853 O31560 P29280 P75952 P67441 P51562’

2. ‘C6DKY6 C3K3D1 Q4K4K9 B5Y006 P39601 P03039 P17446 B7N8L5 B7M2V7 Q0T7M8 Q8FKI7 B7NK49 Q8X6C3 Q1RFM1 B1LIJ9 Q0TKV9 A1AIE5 A6TGE7 Q31U25 A8A771 P0ACU6 A8AKV8 Q5PK70 A9MHG9 Q57H90 Q6D6E1 P03038 A7ML90 P0ACU5 B4EX96 P44757‘

3. ‘P32398 A0R4Z6 O34619 Q88FX7 O34643 Q65R67 P43506 B8F853 O31560 P29280 P75952 P67441 P51562‘

# 14/3/19
- continuer l'analyses des deux blast (parser ?)
- 1/2 intégrer les script REST
- Début de rédaction du rapport de stage (30 min).
- "Intégration" du script `iprscan` pour récupérer les domaines d'interpro

>**entre vu** :
parser de xml blast en avril
faire un beau notebook... : présentation des résultats
voir xpat python : etree = parser xml

# 15/3/19
- Revu du parser du fichier domains
- Création du notebook Proteinus. Il s'agit d'un notebook propre qui contient les codes Python fonctionnels. Il possède également une mise en page correcte afin de faciliter la lecture et le présentation des résultats.
- Renommage du notebook Stage (notebook original) en Nébuleuse. Ce notebook est destiner à l'élaboration du code Python qui une fois fonctionnel pourra intégrer Proteinus.
- Rédaction

# 21/03/19 🌷
- Révision du code makeurl et uniprotInfo.
- Citations des sites des API dans Zotero
- rédaction
# 22/03/19
- fonction pour lancer un alignement clustal
- rédac to LibreOffice
# 28/03/19
- redac
- première analyse rapide du clustal vs uniprot
- web logo
    |publi https://genome.cshlp.org/content/14/6/1188.long

>**entre vue** :
Web Logo : publications, calcul des cscores, la formule de calcul peut-être intégrée au rapport.
Regarder les structures des protéines homologues sur le site uniprot. Parser la fiche structure pour récupére les *positions* des des domaines et aussi les entrées PDB pour pouvoir être soumis à *ESPript*
"Stat" simple sur les séquences homologues : combien on en a, de quel organisme...

# 29/3/19
- mise à jour de def uniprotInfo : + choix de télécharger directement le fichier xml
- mise à jour de Proteinus : + def uniprot ; + mise en page
- annotation manuel des morceaux de WebLogo
- pip install weblogo
# 01/04/2019 🐟
- installation et tests de WebLogo
- création des fonctions pour utiliser WebLogo (*cutDomaines, paramWebLogo, WebLogo*)
- implémentzation des fonctions sauf paramWebLogo
- Mise à jour de la ToDo List
# 02/4/19
- Rédaction du rapport
- lib pyproteinsExt

## Notes sur pyproteinsExt
 1. `msaLib.Msa(fileName="clustalw.aln")` fichier fasta
 2. `recordList = oMsa.recordLookup(predicate=f)` récup les séquences brutes dans un dico
 3. `oMsa.asMatrix` génère des vecteurs pour chaque séquence

# 03/4/19
- sauvegarde
- MAJ def makeDirectory : WebLogo
- MAJ def scanDomains : add récup de tsv depuis InterProt
- MAJ def clustal : add detect + récup fichier clustal présent
- MAJ de protéinus (add MAJ du jour)

# 4/4/19
- création de la fonction pour parser des xml de blast
- 🆙 Proteinus : + def homology + sequenceUniprot
- 🆙 Proteinus : 🆙 def blast
# 5/4/19
- 🆙 def scanDomains : + classement en fonction de la description
- création def verifFile
- 🆙 def homology : + verif de fichier fasta
- 🆙 def clustal : + verif file
- 🆙 def uniProtInfo : + modif de code (+path)
- 🆙 Proteinus : 🆙 def scanDomains + homology + clustal + GlobVar
- 🆙 Proteinus : 🆙 init : + téléchargement du uniprot.xml
- Début de l'organigramme du programme
# 8/4/19
- 🆙 def scanDomains : + cas où la description est vide
- 🆙 def domains2WebLogo : réinitialiser LABEL
- 🆙 Proteinus : 🆙 scanDomains + domains2WebLogo ; add WebLogo
# 9/4/19
- Rédaction
- Logigramme
- Réunion Python
# 10/4/19
- Git : clone des repo
- 🆙 def init : + parser uniprot.xml
- 🆙 WebLogo : revu du code, et de l'ex-LABEL
- création de def msa
- code custom jupy : `jt -t onedork -nfs 11 -fs 11 -tfs 11 -ofs 11 -cellw 88% -T` source : (https://github.com/dunovank/jupyter-themes)
# 11/4/19
- rédaction
- logigramme
- implémentation de def msa
- commit 9366d06
# 12/4/19
- implémentation de def msa
- 🆙 Proteinus : add msa ; 🆙 init + WebLogo
# 15/4/19
- rédaction
- perte des données de Nébuleuses : retour à la version du 11/4
# 16/4/19
- chimera
- 🆙 Nébuleuse : WebLogo et msa
- plot
# 17/4/19
- chimera
# 18/4/19
- pyproteins
  - 🆙 alignement
  - 🆙 readme pyproteinsExt
- 🆙 Nebuleuse : lien avec la lib de Guillaume pour alignement de séquences et parser de séquence de PDB.
# 19/4/19
- implémentation de la cellule qui permet de récupérer les match entre chiméra et UniProt


revoir le paramètre START de WL.



%load_ext autoreload
%autoreload 2


# ToDo list
[x] télécharger la séquence depuis InterProt

[x] Domaines
    [x] REST d'InterPro à tester
    [x] REST à intégrer dans le script.
    [x] télécharger le fichier des domaines d'InterProt
    [x] Intégrer (lire le fichier) les domaines dans l'objet
    [x] Revoir le code pour télécharger directement la séquence depuis interproScan (https://www.ebi.ac.uk/interpro/protein/P04483?export=tsv)
    [?] Revoir le code pour une meilleur gestion des doublons (les *is*)

[x] Alignement multiples
    [x] REST Clustal : script Python à tester
    [x] REST à intégrer dans le script python (Proteinus)
        [x] ADD vérif si clustal présent
    [x] modifier l'extnsion de sortie en aln pour la suite
    [x] améliorer le code def clustal()

[-] WebLogo
    [x] Article, formule de calcul
    [x] tester
    [/] implémenter les fonctions :
        [x] WebLogo
        [x] domains2WebLogo
        [] paramWebLogo
        [x] revoir le LABEL
    [x] intégrer à Proteinus
    [] Plot de l’entropie
        [] afficher les points qui dépassent un certains seuils

[-] Homologues
    [x] Télécharger le résultat de Blast
    [x] Vérifier les identifiants Blast : sont-ils dans UniProt => OK
    [x] Parser du BLAST
    [x] Homologie des autres séquences à la main
    [] Charger les séquences depuis le fasta
    [] Entre les séquences communes
        [] Identifiants UniProt commun ?
[-] Chiméra et PDB
    [x] parser seq chiméra
    [x] parser PDB
    [] intersec chiméra et PDB
    [] intersec avec seq UniProt

[] voir http://endscript.ibcp.fr/ESPript/cgi-bin/ESPript.cgi
[] stat des séquence (cf 28/3)
[] initialisateur mettre à jour tous les self.trucFile
[] Revoir verifFile

x : fait      / : fait a moitié      - : en cours
! : urgent    ? : allô huston        o : condition requise pour
