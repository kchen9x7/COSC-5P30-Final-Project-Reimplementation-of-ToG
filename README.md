# COSC-5P30-Final-Project-Reimplementation-of-ToG

This project is a personal course project based on the repository [link](https://github.com/IDEA-FinAI/ToG) and the work by Sun et al. (2024).
The project is aimed to reference and understand the work by Sun et al. (2024), and to reproduce the result. It is entirely used for academic purposes not meant be commercialized or redistributed. Please inform should there be a copyright conflict.
The source code of this repository may differ from the original repository referenced as it is modified for running locally.

For the scope of this personal project, only Freebase is used as the KG due to the time and cost constraint. Below is the steps on how to set up the KG for Freebase based on what is provided by the author and personal experience:

## Freebase Setup

## Requirements

- OpenLink Virtuoso 7.2.5 (download from this public [link](https://sourceforge.net/projects/virtuoso/files/virtuoso/))
- Python 3
- Freebase dump from this public [link](https://developers.google.com/freebase?hl=en)

## Setup

### Data Preprocessing

We use this py script (public [link)](https://github.com/lanyunshi/Multi-hopComplexKBQA/blob/master/code/FreebaseTool/FilterEnglishTriplets.py), to clean the data and remove non-English or non-digital triplets:

```shell
gunzip -c freebase-rdf-latest.gz > freebase # data size: 400G
nohup python -u FilterEnglishTriplets.py 0<freebase 1>FilterFreebase 2>log_err & # data size: 125G
```

## Import data

we import the cleaned data to virtuoso. Warning. loading this filtered Freebase data takes at least 1 day to run.

## Warning
For reference, my machine has a 3.2 GHz CPU of AMD Ryzen 7 5800H with Radeon Graphics, 16 GB of RAM, and a NVIDIA GeForce RTX 3060 laptop GPU.

```shell
tar xvpfz virtuoso-opensource.x86_64-generic_glibc25-linux-gnu.tar.gz
cd virtuoso-opensource/database/
mv virtuoso.ini.sample virtuoso.ini

# ../bin/virtuoso-t -df # start the service in the shell
../bin/virtuoso-t  # start the service in the backend.
../bin/isql 1111 dba dba # run the database

# 1、unzip the data and use rdf_loader to import
SQL>
ld_dir('.', 'FilterFreebase', 'http://freebase.com'); 
rdf_loader_run();
```

Wait for a long time and then ready to use.

## Mapping data to Wikidata

Due to the partial incompleteness of the data present in the freebase dump, we need to map some of the entities with missing partial relationships to wikidata. We download these rdf data via this public [link](https://developers.google.com/freebase?hl=en#freebase-wikidata-mappings)

we can use the same method to add it into virtuoso.

```shell
gunzip -c fb2w.nt.gz > fb2w
nohup python -u FilterEnglishTriplets.py 0<fb2w 1>Freebase2Wiki 2>log_err &
```

```shell
# ../bin/virtuoso-t -df # start the service in the shell
../bin/virtuoso-t  # start the service in the backend.
../bin/isql 1111 dba dba # run the database

# 1、unzip the data and use rdf_loader to import
SQL>
ld_dir('.', 'Freebase2Wiki', 'http://freebase.com');
rdf_loader_run();
```

## Test example
Please use checkFreebaseDBConnection.py under the ToG folder to test the connectivity
## 

## Reference
Sun, J.; Xu, C.; Tang, L.;Wang, S.; Lin, C.; Gong, Y.; Ni, L.;
Shum, H.-Y.; and Guo, J. 2024. Think-on-graph: Deep and
responsible reasoning of large language model on knowledge
graph. In The Twelfth International Conference on
Learning Representations.
