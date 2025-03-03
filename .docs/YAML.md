---
layout: page
title: "First Attempt to Write a YAML in OpenAPI"
permalink: /yaml
---

# eutils.yml
**A Work in Progress**
```
---
openapi: 3.0.0
info:
  title: E-Utilities - OpenAPI 3.0
  description: |-
    This is a sample E-Utilities Server based on the OpenAPI 3.0 specification.  I copied a template from Swagger at [https://swagger.io](https://swagger.io) to get started.
    Some useful links:
    - [save for later](http address here)
    - [save for later](http address here)
  termsOfService: [link to terms of service here](http address)
  contact:
    email: jenniferpetroffsmith@gmail.com
  license:
    name: Apache 2.0
    url: http://www.apache.org/licenses/LICENSE-2.0.html
  version: 1.0.11
security:
  - api_key: []
externalDocs:
  description:  A General Introduction to the E-utilities  
  url: [A General Introduction to the E-utilities]([https://www.ncbi.nlm.nih.gov/books/NBK25500/](https://www.ncbi.nlm.nih.gov/books/NBK25497/) )
servers:
  - url: https://eutils.ncbi.nlm.nih.gov/entrez/eutils
    description: base URL
tags:
  - name: esearch
    description: Finds text in one NCBI databases
    externalDocs:
      description: Find out more
      url: https://github.com/jenpetsmit/eutilities/blob/main/eutils/esearch.md
  - name: esummary
    description: Returns document summaries for each UID
    externalDocs:
      description: Description of ESummary
      url: https://github.com/jenpetsmit/eutilities/blob/main/eutils/esummary.md
  - name: einfo
    description: Retrieves information and statistics about a single database
    externalDocs:
      description: Find out more
      url: https://github.com/jenpetsmit/eutilities/blob/main/eutils/einfo.md
  - name: egquery
    description: Searches a text query in all NCBI databases and returns the number of results [IN THE FORM OF UID PERHAPS?] in each database
    externalDocs:
      description: Find out more
      url: https://github.com/jenpetsmit/eutilities/blob/main/eutils/egquery.md
  - name: efetch
    description: Retrieves on screen or downloads full records for each UID
    externalDocs:
      description: Find out more
      url: https://github.com/jenpetsmit/eutilities/blob/main/eutils/efetch.md    
  - name: elink
    description: Retrieves UIDs for related or linked records, or LinkOut URLs (URLs that link outside NCBI databases) 
    externalDocs:
      description: Find out more
      url: https://github.com/jenpetsmit/eutilities/blob/main/eutils/elink.md
  - name: espell
    description: Retrieves spelling suggestions for a text query
    externalDocs:
      description: Find out more
      url: https://github.com/jenpetsmit/eutilities/blob/main/eutils/espell.md 
  - name: ecitmatch
    description: Searches PubMed for a series of citation strings  
    externalDocs:
      description: Find out more
      url: https://github.com/jenpetsmit/eutilities/blob/main/eutils/ecitmatch.md
  - name: epost
    description: Stores a list of UIDs to use with other operations (get, post, etc)
    externalDocs:
      description: Find out more
      url: https://github.com/jenpetsmit/eutilities/blob/main/eutils/epost.md
paths:

 /esearch.fcgi?: 
    get:
      tags: 
        - esearch
      summary: queries for text in a single database
      description: Multiple texts can be provided with comma separated strings
      operationId: findtextByDatabase
      parameters:
        - name: db
          in: query
          description: target database
          required: true
          explode: true
          schema:
            type: string
        - name: term
          in: query
          description: the term or phrase to search for
          required: true
          explode: true
          schema:
           type: string
        - $ref: '#/components/parameters/retmode'
        - $ref: '#/components/parameters/rettype'  
    security:
      -api_key: []
 responses:
        "200":
          description: ESummary downloaded
          content: 
            application/xml:
              schema:
                type: integer 
        "401":
          description: uh oh

 /esummary.fcgi:
    get:
      tags: 
        - esummary
      summary: Downloads document summaries of one ore more (comma separated) UIDs for a single database (db)  
      description: Downloads document summaries of one ore more (comma separated) UIDs for a single database (db) or if information was saved to the History server in a previous query,  for that set or UIDs stored on History Server
      operationId: docsumByUID
      parameters:
        - name: db
          in: query
          description: target database
          required: true
          explode: true
          schema:
            type: string
        - name: id
          in: query
          description: the UID
          required: true
          explode: true
          schema:
            type: integer
        - name: usehistory
          in: query
          description: save results to history server | use with WebEnv
          required: false
          explode: true
          schema: 
            type: boolean
        - name: WebEnv
          in: query
          description: saved session string from esearch, epost, or elink | Needs query_key and usehistory
          required: false
          explode: false
          schema:  
            type: string
        - name: query_key
          in: query
          description: integer query key returned by previouse esearch, epost, or elink | Needs WebEnv
          required: false
          schema:  
           type: integer
        - $ref: '#/components/parameters/WebEnv'
        - $ref: '#/components/parameters/usehistory'
        - $ref: '#/components/parameters/retmax'
        - $ref: '#/components/parameters/datetype'
        - $ref: '#/components/parameters/strand'
        - $ref: '#/components/parameters/seq_start'
        - $ref: '#/components/parameters/seq_stop'
        - $ref: '#/components/parameters/complexity'
        - $ref: '#/components/parameters/retstart'  
        - $ref: '#/components/parameters/retmax'
        - $ref: '#/components/parameters/version'
        - $ref: '#/components/parameters/rettype' 
        - name: retmode
          in: query
          description: Specifies the method used to sort UIDs in the ESearch output. 
          explode: false
          schema:  
           type: array
           items:
            enum:  # enum restricts to specific values  BUT THIS IS FOR db=pubmed MED ONLY
              - pub_date
              - Author
              - JournalName
              - relevance
      security:
        -api_key: []
      responses:
        "200":
          description: ESummary downloaded, originally designed for PubMed articles, but has been expanded to to other databases where the fields of the document summary vary by database
          content: 
            application/xml:
              schema:
                type: string
            application/json:
              schema:
                type: string
            text/html:
              schema:
                type:string
            text/*
              schema:
                type: string
        "401":
          description: uh oh
 /efetch.fcgi:
    get:
      tags: 
        - efetch
      summary: Returns on screen or downloads full records in a variety of formats from an UID or list of comma-separate UIDs.
      description: Returns on screen or downloads full records in a variety of formats from an UID or list of comma-separate UIDs or set of UIDs from history server. Not all databases are supported. EFetch can retrieve GI numbers records by including their accession.version identifier in the id parameter. For example  - efetch.fcgi?db={database}&id={uid_list}&rettype={retrieval_type}&retmode={retrieval_mode}. For sequence databases (nuccore, popset, protein), the UID list may be a mixed list of GI numbers and accession.version identifiersThere is no set maximum for the number of UIDs that can be passed to EFetch, but if more than about 200 UIDs are to be provided, the request should be made using the HTTP POST method
      operationId: efetchdwnload
      parameters:
        - name: db
          in: query
          description: target database
          required: true
          explode: true
          schema:
            type: string
        - name: id
          in: query
          description: the UID
          required: true
          explode: true
          schema:
            type: string
        - $ref: '#/components/parameters/WebEnv'
        - $ref: '#/components/parameters/usehistory'
        - $ref: '#/components/parameters/retmax'
        - $ref: '#/components/parameters/datetype'
        - $ref: '#/components/parameters/strand'
        - $ref: '#/components/parameters/seq_start'
        - $ref: '#/components/parameters/seq_stop'
        - $ref: '#/components/parameters/complexity'
        - $ref: '#/components/parameters/retmode'
        - $ref: '#/components/parameters/rettype'  
      security:
        -api_key: []
      responses:
        "200":
          description: EFetch records, results vary by databases's fields. 
          content: 
            application/xml:
              schema:
                type: string
            application/json:
              schema:
                type: string
            text/html:
              schema:
                type:string
            text/*
              schema:
                type: string
        '400':
          description: limitations vary with database. See [Documentation](https://github.com/jenpetsmit/eutilities/blob/main/eutils/efetch.md) for details
        '404':
          description: User not found
 /egquery.fcgi:
    get:     
      summary: Entrez text query in all Entrez databases
      description: Searches a text query in all Entrez databases and returns the number of results for the query in each database. All special characters must be URL encoded. Spaces may be replaced by '+' signs. 
      parameters:
        - name: term
          in: query
          description: Entrez text query
          explode: true
          required: false
          schema: 
            type: string
      responses:
        '200':
          description: Ok
 /einfo.fcgi:
    get:     
      summary: Provides a list of the names of all valid Entrez databases or Provides statistics for a single database
      description: Provides a list of the names of all valid Entrez databases or when given a database, provides database statistics including lists of indexing fields and available link names
      parameters:
        - name: db
          in: query
          description: target database about which to gather statistics
          explode: false
          required: false
          schema: 
            type: string
        - name: version
          in: query
          description: Used to specify version 2.0 EInfo XML. The only supported value is ‘2.0’. 
          explode: false
          required: false
          schema: 
            type: string
          examples:  # trying to figure out if I can add a link to the Version parameter - not sure if it works
            link:
              summary: "See detailed information about version parametmer with respect to EInfo"
              externalValue: https://github.com/jenpetsmit/eutilities/blob/main/eutils/einfo.md
      security:
        -api_key: []
      responses:
        '200':
          description: Ok
 /elink.fcgi:
   get:     
      summary: Retrieves UIDs for related or linked records, or LinkOut URLs.
      description: Provides a list of the names of all valid Entrez databases or when given a database, provides database statistics including lists of indexing fields and available link names
      parameters:
        - name: db
          in: query
          description: Target database from which to retrieve UIDs. This is the origin database of the link operation. If db and dbfrom are set to the same database value, then ELink will return computational neighbors within that database.
          explode: true
          required: false
          schema: 
            type: string
        - name: dbfrom
          in: query
          description: Source database with the input UIDs
          explode: false
          required: true
          schema: 
            type: string
        - name: cmd
          in: query
          description: ELink command mode
          explode: false
          required: true
          schema: 
            type: array
            items:
              type: string
              enum:        # enum restricts to specific values  
                - neighbor
                - neighbor_score
                - neighbot_history
                - acheck
                - ncheck
                - lcheck
                - llinks
                - llinkslib
                - prlinks
        - name: id
          in: query
          description: A single UID or a comma-separated UID list from the same database (dbfrom)
          explode: true
          required: false
          schema: 
            type: string
          example: https://eutils.ncbi.nlm.nih.gov/entrez/eutils/elink.fcgi?dbfrom=nuccore&db=nuccore&id=34577062
        - $ref: '#/components/parameters/query_key'
        - $ref: '#/components/parameters/WebEnv'
        - $ref: '#/components/parameters/retmode'
        - $ref: '#/components/parameters/idtype'   #  add this to components
        - $ref: '#/components/parameters/linkname' # add this to components
        - $ref: '#/components/parameters/term'
        - name: holding
          in: query
          description: Only URLs for the LinkOut provider specified by holding will be returned. The value provided to holding should be the abbreviation of the LinkOut provider's name found in the <NameAbbr> tag of the ELink XML output when cmd is set to llinks or llinkslib. The holding parameter only functions when cmd is set to llinks or llinkslib.
          explode: true
          required: false
          schema:
            type: string
        - $ref: '#/components/parameters/datetype'
        - $ref: '#/components/parameters/reldate'
        - $ref: '#/components/parameters/mindate'
        - $ref: '#/components/parameters/maxdate'
      security:
        -api_key: []
      responses:
          '200':
            description:  200 response It works
            content:  # Response body
              application/xml: 
                schema: 
                  type: object
                  properties:
                    id: 
                      type: string 
 /espell.fcgi:
   get:     
      summary: Retrieves UIDs for related or linked records, or LinkOut URLs.
      description: Provides a list of the names of all valid Entrez databases or when given a database, provides database statistics including lists of indexing fields and available link names
      parameters:
        - name: db
          in: query
          description: Target database to search
          explode: true
          required: true
          schema: 
            type: string
        - name: term
          in: query
          description: Entrez text query. All special characters must be URL encoded. Spaces may be replaced by '+' signs. 
          explode: true
          required: true
          schema:
           type: string
      security:
        -api_key: []
      responses:
          '200':
            description:  200 response It works
 /ecitmatch.fcgi:
   get:     
      summary: Retrieves PubMed IDs (PMIDs) that correspond to a set of input citation strings.
      description: Retrieves PubMed IDs (PMIDs) that correspond to a set of input citation strings. 
      parameters:
        - name: db
          in: query
          description: Target database to search. The only supported value is pubmed. Example db=pubmed
          explode: true
          required: true
          schema: 
            type: string
            enum: 
              - pubmed
        - name: rettype
          in: query
          description: ERetrieval type. The only supported value is ‘xml’. example retye=xml
          explode: true
          required: true
          schema:
           type: string
        - name: bdata
          in: query
          description: Citation strings. Each input citation must be represented by a citation string in the following format- journal_title|year|volume|first_page|author_name|your_key|
          explode: true
          required: true
          schema:
           type: string
      security:
        -api_key: []
      responses:
          '200':
            description:  200 response It works
          '401':
            description: database must be pubmed, example, db=pubmed

components:  
  parameters:
    db:
      name: database
      in: query
      description: the abbreviated name of the NCBI database to be queried.
      required: false
      explode: true
      schema:
        type: string
    term:
      name: term
      in: query
      description: the term or phrase for which to search
      required: true
      explode: true
      schema:
         type: string
    usehistory:
      name: Use history
      in: query
      description: saves results to history server
      required: false
      explode: true
      schema: 
        type: string
    WebEnv:
      name: Web Environment
      in: query
      description: saved session string from esearch, epost, or elink
      required: false
      explode: false
      schema:  
        type: string
    query_key:
      name: Query key
      in: query
      description: integer query key returned by previouse esearch, epost, or elink
      required: false
      explode: false
      schema:  
        type: integer
    retmax:
      name: Retrival maximum  
      in: query
      description: Total number of UIDs from the retrieved set to be shown in the XML output (default=20). By default, ESearch only includes the first 20 UIDs retrieved in the XML output. If usehistory is set to 'y', the remainder of the retrieved set will be stored on the History server; otherwise these UIDs are lost. Increasing retmax allows more of the retrieved UIDs to be included in the XML output, up to a maximum of 10,000 records. 
      required: false
      explode: false
      schema:  
        type: string
    retstart:
      name: Retrival start UID  
      in: query
      description: Sequential index of the first UID in the retrieved set to be shown in the XML output (default=0, corresponding to the first record of the entire set). This parameter can be used in conjunction with retmax to download an arbitrary subset of UIDs retrieved from a search.
      required: false
      explode: false
      schema:  
        type: integer
    rettype:
      name: Retrival Type  
      in: query
      description: Specifies the type of return but options vary by database, for example flat file, FASTA, EST, or accession, abstract or MEDLINE from PubMed, or GenPept or FASTA from protein. See the Parameters web page for a comlete list.
      explode: false
      schema:  
        type: string
    retmode:
      name: Retrival mode  
      in: query
      description: Determines the format of the returned output. The default value is xml for ESearch XML; json is also supported to return output in JSON format; efetch supports remode-text.
      explode: false
      schema:  
        type: string
    sort:
      name: Retrival mode  
      in: query
      description: Specifies the method used to sort UIDs in the ESearch output. 
      required: false
      explode: false
      schema:  
        type: array
        items:
          type: string
          enum:        # enum restricts to specific values  BUT THIS IS FOR PUB MED ONLY
            - pub_date
            - Author
            - JournalName
            - relevance   
    strand:
      name: strand
      in: query
      description: Strand of DNA to retrieve. Available values are "1" for the plus strand and "2" for the minus strand.
      required: false
      explode: false
      schema:
        type: integer
        example: db=nuccore&id=21614549&strand=1&seq_start=1&seq_stop=100&rettype=fasta&retmode=text
    seq_start:  
      name: sequence start
      in: query
      description: First sequence base to retrieve. The value should be the integer coordinate of the first desired base, with "1" representing the first base of the sequence.
      required: false
      explode: false
      schema:
        type: integer
        example: db=nuccore&id=21614549&strand=1&seq_start=1&seq_stop=100&rettype=fasta&retmode=text
    seq_stop:
      name: strand # 
      in: query
      description: Last sequence base to retrieve. The value should be the integer coordinate of the last desired base, with "1" representing the first base of the sequence.
      schema:
        type: integer
        example: db=nuccore&id=21614549&strand=1&seq_start=1&seq_stop=100&rettype=fasta&retmode=text
    complexity:
      name: strand   # 
      in: query
      description: 
      schema:
        type: string
        enum:
          - 0
            - description: entire blob
          - 1
            - description: bioseq
          - 2
            - description: minimal bioseq-set
          - 3
            - description: minimal nuc-prot
          -4
            - description: minimal pub-set
    version:  # 
      name: version
      in: query
      required: false
      description: Used to specify version 2.0 ESummary XML. When present, ESummary will return version 2.0 DocSum XML that is unique to each Entrez database and that often contains more data than the default DocSum XML.
      schema:
        type: string
        example: https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esummary.fcgi?db=pubmed&id=11850928,11482001&version=2.0
    idtype:
      name: idtype
      in: query
      required: false
      description: Specifies the type of identifier to return for sequence databases (nuccore, popset, protein). By default, ELink returns GI numbers in its output. If idtype is set to ‘acc’, ELink will return accession.version identifiers rather than GI numbers.
      schema:
        type: string
    linkname:
      name: linkname
      in: query
      required: false
      explode: false
      description: Name of the Entrez link to retrieve. Every link in Entrez is given a name of the form dbfrom_db_subset
      schema:
        type: string
        example: Find snps with genotype data linked to genes. <br> https://eutils.ncbi.nlm.nih.gov/entrez/eutils/elink.fcgi?dbfrom=gene&db=snp&id=93986&linkname=gene_snp_genegenotype
    datetype:
      name: datetype
      in: query
      required: false
      explode: false
      description: Type of date used to limit a link operation. The allowed values vary between Entrez databases, but common values are 'mdat' (modification date), 'pdat' (publication date) and 'edat' (Entrez date). Generally an Entrez database will have only two allowed values for datetype.
      schema:
        type: string
    reldate:
      name: reldate
      in: query
      required: false
      explode: false
      description: When reldate is set to an integer n, ELink returns only those items that have a date specified by datetype within the last n days.
      schema: 
        type: string
    mindate:
      name: mindate 
      in: query
      required: false
      explode: false
      description: Beginning of date range  used to limit a link operation by the date specified by datetype. Mindate and maxdate must be used together to specify an arbitrary date range. The general date format is YYYY/MM/DD, and these variants are also allowed - YYYY, YYYY/MM.
      schema: 
        type: string
    maxdate:
      name: maxdate
      in: query
      required: false
      explode: false
      description: End of date range  used to limit a link operation by the date specified by datetype. Mindate and maxdate must be used together to specify an arbitrary date range. The general date format is YYYY/MM/DD, and these variants are also allowed- YYYY, YYYY/MM.
      schema: 
        type: string
    query_key:
      name: Query Key
      in: query
      required: false
      explode: false
      type: string
    api_key:
      name API key  
      in: query
      reaquired: false
      explose: false
      type: sting
      description: Only needed if access to E-Utilities is more than three requests per second. Users can get an API key from their NCBI account, from the Settings page.
 responses:
    205:
      description: all good
    IllegalInput:
      description: Illegal input for operation.
    GeneralError:
       description: General Error
    MissingParameter:
      description: Either usehistory, query_key, retmax  
    405:
       description: Invalid status value
       content:
          application/xml:
            schema:
              type: array
              items:
                enum:
                 - placed
                 - approved
                 - delivered
    
  securitySchemes:
    api_key:
      description: Only needed if access to E-Utilities is more than three requests per second. Users can get an API key from their NCBI account, from the  Settings page.
      type: apiKey
      name: api_key
      in: header
```
