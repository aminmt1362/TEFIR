# Table Extraction for Information Retrieval

## Description & Introduction
In IR, thousands of papers are published every year that contains valuable information such as tables which almost all of these papers are stored in PDF format.

In systematic reviews, tabular data has a central point in presenting test values, which when extracted allow easy manipulation, aggregation and usage in different studies, and also enable search engines to index them.

In empirical domains like computer science and its subdomains or also in other domains such as medical domains, thousands of papers are published every year also containing tables, and these valuable data usually get ignored by search engines which otherwise would help a lot of researchers.

I developed a framework that processes the results of PDF extraction tools and calculates a scoring value based on the already created ground truth in the framework. The score value allows creating a ranked list of table extraction tools. The scoring algorithm does the scoring based on the already established ground truth.

Another part of the framework is to evaluate the results we obtained by the scoring part of the framework. For this, i process the test collection that contains 5870 PDF files with the pdf extraction tools and put the results into the search engine Solr. I also created a set of questions from the test collection and a set of relevance answers. The result of the framework provides an evaluation result which presents a better understanding of the ability of the extraction tools and the results calculated.

![Table Extraction for Information Retrieval - Framework](https://cldup.com/uclqDPRVay.png)

## Projects
This project is based on two sub projects which are:
- TCF Table comparison and scoring.
- TableExtractor: Table preprocessing and conversion into JSON format.

## Requirements
- Java
- MongoDB
- Solr

# Test
- Clonse both sub projects.
- Start Mongo DB.
- Start Solr.
- Start both projects.

## Validation
- Import all Ground truth in this project into the framework to prepare for scoring.
```sh
curl -X POST \
  http://localhost:8090/importtable/ \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 9df84783-c4c0-1d9e-66fc-d675799f62c0' \
  -d '{ %JSON TABLE% }'
```
- Import extracted groundtruth tables by table extraction tools.
```sh
curl -X POST \
  http://localhost:9696/processPdf2Table \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 799434b7-42b2-8e72-55ab-aaa70c9bc213' \
  -d '{
	"type" : "groundtruth",
	"importToDb" : "true",
	"sourcePath" : "%Path of the file%"
}'
```
- Start the scoring calculation.
```sh
curl -X POST \
  http://localhost:9696/calcPdfGenieScore \
  -H 'cache-control: no-cache' \
  -H 'postman-token: 1eab4757-4d8b-907e-2f8a-834d53bcfe94'
```

Note: This process can be done by PDF2Table the same way.

## Evaluation
The process to import all extracted PDF files into Solr.
- Create two cores into solr, with names: pdf2table and pdfgenie.
- Process all PDF files with PDFGenie and PDF2Table and store the files in a folder.
- Import pdfgenie tables into solr.
- - Step 1: Preprocess all files to extract tables from HTML files and convert into JSON.
```sh
curl -X POST \
  http://localhost:9696/pdfgenieprocess \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: c3a7f55b-c719-30bb-5e75-3deb74630641' \
  -d '{
	"type" : "default",
	"importToDb" : "true",
	"sourcePath" : "/home/amin/Documents/amin/classification/finalHtmlFiles_pdfgenie/all/"
}'
```
- - Step 2: Import Into Solr:
```sh
curl -X POST \
  http://localhost:9696/importtosolr \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: e47e8dca-d27f-7a01-0acf-b397c528e2ce' \
  -d '{
	"core" : "pdfgenie",
	"processortype": "pdfgenie"
}'
```

Note: This can be done the same way with pdf2table.

