# wdps2019

Web Data Processing Systems 2019 (VU course XM_40020)

# Assignment: Large Scale Entity Linking

The assignment for this course is to perform [Entity
Linking](https://en.wikipedia.org/wiki/Entity_linking) on a collection of web
pages using entities from Freebase. Your solution should be scalable and
accurate, and conform to the specifications below. You should work in groups of
4 people. You can use *any existing languages or tools you want*, as long as
it's easy for us to run it on the DAS-4 cluster. Of course, your solution is
not allowed to call web services over the internet. You are encouraged to use
the technologies covered in the lectures.

Your program should receive in input a gzipped [WARC
file](https://en.wikipedia.org/wiki/Web_ARChive), and returns in output a
three-column tab-separated file with document IDs (i.e., the content of the field 'WARC-Record-ID'), entity surface forms (like
"Berlin"), and Freebase entity IDs (like "/m/03hrz"). There is a sample file of
the input (warc) and output (tsv) formats in the `data` directory. Your
program must be runnable on the [DAS-4 cluster](https://www.cs.vu.nl/das4/)
using a bash script, and you should provide a README file with a description of
your approach. For example, your program could be run using the command `bash
run.sh input.warc.gz > output.tsv`.

The performance of your solution will be graded on three dimensions:
Compliance (20%), scalability (20%) and quality (60%).

## Compliance

Does the program that you deliver complies with the specifications of the
assignment? To measure this aspect, we will evaluate to what extent your
program can be run easily on the DAS-4 and whether it produces the output as
specified above. Points will be detracted if your program does not compile, if
it requires extensive and elaborate installation procedures, whether it
produces the output in an incorrect format, etc.

## Scalability

Your solution should be able to be executed on large volumes of data. You can
improve the scalability either by using frameworks like Spark to parallelize
the computation, and/or by avoiding to use very complex algorithms that are
very slow. To measure this aspect, we will evaluate whether you make use of big
data frameworks, and test how fast your algorithm can disambiguate some example
web pages.

## Quality

Your solution should be able to correctly disambiguate as many entities as
possible. To measure the quality of your solution, we will use the [F1
score](https://en.wikipedia.org/wiki/F1_score) on some test webpages (these
webpages are not available to the students).

# Starting code

To help you with the development of the assignment, we provide some example
Python code in the directory "/home/jurbani/wdps/" in the DAS-4 cluster. This
code is also available [here](https://github.com/karmaresearch/wdps). Note that
you do not have to write your program in Python. As mentioned above, you can
use whatever language you want.

We have loaded four major KBs into a triple store called "Trident". The KBs are
Freebase, YAGO, Wikidata, and DBPedia. You can access these KBs with SPARQL
queries.  To start the triple store, you can use the script
"start_sparql_server.sh".  This script will start the triple store in a node so
that you can query it (if you want) during the disambiguation process. In
principle, the triple store can be accessed with a command like : `curl -XPOST
-s 'http://<host>:8082/sparql' -d "print=true&query=SELECT * WHERE { ?s ?p ?o .
} LIMIT 10"`. To experiment with some SPARQL examples, see
https://query.wikidata.org/ . Both services return JSON. Because Freebase was
integrated into the Google Knowledge Graph, you can look up IDs on Google using
URLs like this: [http://g.co/kg/m/03hrz].

In order to facilitate syntactic matching between the entities in the webpage
and the ones in the KB, we indexed all the Freebase labels in
[Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/index.html).
With this program, you can retrieve all the entities IDs that match a given
string. The elasticsearch server can be started on a DAS-4 node with the
commands shown in the file start_elasticsearch_server.sh. Once the server is
started, it can be accessed from the command line like this: `curl
"http://<host>:9200/freebase/label/_search?q=obama"` . 

# Frequently Asked Questions

## How can I get access to the DAS-4 cluster?

I will create an account for each group in this course. In addition, you can
ask Kees Verstoep (c.verstoep@vu.nl) to create personal accounts in case you
need them.

## I cannot access the DAS-4 cluster ...

The DAS-4 cluster is accessible only within the VU campus. It can also be
accessed from home, but this requires a SSH tunnelling via ssh.data.vu.nl.
Unfortunately, I cannot help you with setting up SSH or other types of
connections.

## All the machines are occupied!

The DAS-4 contains more than 60 machines. Since there are about 30 groups, it
means that there should be at least two machines per group. However, it could
be that some groups decide to use more machines, especially towards the end of
the course. In case the cluster is overloaded, we will block groups to use more
than 3 machines, but this process might take some time. My advice is to not
start late with the assignment. No extension will be given if the cluster is
overloaded. I also suggest that you move a part of your development on your
laptops and use the DAS-4 only for testing the final prototype.

## Python3 misses some libraries

If you need to install external libraries on python, you can use the utility
pip. You must make sure that the libraries are installed in your home
directory. For instance, the script "start_sparql_server.sh" requires the
library "requests". To install it, type the command "pip3 install --user
requests".

## How can we get more results from Freebase?

You can increase the number of results with the "size" parameter (see
[Elasticsearch
documentation](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/index.html)),
and you can look up which entity is probably the Obama that is meant by
querying the SPARQL endpoint (e.g. which entity has the most facts about it).
E.g. `curl -s
"http://10.149.0.127:9200/freebase/label/_search?q=obama&size=1000"` .

## Why doesn't this SPARQL query work?

Not all SPARQL features are implemented in Trident. In particular, string
filtering functions are not present (such as `langMatches`). Instead, try to
write SPARQL queries with possibly many results, and filter them in your own
code.

## What should we write in the README of our submission?

Please describe briefly how your system works, which existing tools you have
used and why, and how to run your solution.

## We have reached out disk quota on DAS-4, what do we do?

You should always use the larger scratch disk on `/var/scratch/wdps19XX`.

## Should we detect entities in non-English text?

No, you only have to detect entities in English text.
