[SDS-2.2, Scalable Data Science](https://lamastex.github.io/scalable-data-science/sds/2/2/)
===========================================================================================

Archived YouTube videos of this live unedited lab-lecture:

[![Archived YouTube video of this live unedited lab-lecture](http://img.youtube.com/vi/3vxH9SGt37E/0.jpg)](https://www.youtube.com/embed/3vxH9SGt37E?start=988&end=2073&autoplay=1) [![Archived YouTube video of this live unedited lab-lecture](http://img.youtube.com/vi/hITYiBKdf5A/0.jpg)](https://www.youtube.com/embed/hITYiBKdf5A?start=0&end=1914&autoplay=1)

Word Count on US State of the Union (SoU) Addresses
===================================================

-   Word Count in big data is the equivalent of `Hello World` in programming
-   We count the number of occurences of each word in the first and last (2016) SoU addresses.

**prerequisite** see **DO NOW** below. You should have loaded data as instructed in `scalable-data-science/xtraResources/sdsDatasets`.

#### DO NOW (if not done already)

In your databricks community edition:

1.  In your `WorkSpace` create a Folder named `scalable-data-science`
2.  `Import` the databricks archive file at the following URL:
    -   <https://github.com/lamastex/scalable-data-science/raw/master/dbcArchives/2017/parts/xtraResources.dbc>
3.  This should open a structure of directories in with path: `/Workspace/scalable-data-science/xtraResources/`

An interesting analysis of the textual content of the *State of the Union (SoU)* addresses by all US presidents was done in:

-   [Alix Rule, Jean-Philippe Cointet, and Peter S. Bearman, Lexical shifts, substantive changes, and continuity in State of the Union discourse, 1790–2014, PNAS 2015 112 (35) 10837-10844; doi:10.1073/pnas.1512221112](http://www.pnas.org/content/112/35/10837.full).

![](http://www.pnas.org/content/112/35/10837/F5.large.jpg)

[Fig. 5](http://www.pnas.org/content/112/35/10837.full). A river network captures the flow across history of US political discourse, as perceived by contemporaries. Time moves along the x axis. Clusters on semantic networks of 300 most frequent terms for each of 10 historical periods are displayed as vertical bars. Relations between clusters of adjacent periods are indexed by gray flows, whose density reflects their degree of connection. Streams that connect at any point in history may be considered to be part of the same system, indicated with a single color.

Let us investigate this dataset ourselves!
------------------------------------------

1.  We first get the source text data by scraping and parsig from <http://stateoftheunion.onetwothree.net/texts/index.html> as explained in
    [scraping and parsing SoU addresses](/#workspace/scalable-data-science/xtraResources/sdsDatasets/scraperUSStateofUnionAddresses).

-   This data is already made available in DBFS, our distributed file system.
-   We only do the simplest word count with this data in this notebook and will do more sophisticated analyses in the sequel (including topic modeling, etc).

Project Suggestion
------------------

**Streaming/NLP/Vertex-Programs, etc**:

-   [project: MEP - meme Evolution Programme](https://lamastex.github.io/scalable-data-science/sds/research/mep/) - just won (2017-2018) AWS Cloud Computing Credits for research grant.
-   [The GDELT Project: Watching our World Unfold](https://www.gdeltproject.org/)

Key Data Management Concepts
----------------------------

### The Structure Spectrum

**(watch now 1:10)**:

[![Structure Spectrum by Anthony Joseph in BerkeleyX/CS100.1x](http://img.youtube.com/vi/pMSGGZVSwqo/0.jpg)](https://www.youtube.com/watch?v=pMSGGZVSwqo?rel=0&autoplay=1&modestbranding=1&start=1&end=70)

Here we will be working with **unstructured** or **schema-never** data (plain text files).
\*\*\*

### Files

**(watch later 1:43)**:

[![Files by Anthony Joseph in BerkeleyX/CS100.1x](http://img.youtube.com/vi/NJyBQ-cQ3Ac/0.jpg)](https://www.youtube.com/watch?v=NJyBQ-cQ3Ac?rel=0&autoplay=1&modestbranding=1&start=1)

### DBFS and dbutils - where is this dataset in our distributed file system?

-   Since we are on the databricks cloud, it has a file system called DBFS
-   DBFS is similar to HDFS, the Hadoop distributed file system
-   dbutils allows us to interact with dbfs.
-   The 'display' command displays the list of files in a given directory in the file system.

``` scala
display(dbutils.fs.ls("dbfs:/datasets/sou")) // Cntrl+Enter to display the files in dbfs:/datasets/sou
```

| path                            | name         | size    |
|---------------------------------|--------------|---------|
| dbfs:/datasets/sou/17900108.txt | 17900108.txt | 6725.0  |
| dbfs:/datasets/sou/17901208.txt | 17901208.txt | 8427.0  |
| dbfs:/datasets/sou/17911025.txt | 17911025.txt | 14175.0 |
| dbfs:/datasets/sou/17921106.txt | 17921106.txt | 12736.0 |
| dbfs:/datasets/sou/17931203.txt | 17931203.txt | 11668.0 |
| dbfs:/datasets/sou/17941119.txt | 17941119.txt | 17615.0 |
| dbfs:/datasets/sou/17951208.txt | 17951208.txt | 12296.0 |
| dbfs:/datasets/sou/17961207.txt | 17961207.txt | 17340.0 |
| dbfs:/datasets/sou/17971122.txt | 17971122.txt | 12473.0 |
| dbfs:/datasets/sou/17981208.txt | 17981208.txt | 13394.0 |
| dbfs:/datasets/sou/17991203.txt | 17991203.txt | 9236.0  |
| dbfs:/datasets/sou/18001111.txt | 18001111.txt | 8382.0  |
| dbfs:/datasets/sou/18011208.txt | 18011208.txt | 19342.0 |
| dbfs:/datasets/sou/18021215.txt | 18021215.txt | 13003.0 |
| dbfs:/datasets/sou/18031017.txt | 18031017.txt | 14022.0 |
| dbfs:/datasets/sou/18041108.txt | 18041108.txt | 12652.0 |
| dbfs:/datasets/sou/18051203.txt | 18051203.txt | 17190.0 |
| dbfs:/datasets/sou/18061202.txt | 18061202.txt | 17135.0 |
| dbfs:/datasets/sou/18071027.txt | 18071027.txt | 14334.0 |
| dbfs:/datasets/sou/18081108.txt | 18081108.txt | 16225.0 |
| dbfs:/datasets/sou/18091129.txt | 18091129.txt | 11050.0 |
| dbfs:/datasets/sou/18101205.txt | 18101205.txt | 15028.0 |
| dbfs:/datasets/sou/18111105.txt | 18111105.txt | 13941.0 |
| dbfs:/datasets/sou/18121104.txt | 18121104.txt | 19615.0 |
| dbfs:/datasets/sou/18131207.txt | 18131207.txt | 19532.0 |
| dbfs:/datasets/sou/18140920.txt | 18140920.txt | 12632.0 |
| dbfs:/datasets/sou/18151205.txt | 18151205.txt | 19398.0 |
| dbfs:/datasets/sou/18161203.txt | 18161203.txt | 20331.0 |
| dbfs:/datasets/sou/18171212.txt | 18171212.txt | 26236.0 |
| dbfs:/datasets/sou/18181116.txt | 18181116.txt | 26445.0 |

Truncated to 30 rows

Let us display the *head* or the first few lines of the file `dbfs:/datasets/sou/17900108.txt` to see what it contains using `dbutils.fs.head` method.
`head(file: String, maxBytes: int = 65536): String` -&gt; Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
as follows:

``` scala
dbutils.fs.head("dbfs:/datasets/sou/17900108.txt",673) // Cntrl+Enter to get the first 673 bytes of the file (which corresponds to the first five lines)
```

>     [Truncated to first 673 bytes]
>     res1: String = 
>     "George Washington 
>
>     January 8, 1790 
>     Fellow-Citizens of the Senate and House of Representatives: 
>     I embrace with great satisfaction the opportunity which now presents itself of congratulating you on the present favorable prospects of our public affairs. The recent accession of the important state of North Carolina to the Constitution of the United States (of which official information has been received), the rising credit and respectability of our country, the general and increasing good will toward the government of the Union, and the concord, peace, and plenty with which we are blessed are circumstances auspicious in an eminent degree to our national prosperity. "

##### You Try!

Uncomment and modify `xxxx` in the cell below to read the first 1000 bytes from the file.

``` scala
//dbutils.fs.head("dbfs:/datasets/sou/17900108.txt", xxxx) // Cntrl+Enter to get the first 1000 bytes of the file
```

### Read the file into Spark Context as an RDD of Strings

-   The `textFile` method on the available `SparkContext` `sc` can read the text file `dbfs:/datasets/sou/17900108.txt` into Spark and create an RDD of Strings
    -   but this is done lazily until an action is taken on the RDD `sou17900108`!

``` scala
val sou17900108 = sc.textFile("dbfs:/datasets/sou/17900108.txt") // Cntrl+Enter to read in the textfile as RDD[String]
```

>     sou17900108: org.apache.spark.rdd.RDD[String] = dbfs:/datasets/sou/17900108.txt MapPartitionsRDD[17803] at textFile at <console>:34

### Perform some actions on the RDD

-   Each String in the RDD `sou17900108` represents one line of data from the file and can be made to perform one of the following actions:
    -   count the number of elements in the RDD `sou17900108` (i.e., the number of lines in the text file `dbfs:/datasets/sou/17900108.txt`) using `sou17900108.count()`
    -   display the contents of the RDD using `take` or `collect`.

``` scala
sou17900108.count() // <Shift+Enter> to count the number of elements in the RDD
```

>     res47: Long = 23

``` scala
sou17900108.take(5) // <Shift+Enter> to display the first 5 elements of RDD
```

>     res48: Array[String] = Array("George Washington ", "", "January 8, 1790 ", "Fellow-Citizens of the Senate and House of Representatives: ", "I embrace with great satisfaction the opportunity which now presents itself of congratulating you on the present favorable prospects of our public affairs. The recent accession of the important state of North Carolina to the Constitution of the United States (of which official information has been received), the rising credit and respectability of our country, the general and increasing good will toward the government of the Union, and the concord, peace, and plenty with which we are blessed are circumstances auspicious in an eminent degree to our national prosperity. ")

``` scala
sou17900108.take(5).foreach(println) // <Shift+Enter> to display the first 5 elements of RDD line by line
```

>     George Washington 
>
>     January 8, 1790 
>     Fellow-Citizens of the Senate and House of Representatives: 
>     I embrace with great satisfaction the opportunity which now presents itself of congratulating you on the present favorable prospects of our public affairs. The recent accession of the important state of North Carolina to the Constitution of the United States (of which official information has been received), the rising credit and respectability of our country, the general and increasing good will toward the government of the Union, and the concord, peace, and plenty with which we are blessed are circumstances auspicious in an eminent degree to our national prosperity. 

``` scala
sou17900108.collect // <Cntrl+Enter> to display all the elements of RDD
```

>     res50: Array[String] = Array("George Washington ", "", "January 8, 1790 ", "Fellow-Citizens of the Senate and House of Representatives: ", "I embrace with great satisfaction the opportunity which now presents itself of congratulating you on the present favorable prospects of our public affairs. The recent accession of the important state of North Carolina to the Constitution of the United States (of which official information has been received), the rising credit and respectability of our country, the general and increasing good will toward the government of the Union, and the concord, peace, and plenty with which we are blessed are circumstances auspicious in an eminent degree to our national prosperity. ", "In resuming your consultations for the general good you can not but derive encouragement from the reflection that the measures of the last session have been as satisfactory to your constituents as the novelty and difficulty of the work allowed you to hope. Still further to realize their expectations and to secure the blessings which a gracious Providence has placed within our reach will in the course of the present important session call for the cool and deliberate exertion of your patriotism, firmness, and wisdom. ", "Among the many interesting objects which will engage your attention that of providing for the common defense will merit particular regard. To be prepared for war is one of the most effectual means of preserving peace. ", "A free people ought not only to be armed, but disciplined; to which end a uniform and well-digested plan is requisite; and their safety and interest require that they should promote such manufactories as tend to render them independent of others for essential, particularly military, supplies. ", "The proper establishment of the troops which may be deemed indispensable will be entitled to mature consideration. In the arrangements which may be made respecting it it will be of importance to conciliate the comfortable support of the officers and soldiers with a due regard to economy. ", "There was reason to hope that the pacific measures adopted with regard to certain hostile tribes of Indians would have relieved the inhabitants of our southern and western frontiers from their depredations, but you will perceive from the information contained in the papers which I shall direct to be laid before you (comprehending a communication from the Commonwealth of Virginia) that we ought to be prepared to afford protection to those parts of the Union, and, if necessary, to punish aggressors. ", "The interests of the United States require that our intercourse with other nations should be facilitated by such provisions as will enable me to fulfill my duty in that respect in the manner which circumstances may render most conducive to the public good, and to this end that the compensation to be made to the persons who may be employed should, according to the nature of their appointments, be defined by law, and a competent fund designated for defraying the expenses incident to the conduct of foreign affairs. ", "Various considerations also render it expedient that the terms on which foreigners may be admitted to the rights of citizens should be speedily ascertained by a uniform rule of naturalization. ", "Uniformity in the currency, weights, and measures of the United States is an object of great importance, and will, I am persuaded, be duly attended to. ", "The advancement of agriculture, commerce, and manufactures by all proper means will not, I trust, need recommendation; but I can not forbear intimating to you the expediency of giving effectual encouragement as well to the introduction of new and useful inventions from abroad as to the exertions of skill and genius in producing them at home, and of facilitating the intercourse between the distant parts of our country by a due attention to the post-office and post-roads. ", "Nor am I less persuaded that you will agree with me in opinion that there is nothing which can better deserve your patronage than the promotion of science and literature. Knowledge is in every country the surest basis of public happiness. In one in which the measures of government receive their impressions so immediately from the sense of the community as in ours it is proportionably essential. ", "To the security of a free constitution it contributes in various ways--by convincing those who are intrusted with the public administration that every valuable end of government is best answered by the enlightened confidence of the people, and by teaching the people themselves to know and to value their own rights; to discern and provide against invasions of them; to distinguish between oppression and the necessary exercise of lawful authority; between burthens proceeding from a disregard to their convenience and those resulting from the inevitable exigencies of society; to discriminate the spirit of liberty from that of licentiousness-- cherishing the first, avoiding the last--and uniting a speedy but temperate vigilance against encroachments, with an inviolable respect to the laws. ", "Whether this desirable object will be best promoted by affording aids to seminaries of learning already established, by the institution of a national university, or by any other expedients will be well worthy of a place in the deliberations of the legislature. ", "Gentlemen of the House of Representatives: ", "I saw with peculiar pleasure at the close of the last session the resolution entered into by you expressive of your opinion that an adequate provision for the support of the public credit is a matter of high importance to the national honor and prosperity. In this sentiment I entirely concur; and to a perfect confidence in your best endeavors to devise such a provision as will be truly with the end I add an equal reliance on the cheerful cooperation of the other branch of the legislature. ", "It would be superfluous to specify inducements to a measure in which the character and interests of the United States are so obviously so deeply concerned, and which has received so explicit a sanction from your declaration. ", "Gentlemen of the Senate and House of Representatives: ", "I have directed the proper officers to lay before you, respectively, such papers and estimates as regard the affairs particularly recommended to your consideration, and necessary to convey to you that information of the state of the Union which it is my duty to afford. ", The welfare of our country is the great object to which our cares and efforts ought to be directed, and I shall derive great satisfaction from a cooperation with you in the pleasing though arduous task of insuring to our fellow citizens the blessings which they have a right to expect from a free, efficient, and equal government.)

### Cache the RDD in (distributed) memory to avoid recreating it for each action

-   Above, every time we took an action on the same RDD, the RDD was reconstructed from the textfile.
    -   Spark's advantage compared to Hadoop MapReduce is the ability to cache or store the RDD in distributed memory across the nodes.
-   Let's use `.cache()` after creating an RDD so that it is in memory after the first action (and thus avoid reconstruction for subsequent actions).
    -   count the number of elements in the RDD `sou17900108` (i.e., the number of lines in the text file `dbfs:/datasets/sou/17900108.txt`) using `sou17900108.count()`
    -   display the contents of the RDD using `take` or `collect`.

``` scala
// Shift+Enter to read in the textfile as RDD[String] and cache it in distributed memory
val sou17900108 = sc.textFile("dbfs:/datasets/sou/17900108.txt")
sou17900108.cache() // cache the RDD in memory
```

>     sou17900108: org.apache.spark.rdd.RDD[String] = dbfs:/datasets/sou/17900108.txt MapPartitionsRDD[17809] at textFile at <console>:37
>     res51: sou17900108.type = dbfs:/datasets/sou/17900108.txt MapPartitionsRDD[17809] at textFile at <console>:37

``` scala
sou17900108.count() // Shift+Enter during this count action the RDD is constructed from texfile and cached
```

>     res52: Long = 23

``` scala
sou17900108.count() // Shift+Enter during this count action the cached RDD is used (notice less time taken by the same command)
```

>     res53: Long = 23

``` scala
sou17900108.take(5) // <Cntrl+Enter> to display the first 5 elements of the cached RDD
```

>     res54: Array[String] = Array("George Washington ", "", "January 8, 1790 ", "Fellow-Citizens of the Senate and House of Representatives: ", "I embrace with great satisfaction the opportunity which now presents itself of congratulating you on the present favorable prospects of our public affairs. The recent accession of the important state of North Carolina to the Constitution of the United States (of which official information has been received), the rising credit and respectability of our country, the general and increasing good will toward the government of the Union, and the concord, peace, and plenty with which we are blessed are circumstances auspicious in an eminent degree to our national prosperity. ")

#### Lifecycle of a Spark Program

**(watch now 0:23)**:

[![Spark Program Lifecycle by Anthony Joseph in BerkeleyX/CS100.1x](http://img.youtube.com/vi/HWZUqNYAJj4/0.jpg)](https://www.youtube.com/watch?v=HWZUqNYAJj4?rel=0&autoplay=1&modestbranding=1&start=1)

##### Summary

-   create RDDs from:
    -   some external data source (such as a distributed file system)
    -   parallelized collection in your driver program
-   lazily transform these RDDs into new RDDs
-   cache some of those RDDs for future reuse
-   you perform actions to execute parallel computation to produce results

### Transform lines to words

-   We need to loop through each line and split the line into words
-   For now, let us split using whitespace
-   More sophisticated regular expressions can be used to split the line (as we will see soon)

``` scala
sou17900108
.flatMap(line => line.split(" "))
.take(100)
```

>     res55: Array[String] = Array(George, Washington, "", January, 8,, 1790, Fellow-Citizens, of, the, Senate, and, House, of, Representatives:, I, embrace, with, great, satisfaction, the, opportunity, which, now, presents, itself, of, congratulating, you, on, the, present, favorable, prospects, of, our, public, affairs., The, recent, accession, of, the, important, state, of, North, Carolina, to, the, Constitution, of, the, United, States, (of, which, official, information, has, been, received),, the, rising, credit, and, respectability, of, our, country,, the, general, and, increasing, good, will, toward, the, government, of, the, Union,, and, the, concord,, peace,, and, plenty, with, which, we, are, blessed, are, circumstances, auspicious, in, an, eminent, degree, to)

### Naive word count

At a first glace, to do a word count of George Washingtons SoU address, we are templed to do the following:

-   just break each line by the whitespace character " " and find the words using a `flatMap`
-   then do the `map` with the closure `word => (word, 1)` to initialize each `word` with a integer count of `1`
    -   ie., transform each word to a *(key, value)* pair or `Tuple` such as `(word, 1)`
-   then count all *value*s with the same *key* (`word` is the Key in our case) by doing a
    -   `reduceByKey(_+_)`
-   and finally `collect()` to display the results.

``` scala
sou17900108
.flatMap( line => line.split(" ") )
.map( word => (word, 1) )
.reduceByKey(_+_)
.collect()
```

>     res56: Array[(String, Int)] = Array((call,1), (country,3), (House,3), (promoted,1), (admitted,1), (agree,1), (accession,1), (exertion,1), (plenty,1), (have,4), (incident,1), (consideration,,1), (session,3), (national,3), (equal,2), (we,2), (intimating,1), (been,2), (who,2), (eminent,1), (any,1), (immediately,1), (essential.,1), (western,1), (speedy,1), (institution,1), (respect,2), (me,2), (peace.,1), (frontiers,1), (free,2), (parts,2), (are,4), (blessings,2), (8,,1), (authority;,1), (presents,1), (affairs,1), (discriminate,1), (expressive,1), (administration,1), (introduction,1), (comfortable,1), (our,10), (as,9), (intrusted,1), (circumstances,2), (peace,,1), (respectability,1), (contributes,1), (branch,1), (better,1), (them,2), (independent,1), (proceeding,1), (duty,2), (law,,1), (foreigners,1), (satisfactory,1), (is,10), (convey,1), (appointments,,1), (favorable,1), (Senate,2), (am,2), (certain,1), (shall,2), (Commonwealth,1), (Virginia),1), (proper,3), (States,4), (recommendation;,1), (impressions,1), (sense,1), (they,2), (new,1), (my,2), (rising,1), (expedient,1), (hope.,1), (uniting,1), (oppression,1), (free,,1), (now,1), (due,2), (has,3), (university,,1), (deserve,1), (licentiousness--,1), (safety,1), (degree,1), (persons,1), (giving,1), (learning,1), (depredations,,1), (Washington,1), (conducive,1), (according,1), (need,1), (manufactures,1), (render,3), (invasions,1), (honor,1), (fulfill,1), (Still,1), (directed,1), (basis,1), (southern,1), (conduct,1), (exigencies,1), (well-digested,1), (objects,1), (Indians,1), (truly,1), (cares,1), (foreign,1), (welfare,1), (consultations,1), (resolution,1), (means,2), (cherishing,1), (this,3), (convincing,1), (deemed,1), (right,1), (There,1), (themselves,1), (general,2), (entirely,1), (explicit,1), (defense,1), (only,1), (importance,,1), (opinion,2), (security,1), (exercise,1), (Knowledge,1), (already,1), (established,,1), (particularly,2), (satisfaction,2), (realize,1), (afford.,1), (rule,1), (cheerful,1), (nations,1), (measure,1), (congratulating,1), (hope,1), (can,3), (resuming,1), (relieved,1), (country,,1), (communication,1), (will,,1), (aggressors.,1), (into,1), (there,1), (science,1), (hostile,1), (rights;,1), (trust,,1), (discern,1), (lay,1), (own,1), (reason,1), (Among,1), (directed,,1), (declaration.,1), (essential,,1), (patriotism,,1), (high,1), (mature,1), (laid,1), (compensation,1), (surest,1), (advancement,1), (respecting,1), (consideration.,1), (one,2), (with,11), (obviously,1), (first,,1), (January,1), (best,3), (importance,2), (interesting,1), (seminaries,1), (post-roads.,1), (proportionably,1), (duly,1), (attention,2), (promote,1), (economy.,1), (afford,1), (Representatives:,3), (from,12), (other,3), (interest,1), (affairs.,2), (well,2), (close,1), (further,1), (received),,1), (facilitated,1), (requisite;,1), (affording,1), (allowed,1), (their,7), (concord,,1), (adequate,1), (last,2), (expediency,1), (between,3), (will,13), (information,3), (useful,1), (valuable,1), ("",1), (confidence,2), (war,1), (provisions,1), (designated,1), (providing,1), (important,2), (encroachments,,1), (uniform,2), (vigilance,1), (so,4), (devise,1), (blessed,1), (Uniformity,1), (reliance,1), (it,6), (The,5), (than,1), (others,1), (attended,1), (deeply,1), (troops,1), (fund,1), (embrace,1), (protection,1), (secure,1), (desirable,1), (engage,1), (received,1), (such,4), (literature.,1), (add,1), (recommended,1), (papers,2), (burthens,1), (common,1), (end,4), (preserving,1), (Whether,1), (to.,1), (deliberations,1), (resulting,1), (place,1), (ways--by,1), (supplies.,1), (derive,2), (To,2), (laws.,1), (great,4), ((of,1), (establishment,1), (commerce,,1), (task,1), (armed,,1), (reflection,1), (less,1), (currency,,1), (lawful,1), (last--and,1), (inevitable,1), (expedients,1), (speedily,1), (the,92), (sentiment,1), (not,3), (nothing,1), (enable,1), (manufactories,1), (most,2), (if,1), (considerations,1), (be,20), (punish,1), (all,1), (contained,1), (though,1), (legislature.,2), (toward,1), (credit,2), (superfluous,1), (disregard,1), (rights,1), (regard,3), (but,5), (official,1), (deliberate,1), (skill,1), (persuaded,1), (itself,1), (increasing,1), (distinguish,1), (necessary,2), (Nor,1), (George,1), (on,3), (distant,1), (against,2), (would,2), (perfect,1), (before,2), (at,2), (object,3), (estimates,1), (them;,1), (should,,1), (interests,2), (Union,,2), (may,5), (government,3), (ascertained,1), (good,,1), (gracious,1), (or,1), (insuring,1), (I,11), (aids,1), (intercourse,2), (Union,1), (of,68), (respectively,,1), (fellow,1), (reach,1), (Various,1), (1790,1), (saw,1), (answered,1), (producing,1), (encouragement,2), (Carolina,1), (particular,1), (Fellow-Citizens,1), (inducements,1), (auspicious,1), (arrangements,1), (difficulty,1), (pacific,1), (opportunity,1), (prosperity.,2), (patronage,1), (A,1), (plan,1), (which,18), (cooperation,2), (you,,1), (also,1), (inhabitants,1), (competent,1), (require,2), (should,3), (tend,1), (genius,1), (naturalization.,1), (character,1), (promotion,1), (for,7), (Gentlemen,2), (teaching,1), (worthy,1), (placed,1), (effectual,2), (present,2), (entitled,1), (your,9), (inventions,1), (terms,1), (North,1), (cool,1), (happiness.,1), (officers,2), (Providence,1), (people,2), (abroad,1), (pleasure,1), (expect,1), (facilitating,1), (was,1), (merit,1), (community,1), (endeavors,1), (arduous,1), (exertions,1), (peculiar,1), (society;,1), (firmness,,1), (pleasing,1), (by,11), (expectations,1), (tribes,1), (efforts,1), (defined,1), (inviolable,1), (It,1), (value,1), (an,5), (soldiers,1), (temperate,1), (sanction,1), (disciplined;,1), (recent,1), (provision,2), (conciliate,1), (made,2), (constitution,1), (agriculture,,1), (concerned,,1), (enlightened,1), (novelty,1), (people,,1), (adopted,1), (efficient,,1), (defraying,1), (wisdom.,1), (employed,1), (convenience,1), (ought,3), (in,16), (provide,1), (weights,,1), (In,4), (good,2), (those,3), (necessary,,1), (support,2), (manner,1), (public,5), (course,1), (and,,1), (entered,1), (within,1), (ours,1), (receive,1), (prospects,1), (liberty,1), (every,2), (matter,1), (nature,1), (you,10), ((comprehending,1), (prepared,2), (various,1), (avoiding,1), (that,15), (a,20), (many,1), (spirit,1), (expenses,1), (not,,1), (work,1), (state,2), (government.,1), (concur;,1), (to,53), (know,1), (military,,1), (persuaded,,1), (post-office,1), (perceive,1), (Constitution,1), (specify,1), (regard.,1), (and,39), (indispensable,1), (constituents,1), (home,,1), (forbear,1), (United,4), (direct,1), (citizens,2), (measures,4))

Unfortunately, as you can see from the `collect` above:

-   the words have punctuations at the end which means that the same words are being counted as different words. Eg: importance
-   empty words are being counted

So we need a bit of `regex`'ing or regular-expression matching (all readily available from Scala via Java String types).

We will cover the three things we want to do with a simple example from Middle Earth!

-   replace all multiple whitespace characters with one white space character " "
-   replace all punction characters we specify within `[` and `]` such as `[,?.!:;]` by the empty string `""` (i.e., remove these punctuation characters)
-   convert everything to lower-case.

``` scala
val example = "Master, Master!   It's me, Sméagol... mhrhm*%* But they took away our precious, they wronged us. Gollum will protect us..., Master, it's me Sméagol."
```

>     example: String = Master, Master!   It's me, Sméagol... mhrhm*%* But they took away our precious, they wronged us. Gollum will protect us..., Master, it's me Sméagol.

``` scala
example.replaceAll("\\s+", " ") //replace multiple whitespace characters (including space, tab, new line, etc.) with one whitespace " "
       .replaceAll("""([,?.!:;])""", "") // replace the following punctions characters: , ? . ! : ; . with the empty string ""
       .toLowerCase() // converting to lower-case
```

>     res57: String = master master it's me sméagol mhrhm*%* but they took away our precious they wronged us gollum will protect us master it's me sméagol

### More sophisticated word count

We are now ready to do a word count of George Washington's SoU on January 8th 1790 as follows:

``` scala
val wordCount_sou17900108 = 
sou17900108
    .flatMap(line => 
         line.replaceAll("\\s+", " ") //replace multiple whitespace characters (including space, tab, new line, etc.) with one whitespace " "
             .replaceAll("""([,?.!:;])""", "") // replace the following punctions characters: , ? . ! : ; . with the empty string ""
             .toLowerCase() // converting to lower-case
             .split(" "))
    .map(x => (x, 1))
    .reduceByKey(_+_)
    
wordCount_sou17900108.collect()
```

>     wordCount_sou17900108: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[17827] at reduceByKey at <console>:44
>     res58: Array[(String, Int)] = Array((university,1), (call,1), (country,4), (promoted,1), (agree,1), (admitted,1), (accession,1), (exertion,1), (plenty,1), (have,4), (incident,1), (session,3), (national,3), (equal,2), (we,2), (intimating,1), (been,2), (who,2), (eminent,1), (any,1), (consideration,2), (immediately,1), (western,1), (speedy,1), (institution,1), (respect,2), (me,2), (discriminate,1), (frontiers,1), (free,3), (affairs,3), (are,4), (economy,1), (administration,1), (parts,2), (presents,1), (blessings,2), (expressive,1), (introduction,1), (comfortable,1), (our,10), (as,9), (intrusted,1), (circumstances,2), (respectability,1), (branch,1), (contributes,1), (better,1), (them,3), (independent,1), (proceeding,1), (received),1), (duty,2), (foreigners,1), (satisfactory,1), (is,10), (convey,1), (commonwealth,1), (favorable,1), (am,2), (certain,1), (january,1), (shall,2), (sense,1), (proper,3), (impressions,1), (disciplined,1), (they,2), (new,1), (my,2), (rising,1), (expedient,1), (uniting,1), (oppression,1), (now,1), (due,2), (has,3), (deserve,1), (licentiousness--,1), (safety,1), (degree,1), (persons,1), (8,1), (giving,1), (concur,1), (learning,1), (conducive,1), (according,1), (need,1), (manufactures,1), (render,3), (invasions,1), (honor,1), (fulfill,1), (directed,2), (basis,1), (southern,1), (conduct,1), (law,1), (well-digested,1), (exigencies,1), (senate,2), (objects,1), (truly,1), (cares,1), (knowledge,1), (concord,1), (foreign,1), (welfare,1), (consultations,1), (resolution,1), (means,2), (cherishing,1), (this,3), (convincing,1), (deemed,1), (right,1), (post-roads,1), (themselves,1), (general,2), (entirely,1), (explicit,1), (defense,1), (only,1), (washington,1), (established,1), (house,3), (first,1), (still,1), (opinion,2), (security,1), (exercise,1), (already,1), (particularly,2), (satisfaction,2), (essential,2), (patriotism,1), (realize,1), (rule,1), (cheerful,1), (nations,1), (measure,1), (congratulating,1), (hope,2), (can,3), (resuming,1), (peace,2), (encroachments,1), (relieved,1), (communication,1), (society,1), (into,1), (there,2), (science,1), (hostile,1), (representatives,3), (discern,1), (lay,1), (own,1), (reason,1), (high,1), (mature,1), (laid,1), (states,4), (compensation,1), (surest,1), (advancement,1), (trust,1), (respecting,1), (declaration,1), (one,2), (with,11), (uniformity,1), (obviously,1), (best,3), (importance,3), (interesting,1), (proportionably,1), (duly,1), (seminaries,1), (aggressors,1), (attention,2), (promote,1), (afford,2), (close,1), (depredations,1), (from,12), (other,3), (well,2), (interest,1), (further,1), (among,1), (virginia),1), (facilitated,1), (appointments,1), (affording,1), (allowed,1), (their,7), (adequate,1), (last,2), (expediency,1), (between,3), (indians,1), (will,14), (information,3), (useful,1), (valuable,1), ("",1), (confidence,2), (war,1), (provisions,1), (designated,1), (providing,1), (important,2), (uniform,2), (so,4), (vigilance,1), (devise,1), (blessed,1), (reliance,1), (it,7), (agriculture,1), (than,1), (others,1), (attended,1), (deeply,1), (troops,1), (fund,1), (embrace,1), (protection,1), (secure,1), (desirable,1), (engage,1), (received,1), (such,4), (nothing,1), (naturalization,1), (add,1), (recommended,1), (papers,2), (burthens,1), (common,1), (end,4), (preserving,1), (weights,1), (deliberations,1), (resulting,1), (place,1), (ways--by,1), (derive,2), (great,4), ((of,1), (establishment,1), (expedients,1), (task,1), (reflection,1), (less,1), (last--and,1), (inevitable,1), (lawful,1), (speedily,1), (the,97), (sentiment,1), (not,4), (enable,1), (manufactories,1), (most,2), (if,1), (considerations,1), (providence,1), (be,20), (home,1), (punish,1), (all,1), (contained,1), (persuaded,2), (though,1), (laws,1), (toward,1), (credit,2), (gentlemen,2), (efficient,1), (superfluous,1), (disregard,1), (rights,2), (regard,4), (but,5), (official,1), (deliberate,1), (skill,1), (itself,1), (increasing,1), (nor,1), (distinguish,1), (necessary,3), (on,3), (distant,1), (against,2), (would,2), (perfect,1), (before,2), (at,2), (object,3), (estimates,1), (united,4), (union,3), (fellow-citizens,1), (interests,2), (may,5), (government,4), (ascertained,1), (armed,1), (gracious,1), (requisite,1), (or,1), (insuring,1), (aids,1), (intercourse,2), (of,68), (fellow,1), (reach,1), (saw,1), (1790,1), (answered,1), (encouragement,2), (producing,1), (prosperity,2), (particular,1), (currency,1), (inducements,1), (auspicious,1), (arrangements,1), (difficulty,1), (pacific,1), (i,11), (opportunity,1), (plan,1), (patronage,1), (military,1), (north,1), (which,18), (cooperation,2), (also,1), (inhabitants,1), (competent,1), (require,2), (should,4), (tend,1), (genius,1), (for,7), (whether,1), (promotion,1), (character,1), (teaching,1), (worthy,1), (placed,1), (effectual,2), (present,2), (entitled,1), (your,9), (inventions,1), (terms,1), (cool,1), (authority,1), (pleasing,1), (officers,2), (literature,1), (people,3), (abroad,1), (pleasure,1), (expect,1), (facilitating,1), (was,1), (merit,1), (community,1), (endeavors,1), (arduous,1), (exertions,1), (peculiar,1), (sanction,1), (by,11), (concerned,1), (expectations,1), (tribes,1), (efforts,1), (defined,1), (inviolable,1), (value,1), (an,5), (soldiers,1), (supplies,1), (temperate,1), (recent,1), (provision,2), (conciliate,1), (happiness,1), (made,2), (constitution,2), (enlightened,1), (novelty,1), (adopted,1), (defraying,1), (carolina,1), (employed,1), (george,1), (convenience,1), (ought,3), (in,20), (provide,1), (commerce,1), (good,3), (those,3), (recommendation,1), (support,2), (manner,1), (public,5), (course,1), (receive,1), (entered,1), (within,1), (ours,1), (prospects,1), (liberty,1), (every,2), (matter,1), (nature,1), (you,11), ((comprehending,1), (prepared,2), (various,2), (avoiding,1), (that,15), (legislature,2), (a,21), (many,1), (spirit,1), (expenses,1), (work,1), (state,2), (to,56), (know,1), (wisdom,1), (post-office,1), (perceive,1), (respectively,1), (specify,1), (firmness,1), (and,40), (forbear,1), (indispensable,1), (constituents,1), (direct,1), (measures,4), (citizens,2))

``` scala
val top10 = wordCount_sou17900108.sortBy(_._2, false).collect()
```

>     top10: Array[(String, Int)] = Array((the,97), (of,68), (to,56), (and,40), (a,21), (be,20), (in,20), (which,18), (that,15), (will,14), (from,12), (with,11), (i,11), (by,11), (you,11), (our,10), (is,10), (as,9), (your,9), (their,7), (it,7), (for,7), (but,5), (may,5), (an,5), (public,5), (country,4), (have,4), (are,4), (states,4), (so,4), (such,4), (end,4), (great,4), (not,4), (regard,4), (united,4), (government,4), (should,4), (measures,4), (session,3), (national,3), (free,3), (affairs,3), (them,3), (proper,3), (has,3), (render,3), (this,3), (house,3), (can,3), (representatives,3), (best,3), (importance,3), (other,3), (between,3), (information,3), (necessary,3), (on,3), (object,3), (union,3), (people,3), (ought,3), (good,3), (those,3), (equal,2), (we,2), (been,2), (who,2), (consideration,2), (respect,2), (me,2), (parts,2), (blessings,2), (circumstances,2), (duty,2), (am,2), (shall,2), (they,2), (my,2), (due,2), (directed,2), (senate,2), (means,2), (general,2), (opinion,2), (particularly,2), (satisfaction,2), (essential,2), (hope,2), (peace,2), (there,2), (one,2), (attention,2), (afford,2), (well,2), (last,2), (confidence,2), (important,2), (uniform,2), (papers,2), (derive,2), (most,2), (persuaded,2), (credit,2), (gentlemen,2), (rights,2), (against,2), (would,2), (before,2), (at,2), (interests,2), (intercourse,2), (encouragement,2), (prosperity,2), (cooperation,2), (require,2), (effectual,2), (present,2), (officers,2), (provision,2), (made,2), (constitution,2), (support,2), (every,2), (prepared,2), (various,2), (legislature,2), (state,2), (citizens,2), (university,1), (call,1), (promoted,1), (agree,1), (admitted,1), (accession,1), (exertion,1), (plenty,1), (incident,1), (intimating,1), (eminent,1), (any,1), (immediately,1), (western,1), (speedy,1), (institution,1), (discriminate,1), (frontiers,1), (economy,1), (administration,1), (presents,1), (expressive,1), (introduction,1), (comfortable,1), (intrusted,1), (respectability,1), (branch,1), (contributes,1), (better,1), (independent,1), (proceeding,1), (received),1), (foreigners,1), (satisfactory,1), (convey,1), (commonwealth,1), (favorable,1), (certain,1), (january,1), (sense,1), (impressions,1), (disciplined,1), (new,1), (rising,1), (expedient,1), (uniting,1), (oppression,1), (now,1), (deserve,1), (licentiousness--,1), (safety,1), (degree,1), (persons,1), (8,1), (giving,1), (concur,1), (learning,1), (conducive,1), (according,1), (need,1), (manufactures,1), (invasions,1), (honor,1), (fulfill,1), (basis,1), (southern,1), (conduct,1), (law,1), (well-digested,1), (exigencies,1), (objects,1), (truly,1), (cares,1), (knowledge,1), (concord,1), (foreign,1), (welfare,1), (consultations,1), (resolution,1), (cherishing,1), (convincing,1), (deemed,1), (right,1), (post-roads,1), (themselves,1), (entirely,1), (explicit,1), (defense,1), (only,1), (washington,1), (established,1), (first,1), (still,1), (security,1), (exercise,1), (already,1), (patriotism,1), (realize,1), (rule,1), (cheerful,1), (nations,1), (measure,1), (congratulating,1), (resuming,1), (encroachments,1), (relieved,1), (communication,1), (society,1), (into,1), (science,1), (hostile,1), (discern,1), (lay,1), (own,1), (reason,1), (high,1), (mature,1), (laid,1), (compensation,1), (surest,1), (advancement,1), (trust,1), (respecting,1), (declaration,1), (uniformity,1), (obviously,1), (interesting,1), (proportionably,1), (duly,1), (seminaries,1), (aggressors,1), (promote,1), (close,1), (depredations,1), (interest,1), (further,1), (among,1), (virginia),1), (facilitated,1), (appointments,1), (affording,1), (allowed,1), (adequate,1), (expediency,1), (indians,1), (useful,1), (valuable,1), ("",1), (war,1), (provisions,1), (designated,1), (providing,1), (vigilance,1), (devise,1), (blessed,1), (reliance,1), (agriculture,1), (than,1), (others,1), (attended,1), (deeply,1), (troops,1), (fund,1), (embrace,1), (protection,1), (secure,1), (desirable,1), (engage,1), (received,1), (nothing,1), (naturalization,1), (add,1), (recommended,1), (burthens,1), (common,1), (preserving,1), (weights,1), (deliberations,1), (resulting,1), (place,1), (ways--by,1), ((of,1), (establishment,1), (expedients,1), (task,1), (reflection,1), (less,1), (last--and,1), (inevitable,1), (lawful,1), (speedily,1), (sentiment,1), (enable,1), (manufactories,1), (if,1), (considerations,1), (providence,1), (home,1), (punish,1), (all,1), (contained,1), (though,1), (laws,1), (toward,1), (efficient,1), (superfluous,1), (disregard,1), (official,1), (deliberate,1), (skill,1), (itself,1), (increasing,1), (nor,1), (distinguish,1), (distant,1), (perfect,1), (estimates,1), (fellow-citizens,1), (ascertained,1), (armed,1), (gracious,1), (requisite,1), (or,1), (insuring,1), (aids,1), (fellow,1), (reach,1), (saw,1), (1790,1), (answered,1), (producing,1), (particular,1), (currency,1), (inducements,1), (auspicious,1), (arrangements,1), (difficulty,1), (pacific,1), (opportunity,1), (plan,1), (patronage,1), (military,1), (north,1), (also,1), (inhabitants,1), (competent,1), (tend,1), (genius,1), (whether,1), (promotion,1), (character,1), (teaching,1), (worthy,1), (placed,1), (entitled,1), (inventions,1), (terms,1), (cool,1), (authority,1), (pleasing,1), (literature,1), (abroad,1), (pleasure,1), (expect,1), (facilitating,1), (was,1), (merit,1), (community,1), (endeavors,1), (arduous,1), (exertions,1), (peculiar,1), (sanction,1), (concerned,1), (expectations,1), (tribes,1), (efforts,1), (defined,1), (inviolable,1), (value,1), (soldiers,1), (supplies,1), (temperate,1), (recent,1), (conciliate,1), (happiness,1), (enlightened,1), (novelty,1), (adopted,1), (defraying,1), (carolina,1), (employed,1), (george,1), (convenience,1), (provide,1), (commerce,1), (recommendation,1), (manner,1), (course,1), (receive,1), (entered,1), (within,1), (ours,1), (prospects,1), (liberty,1), (matter,1), (nature,1), ((comprehending,1), (avoiding,1), (many,1), (spirit,1), (expenses,1), (work,1), (know,1), (wisdom,1), (post-office,1), (perceive,1), (respectively,1), (specify,1), (firmness,1), (forbear,1), (indispensable,1), (constituents,1), (direct,1))

### Doing it all together for George Washington and Barrack Obama

``` scala
//sc.textFile("dbfs:/datasets/sou/17900108.txt") // George Washington's first SoU
sc.textFile("dbfs:/datasets/sou/20160112.txt")   // Barrack Obama's second SoU
    .flatMap(line => 
         line.replaceAll("\\s+", " ") //replace multiple whitespace characters (including space, tab, new line, etc.) with one whitespace " "
             .replaceAll("""([,?.!:;])""", "") // replace the following punctions characters: , ? . ! : ; . with the empty string ""
             .toLowerCase() // converting to lower-case
             .split(" "))
    .map(x => (x,1))
    .reduceByKey(_+_)
    .sortBy(_._2, false)
    .collect()
```

>     res59: Array[(String, Int)] = Array((the,264), (and,189), (to,188), (of,135), (we,116), (a,100), (that,100), (in,90), (our,86), (for,59), (is,51), (or,49), (it,46), (i,41), (on,40), (this,36), (who,35), (but,34), (us,33), (have,32), (are,30), (as,30), (that's,30), (not,27), (their,26), (world,24), (with,24), (more,24), (they,23), (it's,23), (will,22), (be,22), (america,21), (when,21), (can,20), (people,20), (all,20), (work,20), (you,19), (years,18), (so,18), (do,18), (up,18), (make,17), (year,17), (should,17), (american,16), (new,16), (just,16), (if,16), (by,16), (economy,15), (from,15), (change,15), (because,15), (now,15), (americans,15), (want,14), (has,14), (how,14), (there,14), (we've,14), (every,14), (need,13), (out,13), (over,13), (even,13), (better,13), (see,13), (than,13), (country,12), (what,12), (get,12), (future,12), (only,11), (at,11), (about,11), (job,11), (like,11), (security,10), (why,10), (most,10), (those,10), (back,10), (way,10), (them,9), (right,9), (some,9), (one,9), (still,9), (his,9), (keep,9), (know,9), (these,9), (time,9), (no,9), (workers,9), (we're,8), (jobs,8), (him,8), (doesn't,8), (other,8), (past,8), (isil,8), (leadership,8), (also,8), (he,8), (system,8), (voices,8), (next,8), (an,8), (going,7), (my,7), (big,7), (energy,7), (war,7), (together,7), (give,7), (i'm,7), (made,7), (everything,7), (politics,7), (care,7), (power,7), (agree,6), (been,6), (don't,6), (families,6), (without,6), (cut,6), (its,6), (same,6), (states,6), (after,6), (best,6), (was,6), (tonight,6), (economic,6), (democracy,6), (where,6), (opportunity,6), (doing,6), (help,6), (many,6), (can't,6), (national,5), (here,5), (go,5), (into,5), (congress,5), (business,5), (last,5), (president,5), (happen,5), (got,5), (believe,5), (countries,5), (planet,5), (me,5), (come,5), (america's,5), (vote,5), (start,5), (health,5), (love,5), (basic,5), (own,5), (kids,5), (seven,5), (nearly,5), (spirit,5), (needs,5), (we'll,5), (sure,5), (political,5), (may,5), (each,5), (matter,5), (especially,5), (military,5), (progress,5), (lot,5), (take,5), (away,4), (long,4), (foreign,4), (hardworking,4), (allies,4), (oil,4), (travel,4), (done,4), (science,4), (down,4), (important,4), (any,4), (i'll,4), (final,4), (pay,4), (day,4), (single,4), (first,4), (elected,4), (harder,4), (businesses,4), (education,4), (between,4), (almost,4), (answer,4), (history,4), (international,4), (talk,4), (ways,4), (stand,4), (money,4), (there's,4), (approach,4), (told,4), (place,4), (makes,4), (less,4), (pass,4), (everyone,4), (things,4), (comes,4), (changes,4), (look,4), (had,4), (before,4), (fellow,4), (climate,4), (much,4), (communities,4), (save,4), (around,4), (safe,4), (hard,4), (retirement,4), (working,4), (did,4), (words,4), (nation,4), (terrorists,4), (everybody,4), (government,4), (student,4), (won't,4), (what's,3), (tougher,3), (true,3), (agreement,3), (too,3), (nations,3), (second,3), (focus,3), (serious,3), (quiet,3), (open,3), (lead,3), (office,3), (line,3), (chamber,3), (equal,3), (program,3), (fair,3), (live,3), (benefits,3), (didn't,3), (reason,3), (fighting,3), (training,3), (life,3), (she,3), (citizen,3), (two,3), (getting,3), (set,3), (shouldn't,3), (feel,3), (means,3), (play,3), (ourselves,3), (terrorist,3), (thing,3), (kind,3), (qaeda,3), (build,3), (syria,3), (issues,3), (built,3), (family,3), (fact,3), (push,3), (century,3), (young,3), (end,3), (strength,3), (middle,3), (speaker,3), (control,3), (resources,3), (community,3), (might,3), (good,3), (troops,3), (such,3), (let,3), (mr,3), (cuts,3), (interests,3), (he's,3), (say,3), (incredible,3), (strongest,3), (al,3), (isn't,3), (global,3), (support,3), (medical,3), (citizens,3), (others,3), (through,3), (fall,3), (else,3), (justice,3), (rules,3), (protect,3), (strong,3), (great,3), (average,3), (rights,3), (show,3), (against,3), (would,3), (united,3), (face,3), (use,3), (college,3), (commitment,3), (your,3), (put,3), (wants,3), (crisis,3), (reflect,3), (state,3), (generations,3), (biggest,3), (shot,2), (behind,2), (wasn't,2), (month,2), (technology,2), (favor,2), (son,2), (party,2), (promises,2), (rest,2), (stop,2), (21st,2), (washington,2), (handful,2), (society,2), (leaders,2), (something,2), (intend,2), (everywhere,2), (recognize,2), (bills,2), (enemies,2), (taking,2), (vulnerable,2), (respect,2), (voice,2), (wage,2), (immigrant,2), (asia,2), (fight,2), (earth,2), (overnight,2), (muster,2), (sector,2), (tens,2), (question,2), (200,2), (you'll,2), (discovery,2), (forces,2), (powerful,2), (trends,2), (argue,2), (coverage,2), (until,2), (africa,2), (chance,2), (manufacturing,2), (recruit,2), (threat,2), (send,2), (they've,2), (few,2), (optimism,2), (none,2), (freedom,2), (priorities,2), (speak,2), (affordable,2), (school,2), (confidence,2), (created,2), (call,2), (strengths,2), (improve,2), (plenty,2), (low-income,2), (begins,2), (challenges,2), (parties,2), (instead,2), (million,2), (parts,2), (fighters,2), (rigged,2), (law,2), (think,2), (soldier,2), (already,2), (cancer,2), (act,2), (real,2), (works,2), (depend,2), (word,2), (isolating,2), (iran,2), (interest,2), (fear,2), (students,2), (very,2), (whatever,2), (pushing,2), (under,2), (ever,2), (collective,2), (unarmed,2), (space,2), (insurance,2), (gives,2), (stronger,2), (learn,2), (areas,2), (air,2), (doubt,2), (worked,2), (civilians,2), (easy,2), (enough,2), (god,2), (choices,2), (since,2), (coalition,2), (saving,2), (beat,2), (attention,2), (understand,2), (providing,2), (assembly,2), (iowa,2), (decline,2), (plotting,2), (lift,2), (careers,2), (gotten,2), (ukraine,2), (shape,2), (alone,2), (different,2), (brings,2), (pull,2), (ten,2), (nor,2), (development,2), (find,2), (networks,2), (fields,2), (broken,2), (iraq,2), (percent,2), (folks,2), (truth,2), (transition,2), (third,2), (pace,2), (east,2), (cure,2), (i've,2), (early,2), (unemployment,2), (higher,2), (commerce,2), (stamp,2), (public,2), (decisions,2), (anew,2), (tax,2), (finally,2), (coal,2), (internet,2), (accelerate,2), (clean,2), (rooted,2), (voting,2), (ask,2), (tools,2), (bless,2), (saw,2), (innovation,2), (protecting,2), (unconditional,2), (retrain,2), (whether,2), (problem,2), (haven't,2), (both,2), (red,2), (turning,2), (sixty,2), (politicians,2), (wages,2), (try,2), (easier,2), (companies,2), (lose,2), (course,2), (ago,2), (another,2), (could,2), (result,2), (steps,2), (research,2), (budget,2), (diversity,2), (vice,2), (person,2), (violence,2), (record,2), (lincoln,2), (reach,2), (trying,2), (walking,2), (conflicts,2), (helping,2), (unique,2), (today's,2), (cost,2), (respects,2), (attacks,2), (relations,2), (started,2), (far,2), (mean,2), (congressional,2), (computer,2), (reform,2), (taken,2), (worst,2), (choice,2), (poverty,2), (income,2), (solve,2), (force,2), (spread,2), (entrepreneur,2), (either,2), (bipartisan,2), (brothers,2), (gone,2), (possible,2), (let's,2), (union,2), (willingness,2), (prison,2), (immigrants,2), (achieve,2), (must,2), (taxes,2), (corporate,2), (constructive,2), (her,2), (you're,2), (pose,2), (then,2), (today,2), (path,2), (promise,2), (depends,2), (stopped,2), (growing,2), (online,2), (always,2), (hold,2), (someone,1), (goals,1), (greater,1), (urging,1), (graduation,1), (reality,1), (order,1), (hidden,1), (minimum,1), (conflict,1), (offering,1), (knows,1), (prefer,1), (hubs,1), (partnership,1), (shut,1), (arsenal,1), (vietnam,1), (element,1), (encourages,1), (african,1), (republicans,1), (raise,1), (cheaper,1), (regrets,1), (pursue,1), (namely,1), (fourth,1), (veterans,1), (regardless,1), (unnecessary,1), (straight,1), (sources,1), (boardrooms,1), (sick,1), (rolled,1), (trans-pacific,1), (giving,1), (avoided,1), (math,1), (betrays,1), (childhood,1), (places,1), (point,1), (low,1), (marathon,1), (six,1), (three,1), (breaks,1), (stake,1), (blow,1), (depression,1), (often,1), (spilling,1), (grace,1), (blood,1), (remote,1), (results,1), (earlier,1), (reflects,1), (welcome,1), (defend,1), (extending,1), (capacity,1), (someday,1), (precious,1), (enormous,1), (fiction,1), (finds,1), (accounts,1), (rational,1), (scourge,1), (recipe,1), (muslims,1), (protester,1), (accept,1), (casts,1), (disruptions,1), (create,1), (immigration,1), (bound,1), (high,1), (reinvented,1), (hire,1), (slowed,1), (graduates,1), (who've,1), (classes,1), (teacher,1), (winning,1), (increased,1), (next-generation,1), (standing,1), (despite,1), (decades-long,1), (largest,1), (afford,1), (building,1), (date,1), (well,1), (compromise,1), (weight,1), (souls,1), (rallying,1), (dispute,1), (stays,1), (envy,1), (funds,1), (report,1), (brochure,1), ("",1), (tape,1), (maybe,1), (cold,1), (ocean,1), (industry,1), (demands,1), (hands,1), (plots,1), (finance,1), (weakens,1), (branches,1), (package,1), (priority,1), (disagree,1), (policy,1), (diminished,1), (lie,1), (simple,1), (doctors,1), (rooftops,1), (transportation,1), (trucks,1), (dangerous,1), (movements,1), (becoming,1), (late,1), (inside,1), (three-quarters,1), (yemen,1), (russians,1), (shores,1), (matters,1), (promising,1), (conventional,1), (ends,1), (boosted,1), (products,1), (ii,1), (benghazi,1), (wise,1), (elevated,1), (failing,1), (brave,1), (arguments,1), (loved,1), (diplomatic,1), (outward,1), (asian,1), (existence,1), (level,1), (bigger,1), (claiming,1), (loan,1), (vision,1), (tv,1), (territory,1), (produce,1), (presidency,1), (perpetrator,1), (lesson,1), (nuclear-armed,1), (hemisphere,1), (feeding,1), (uses,1), (colombia,1), (locate,1), (prescription,1), (hunted,1), (cures,1), (ensure,1), ("we,1), (religions,1), (partnering,1), (hear,1), (starting,1), (bucks,1), (obama,1), (expanding,1), (region,1), (extra,1), (wind,1), (senator,1), (longest,1), (gun,1), (although,1), (faith,1), (past",1), (i'd,1), (ruin,1), (afghanistan,1), (deal,1), (concern,1), (60,1), (treating,1), (orbit,1), (deny,1), (thank,1), (active,1), (austin,1), (somebody,1), (borrower's,1), (worse,1), (generation,1), (restore,1), (inspired,1), (vicious,1), (abuse,1), (helped,1), (protected,1), (replace,1), (marks,1), (later,1), (appreciate,1), (died,1), (grit,1), (pouring,1), (expand,1), (rely,1), (expected,1), (promote,1), (close,1), (able,1), (nuclear,1), (understanding,1), (permanent,1), (stage,1), (role,1), (diplomacy,1), (garages,1), (influx,1), (worth,1), (sell,1), (protects,1), (sputnik,1), (francis,1), (sanctions,1), (tends,1), (turned,1), (list,1), (dreamer,1), ('til,1), (congressman,1), (barack,1), (guarantee,1), (tea,1), (grows,1), (uphold,1), (worker,1), (partiers,1), (extraordinary,1), (campaign,1), (laden,1), (serves,1), (medicare,1), (wealthiest,1), (evil,1), (application,1), (fossil,1), (gained,1), (offend,1), (2015,1), (mat,1), (uniquely,1), (paying,1), (thomas,1), (practices,1), (joe,1), (status,1), (tpp,1), (falls,1), (expense,1), (anybody,1), (trapped,1), (biden,1), (contradicts,1), (ultimately,1), (rather,1), (january,1), (outdated,1), (flow,1), (spend,1), (kill,1), (ryan,1), (automated,1), (pray,1), (responsible,1), (rising,1), (courage,1), (meanwhile,1), (four,1), (charity,1), (bomb,1), (body,1), (selves,1), (threatened,1), (effort,1), (twisted,1), (anyone,1), (slipping,1), (bite,1), (good-paying,1), (requires,1), (withers,1), (vandalized,1), (poison,1), (bonds,1), (over-the-top,1), (dr,1), (pretty,1), (controls,1), (unpatriotic,1), (epidemic,1), (announcing,1), (cell,1), (vital,1), (rate,1), (upon,1), (patients,1), (helps,1), (camps,1), (struggling,1), (polls,1), (figured,1), (hatred,1), (brakes,1), (entrenched,1), (arizona,1), (world's,1), (example,1), (measure,1), (peace,1), (streak,1), (assistance,1), (strengthen,1), (extreme,1), (overcame,1), (stuck,1), (action,1), (hands-on,1), (pope,1), (attack,1), (reforms,1), (trust,1), (ideology,1), (reduced,1), (dirty,1), (disease,1), (concentrated,1), (join,1), (sitting,1), (delivered,1), (famine,1), (avoids,1), (forty,1), (allowed,1), (believed,1), (restored,1), (founders,1), (peril,1), (advance,1), (child,1), (door,1), (president's,1), (costs,1), (reduce,1), (insist,1), (edison,1), (solving,1), (briefing,1), (offshore,1), (superpower,1), (debating,1), (willing,1), (big-hearted,1), (setting,1), (nation-build,1), (problems,1), (targets,1), (private-sector,1), (loyalty,1), (preserve,1), (moscow,1), (cutting,1), (fifties,1), (stockpile,1), (address,1), (prevent,1), (mosque,1), (cop,1), (whose,1))

### Reading all SoUs at once using `wholetextFiles`

Let us next read all text files (ending with `.txt`) in the directory `dbfs:/datasets/sou/` at once!

`SparkContext.wholeTextFiles` lets you read a directory containing multiple small text files, and returns each of them as `(filename, content)` pairs of strings.

This is in contrast with `textFile`, which would return one record per line in each file.

``` scala
val souAll = sc.wholeTextFiles("dbfs:/datasets/sou/*.txt") // Shift+Enter to read all text files in dbfs:/datasets/sou/
souAll.cache() // let's cache this RDD for efficient reuse
```

>     souAll: org.apache.spark.rdd.RDD[(String, String)] = dbfs:/datasets/sou/*.txt MapPartitionsRDD[17855] at wholeTextFiles at <console>:36
>     res60: souAll.type = dbfs:/datasets/sou/*.txt MapPartitionsRDD[17855] at wholeTextFiles at <console>:36

``` scala
souAll.count() // Shift+enter to count the number of entries in RDD[(String,String)]
```

>     res61: Long = 230

``` scala
souAll.count() // Cntrl+Enter to count the number of entries in cached RDD[(String,String)] again (much faster!)
```

>     res62: Long = 230

Let's examine the first two elements of the RDD `souAll`.

``` scala
souAll.take(2) // Cntr+Enter to see the first two elements of souAll
```

>     res63: Array[(String, String)] = 
>     Array((dbfs:/datasets/sou/17900108.txt,"George Washington 
>
>     January 8, 1790 
>     Fellow-Citizens of the Senate and House of Representatives: 
>     I embrace with great satisfaction the opportunity which now presents itself of congratulating you on the present favorable prospects of our public affairs. The recent accession of the important state of North Carolina to the Constitution of the United States (of which official information has been received), the rising credit and respectability of our country, the general and increasing good will toward the government of the Union, and the concord, peace, and plenty with which we are blessed are circumstances auspicious in an eminent degree to our national prosperity. 
>     In resuming your consultations for the general good you can not but derive encouragement from the reflection that the measures of the last session have been as satisfactory to your constituents as the novelty and difficulty of the work allowed you to hope. Still further to realize their expectations and to secure the blessings which a gracious Providence has placed within our reach will in the course of the present important session call for the cool and deliberate exertion of your patriotism, firmness, and wisdom. 
>     Among the many interesting objects which will engage your attention that of providing for the common defense will merit particular regard. To be prepared for war is one of the most effectual means of preserving peace. 
>     A free people ought not only to be armed, but disciplined; to which end a uniform and well-digested plan is requisite; and their safety and interest require that they should promote such manufactories as tend to render them independent of others for essential, particularly military, supplies. 
>     The proper establishment of the troops which may be deemed indispensable will be entitled to mature consideration. In the arrangements which may be made respecting it it will be of importance to conciliate the comfortable support of the officers and soldiers with a due regard to economy. 
>     There was reason to hope that the pacific measures adopted with regard to certain hostile tribes of Indians would have relieved the inhabitants of our southern and western frontiers from their depredations, but you will perceive from the information contained in the papers which I shall direct to be laid before you (comprehending a communication from the Commonwealth of Virginia) that we ought to be prepared to afford protection to those parts of the Union, and, if necessary, to punish aggressors. 
>     The interests of the United States require that our intercourse with other nations should be facilitated by such provisions as will enable me to fulfill my duty in that respect in the manner which circumstances may render most conducive to the public good, and to this end that the compensation to be made to the persons who may be employed should, according to the nature of their appointments, be defined by law, and a competent fund designated for defraying the expenses incident to the conduct of foreign affairs. 
>     Various considerations also render it expedient that the terms on which foreigners may be admitted to the rights of citizens should be speedily ascertained by a uniform rule of naturalization. 
>     Uniformity in the currency, weights, and measures of the United States is an object of great importance, and will, I am persuaded, be duly attended to. 
>     The advancement of agriculture, commerce, and manufactures by all proper means will not, I trust, need recommendation; but I can not forbear intimating to you the expediency of giving effectual encouragement as well to the introduction of new and useful inventions from abroad as to the exertions of skill and genius in producing them at home, and of facilitating the intercourse between the distant parts of our country by a due attention to the post-office and post-roads. 
>     Nor am I less persuaded that you will agree with me in opinion that there is nothing which can better deserve your patronage than the promotion of science and literature. Knowledge is in every country the surest basis of public happiness. In one in which the measures of government receive their impressions so immediately from the sense of the community as in ours it is proportionably essential. 
>     To the security of a free constitution it contributes in various ways--by convincing those who are intrusted with the public administration that every valuable end of government is best answered by the enlightened confidence of the people, and by teaching the people themselves to know and to value their own rights; to discern and provide against invasions of them; to distinguish between oppression and the necessary exercise of lawful authority; between burthens proceeding from a disregard to their convenience and those resulting from the inevitable exigencies of society; to discriminate the spirit of liberty from that of licentiousness-- cherishing the first, avoiding the last--and uniting a speedy but temperate vigilance against encroachments, with an inviolable respect to the laws. 
>     Whether this desirable object will be best promoted by affording aids to seminaries of learning already established, by the institution of a national university, or by any other expedients will be well worthy of a place in the deliberations of the legislature. 
>     Gentlemen of the House of Representatives: 
>     I saw with peculiar pleasure at the close of the last session the resolution entered into by you expressive of your opinion that an adequate provision for the support of the public credit is a matter of high importance to the national honor and prosperity. In this sentiment I entirely concur; and to a perfect confidence in your best endeavors to devise such a provision as will be truly with the end I add an equal reliance on the cheerful cooperation of the other branch of the legislature. 
>     It would be superfluous to specify inducements to a measure in which the character and interests of the United States are so obviously so deeply concerned, and which has received so explicit a sanction from your declaration. 
>     Gentlemen of the Senate and House of Representatives: 
>     I have directed the proper officers to lay before you, respectively, such papers and estimates as regard the affairs particularly recommended to your consideration, and necessary to convey to you that information of the state of the Union which it is my duty to afford. 
>     The welfare of our country is the great object to which our cares and efforts ought to be directed, and I shall derive great satisfaction from a cooperation with you in the pleasing though arduous task of insuring to our fellow citizens the blessings which they have a right to expect from a free, efficient, and equal government.
>     "), (dbfs:/datasets/sou/17901208.txt,"George Washington 
>
>     December 8, 1790 
>     Fellow-Citizens of the Senate and House of Representatives: 
>     In meeting you again I feel much satisfaction in being able to repeat my congratulations on the favorable prospects which continue to distinguish our public affairs. The abundant fruits of another year have blessed our country with plenty and with the means of a flourishing commerce. 
>     The progress of public credit is witnessed by a considerable rise of American stock abroad as well as at home, and the revenues allotted for this and other national purposes have been productive beyond the calculations by which they were regulated. This latter circumstance is the more pleasing, as it is not only a proof of the fertility of our resources, but as it assures us of a further increase of the national respectability and credit, and, let me add, as it bears an honorable testimony to the patriotism and integrity of the mercantile and marine part of our citizens. The punctuality of the former in discharging their engagements has been exemplary. 
>     In conformity to the powers vested in me by acts of the last session, a loan of 3,000,000 florins, toward which some provisional measures had previously taken place, has been completed in Holland. As well the celerity with which it has been filled as the nature of the terms (considering the more than ordinary demand for borrowing created by the situation of Europe) give a reasonable hope that the further execution of those powers may proceed with advantage and success. The Secretary of the Treasury has my directions to communicate such further particulars as may be requisite for more precise information. 
>     Since your last sessions I have received communications by which it appears that the district of Kentucky, at present a part of Virginia, has concurred in certain propositions contained in a law of that State, in consequence of which the district is to become a distinct member of the Union, in case the requisite sanction of Congress be added. For this sanction application is now made. I shall cause the papers on this very transaction to be laid before you. 
>     The liberality and harmony with which it has been conducted will be found to do great honor to both the parties, and the sentiments of warm attachment to the Union and its present Government expressed by our fellow citizens of Kentucky can not fail to add an affectionate concern for their particular welfare to the great national impressions under which you will decide on the case submitted to you. 
>     It has been heretofore known to Congress that frequent incursions have been made on our frontier settlements by certain banditti of Indians from the northwest side of the Ohio. These, with some of the tribes dwelling on and near the Wabash, have of late been particularly active in their depredations, and being emboldened by the impunity of their crimes and aided by such parts of the neighboring tribes as could be seduced to join in their hostilities or afford them a retreat for their prisoners and plunder, they have, instead of listening to the humane invitations and overtures made on the part of the United States, renewed their violences with fresh alacrity and greater effect. The lives of a number of valuable citizens have thus been sacrificed, and some of them under circumstances peculiarly shocking, whilst others have been carried into a deplorable captivity. 
>     These aggravated provocations rendered it essential to the safety of the Western settlements that the aggressors should be made sensible that the Government of the Union is not less capable of punishing their crimes than it is disposed to respect their rights and reward their attachments. As this object could not be effected by defensive measures, it became necessary to put in force the act which empowers the President to call out the militia for the protection of the frontiers, and I have accordingly authorized an expedition in which the regular troops in that quarter are combined with such drafts of militia as were deemed sufficient. The event of the measure is yet unknown to me. The Secretary of War is directed to lay before you a statement of the information on which it is founded, as well as an estimate of the expense with which it will be attended. 
>     The disturbed situation of Europe, and particularly the critical posture of the great maritime powers, whilst it ought to make us the more thankful for the general peace and security enjoyed by the United States, reminds us at the same time of the circumspection with which it becomes us to preserve these blessings. It requires also that we should not overlook the tendency of a war, and even of preparations for a war, among the nations most concerned in active commerce with this country to abridge the means, and thereby at least enhance the price, of transporting its valuable productions to their markets. I recommend it to your serious reflections how far and in what mode it may be expedient to guard against embarrassments from these contingencies by such encouragements to our own navigation as will render our commerce and agriculture less dependent on foreign bottoms, which may fail us in the very moments most interesting to both of these great objects. Our fisheries and the transportation of our own produce offer us abundant means for guarding ourselves against this evil. 
>     Your attention seems to be not less due to that particular branch of our trade which belongs to the Mediterranean. So many circumstances unite in rendering the present state of it distressful to us that you will not think any deliberations misemployed which may lead to its relief and protection. 
>     The laws you have already passed for the establishment of a judiciary system have opened the doors of justice to all descriptions of persons. You will consider in your wisdom whether improvements in that system may yet be made, and particularly whether an uniform process of execution on sentences issuing from the Federal courts be not desirable through all the States. 
>     The patronage of our commerce, of our merchants and sea men, has called for the appointment of consuls in foreign countries. It seems expedient to regulate by law the exercise of that jurisdiction and those functions which are permitted them, either by express convention or by a friendly indulgence, in the places of their residence. The consular convention, too, with His Most Christian Majesty has stipulated in certain cases the aid of the national authority to his consuls established here. Some legislative provision is requisite to carry these stipulations into full effect. 
>     The establishment of the militia, of a mint, of standards of weights and measures, of the post office and post roads are subjects which I presume you will resume of course, and which are abundantly urged by their own importance. 
>     Gentlemen of the House of Representatives: 
>     The sufficiency of the revenues you have established for the objects to which they are appropriated leaves no doubt that the residuary provisions will be commensurate to the other objects for which the public faith stands now pledged. Allow me, moreover, to hope that it will be a favorite policy with you, not merely to secure a payment of the interest of the debt funded, but as far and as fast as the growing resources of the country will permit to exonerate it of the principal itself. The appropriation you have made of the Western land explains your dispositions on this subject, and I am persuaded that the sooner that valuable fund can be made to contribute, along with the other means, to the actual reduction of the public debt the more salutary will the measure be to every public interest, as well as the more satisfactory to our constituents. 
>     Gentlemen of the Senate and House of Representatives: 
>     In pursuing the various and weighty business of the present session I indulge the fullest persuasion that your consultation will be equally marked with wisdom and animated by the love of your country. In whatever belongs to my duty you shall have all the cooperation which an undiminished zeal for its welfare can inspire. It will be happy for us both, and our best reward, if, by a successful administration of our respective trusts, we can make the established Government more and more instrumental in promoting the good of our fellow citizens, and more and more the object of their attachment and confidence. 
>     GO. WASHINGTON
>     "))

Clearly, the elements are a pair of Strings, where the first String gives the filename and the second String gives the contents in the file.

this can be very helpful to simply loop through the files and take an action, such as counting the number of words per address, as folows:

``` scala
// this just collects the file names which is the first element of the tuple given by "._1" 
souAll.map( fileContentsPair => fileContentsPair._1).collect()
```

>     res64: Array[String] = Array(dbfs:/datasets/sou/17900108.txt, dbfs:/datasets/sou/17901208.txt, dbfs:/datasets/sou/17911025.txt, dbfs:/datasets/sou/17921106.txt, dbfs:/datasets/sou/17931203.txt, dbfs:/datasets/sou/17941119.txt, dbfs:/datasets/sou/17951208.txt, dbfs:/datasets/sou/17961207.txt, dbfs:/datasets/sou/17971122.txt, dbfs:/datasets/sou/17981208.txt, dbfs:/datasets/sou/17991203.txt, dbfs:/datasets/sou/18001111.txt, dbfs:/datasets/sou/18011208.txt, dbfs:/datasets/sou/18021215.txt, dbfs:/datasets/sou/18031017.txt, dbfs:/datasets/sou/18041108.txt, dbfs:/datasets/sou/18051203.txt, dbfs:/datasets/sou/18061202.txt, dbfs:/datasets/sou/18071027.txt, dbfs:/datasets/sou/18081108.txt, dbfs:/datasets/sou/18091129.txt, dbfs:/datasets/sou/18101205.txt, dbfs:/datasets/sou/18111105.txt, dbfs:/datasets/sou/18121104.txt, dbfs:/datasets/sou/18131207.txt, dbfs:/datasets/sou/18140920.txt, dbfs:/datasets/sou/18151205.txt, dbfs:/datasets/sou/18161203.txt, dbfs:/datasets/sou/18171212.txt, dbfs:/datasets/sou/18181116.txt, dbfs:/datasets/sou/18191207.txt, dbfs:/datasets/sou/18201114.txt, dbfs:/datasets/sou/18211203.txt, dbfs:/datasets/sou/18221203.txt, dbfs:/datasets/sou/18231202.txt, dbfs:/datasets/sou/18241207.txt, dbfs:/datasets/sou/18251206.txt, dbfs:/datasets/sou/18261205.txt, dbfs:/datasets/sou/18271204.txt, dbfs:/datasets/sou/18281202.txt, dbfs:/datasets/sou/18291208.txt, dbfs:/datasets/sou/18301206.txt, dbfs:/datasets/sou/18311206.txt, dbfs:/datasets/sou/18321204.txt, dbfs:/datasets/sou/18331203.txt, dbfs:/datasets/sou/18341201.txt, dbfs:/datasets/sou/18351207.txt, dbfs:/datasets/sou/18361205.txt, dbfs:/datasets/sou/18371205.txt, dbfs:/datasets/sou/18381203.txt, dbfs:/datasets/sou/18391202.txt, dbfs:/datasets/sou/18401205.txt, dbfs:/datasets/sou/18411207.txt, dbfs:/datasets/sou/18421206.txt, dbfs:/datasets/sou/18431206.txt, dbfs:/datasets/sou/18441203.txt, dbfs:/datasets/sou/18451202.txt, dbfs:/datasets/sou/18461208.txt, dbfs:/datasets/sou/18471207.txt, dbfs:/datasets/sou/18481205.txt, dbfs:/datasets/sou/18491204.txt, dbfs:/datasets/sou/18501202.txt, dbfs:/datasets/sou/18511202.txt, dbfs:/datasets/sou/18521206.txt, dbfs:/datasets/sou/18531205.txt, dbfs:/datasets/sou/18541204.txt, dbfs:/datasets/sou/18551231.txt, dbfs:/datasets/sou/18561202.txt, dbfs:/datasets/sou/18571208.txt, dbfs:/datasets/sou/18581206.txt, dbfs:/datasets/sou/18591219.txt, dbfs:/datasets/sou/18601203.txt, dbfs:/datasets/sou/18611203.txt, dbfs:/datasets/sou/18621201.txt, dbfs:/datasets/sou/18631208.txt, dbfs:/datasets/sou/18641206.txt, dbfs:/datasets/sou/18651204.txt, dbfs:/datasets/sou/18661203.txt, dbfs:/datasets/sou/18671203.txt, dbfs:/datasets/sou/18681209.txt, dbfs:/datasets/sou/18691206.txt, dbfs:/datasets/sou/18701205.txt, dbfs:/datasets/sou/18711204.txt, dbfs:/datasets/sou/18721202.txt, dbfs:/datasets/sou/18731201.txt, dbfs:/datasets/sou/18741207.txt, dbfs:/datasets/sou/18751207.txt, dbfs:/datasets/sou/18761205.txt, dbfs:/datasets/sou/18771203.txt, dbfs:/datasets/sou/18781202.txt, dbfs:/datasets/sou/18791201.txt, dbfs:/datasets/sou/18801206.txt, dbfs:/datasets/sou/18811206.txt, dbfs:/datasets/sou/18821204.txt, dbfs:/datasets/sou/18831204.txt, dbfs:/datasets/sou/18841201.txt, dbfs:/datasets/sou/18851208.txt, dbfs:/datasets/sou/18861206.txt, dbfs:/datasets/sou/18871206.txt, dbfs:/datasets/sou/18881203.txt, dbfs:/datasets/sou/18891203.txt, dbfs:/datasets/sou/18901201.txt, dbfs:/datasets/sou/18911209.txt, dbfs:/datasets/sou/18921206.txt, dbfs:/datasets/sou/18931203.txt, dbfs:/datasets/sou/18941202.txt, dbfs:/datasets/sou/18951207.txt, dbfs:/datasets/sou/18961204.txt, dbfs:/datasets/sou/18971206.txt, dbfs:/datasets/sou/18981205.txt, dbfs:/datasets/sou/18991205.txt, dbfs:/datasets/sou/19001203.txt, dbfs:/datasets/sou/19011203.txt, dbfs:/datasets/sou/19021202.txt, dbfs:/datasets/sou/19031207.txt, dbfs:/datasets/sou/19041206.txt, dbfs:/datasets/sou/19051205.txt, dbfs:/datasets/sou/19061203.txt, dbfs:/datasets/sou/19071203.txt, dbfs:/datasets/sou/19081208.txt, dbfs:/datasets/sou/19091207.txt, dbfs:/datasets/sou/19101206.txt, dbfs:/datasets/sou/19111205.txt, dbfs:/datasets/sou/19121203.txt, dbfs:/datasets/sou/19131202.txt, dbfs:/datasets/sou/19141208.txt, dbfs:/datasets/sou/19151207.txt, dbfs:/datasets/sou/19161205.txt, dbfs:/datasets/sou/19171204.txt, dbfs:/datasets/sou/19181202.txt, dbfs:/datasets/sou/19191202.txt, dbfs:/datasets/sou/19201207.txt, dbfs:/datasets/sou/19211206.txt, dbfs:/datasets/sou/19221208.txt, dbfs:/datasets/sou/19231206.txt, dbfs:/datasets/sou/19241203.txt, dbfs:/datasets/sou/19251208.txt, dbfs:/datasets/sou/19261207.txt, dbfs:/datasets/sou/19271206.txt, dbfs:/datasets/sou/19281204.txt, dbfs:/datasets/sou/19291203.txt, dbfs:/datasets/sou/19301202.txt, dbfs:/datasets/sou/19311208.txt, dbfs:/datasets/sou/19321206.txt, dbfs:/datasets/sou/19340103.txt, dbfs:/datasets/sou/19350104.txt, dbfs:/datasets/sou/19360103.txt, dbfs:/datasets/sou/19370106.txt, dbfs:/datasets/sou/19380103.txt, dbfs:/datasets/sou/19390104.txt, dbfs:/datasets/sou/19400103.txt, dbfs:/datasets/sou/19410106.txt, dbfs:/datasets/sou/19420106.txt, dbfs:/datasets/sou/19430107.txt, dbfs:/datasets/sou/19440111.txt, dbfs:/datasets/sou/19450106.txt, dbfs:/datasets/sou/19460121.txt, dbfs:/datasets/sou/19470106.txt, dbfs:/datasets/sou/19480107.txt, dbfs:/datasets/sou/19490105.txt, dbfs:/datasets/sou/19500104.txt, dbfs:/datasets/sou/19510108.txt, dbfs:/datasets/sou/19520109.txt, dbfs:/datasets/sou/19530107.txt, dbfs:/datasets/sou/19530202.txt, dbfs:/datasets/sou/19540107.txt, dbfs:/datasets/sou/19550106.txt, dbfs:/datasets/sou/19560105.txt, dbfs:/datasets/sou/19570110.txt, dbfs:/datasets/sou/19580109.txt, dbfs:/datasets/sou/19590109.txt, dbfs:/datasets/sou/19600107.txt, dbfs:/datasets/sou/19610112.txt, dbfs:/datasets/sou/19610130.txt, dbfs:/datasets/sou/19620111.txt, dbfs:/datasets/sou/19630114.txt, dbfs:/datasets/sou/19640108.txt, dbfs:/datasets/sou/19650104.txt, dbfs:/datasets/sou/19660112.txt, dbfs:/datasets/sou/19670110.txt, dbfs:/datasets/sou/19680117.txt, dbfs:/datasets/sou/19690114.txt, dbfs:/datasets/sou/19700122.txt, dbfs:/datasets/sou/19710122.txt, dbfs:/datasets/sou/19720120.txt, dbfs:/datasets/sou/19730202.txt, dbfs:/datasets/sou/19740130.txt, dbfs:/datasets/sou/19750115.txt, dbfs:/datasets/sou/19760119.txt, dbfs:/datasets/sou/19770112.txt, dbfs:/datasets/sou/19780119.txt, dbfs:/datasets/sou/19790125.txt, dbfs:/datasets/sou/19800121.txt, dbfs:/datasets/sou/19810116.txt, dbfs:/datasets/sou/19820126.txt, dbfs:/datasets/sou/19830125.txt, dbfs:/datasets/sou/19840125.txt, dbfs:/datasets/sou/19850206.txt, dbfs:/datasets/sou/19860204.txt, dbfs:/datasets/sou/19870127.txt, dbfs:/datasets/sou/19880125.txt, dbfs:/datasets/sou/19890209.txt, dbfs:/datasets/sou/19900131.txt, dbfs:/datasets/sou/19910129.txt, dbfs:/datasets/sou/19920128.txt, dbfs:/datasets/sou/19930217.txt, dbfs:/datasets/sou/19940125.txt, dbfs:/datasets/sou/19950124.txt, dbfs:/datasets/sou/19960123.txt, dbfs:/datasets/sou/19970204.txt, dbfs:/datasets/sou/19980127.txt, dbfs:/datasets/sou/19990119.txt, dbfs:/datasets/sou/20000127.txt, dbfs:/datasets/sou/20010227.txt, dbfs:/datasets/sou/20010920.txt, dbfs:/datasets/sou/20020129.txt, dbfs:/datasets/sou/20030128.txt, dbfs:/datasets/sou/20040120.txt, dbfs:/datasets/sou/20050202.txt, dbfs:/datasets/sou/20060131.txt, dbfs:/datasets/sou/20070123.txt, dbfs:/datasets/sou/20080128.txt, dbfs:/datasets/sou/20090224.txt, dbfs:/datasets/sou/20100127.txt, dbfs:/datasets/sou/20110125.txt, dbfs:/datasets/sou/20120124.txt, dbfs:/datasets/sou/20130212.txt, dbfs:/datasets/sou/20140128.txt, dbfs:/datasets/sou/20150120.txt, dbfs:/datasets/sou/20160112.txt)

Let us find the number of words in each of the SoU addresses next (we need to work with Strings inside the closure!).

``` scala
val wcs = souAll.map( fileContentsPair => 
  {
    val wc = fileContentsPair._2
                             .replaceAll("\\s+", " ") //replace multiple whitespace characters (including space, tab, new line, etc.) with one whitespace " "
                             .replaceAll("""([,?.!:;])""", "") // replace the following punctions characters: , ? . ! : ; . with the empty string ""
                             .toLowerCase() // converting to lower-case
                             .split(" ") // split each word separated by white space
                             .size // find the length of array
    wc
  }    
)      
```

>     wcs: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[17859] at map at <console>:36

``` scala
wcs.collect()
```

>     res65: Array[Int] = Array(1088, 1408, 2308, 2103, 1970, 2920, 1991, 2872, 2062, 2223, 1510, 1377, 3229, 2206, 2284, 2106, 2948, 2873, 2399, 2693, 1836, 2451, 2280, 3247, 3263, 2119, 3159, 3372, 4432, 4382, 4716, 3446, 5828, 4739, 6387, 8423, 9009, 7754, 6993, 7319, 10528, 15077, 7194, 7880, 7906, 13451, 10814, 12360, 11444, 11484, 13425, 8985, 8245, 8406, 8031, 9314, 16117, 18226, 16416, 21295, 7624, 8323, 13248, 9927, 9592, 10139, 11615, 10478, 13656, 16352, 12341, 14030, 6981, 8397, 6116, 5994, 9223, 7132, 11986, 9822, 7708, 8742, 6464, 3995, 10028, 9200, 12209, 6799, 8023, 7887, 11638, 6703, 3828, 3073, 3790, 8953, 19751, 15140, 5294, 9025, 13006, 11527, 16297, 13683, 12288, 15897, 14676, 15448, 12115, 20213, 15138, 19139, 19600, 9761, 14881, 17411, 25038, 23580, 27387, 19388, 13895, 6769, 23710, 25156, 3557, 4538, 7691, 2122, 3918, 5470, 4760, 2710, 5609, 5749, 6706, 6969, 10848, 10313, 8782, 8065, 10998, 4551, 5691, 4215, 2229, 3521, 3816, 2736, 4683, 3742, 3176, 3294, 3476, 4539, 3761, 8130, 27728, 6039, 5097, 3403, 5132, 3994, 5340, 9605, 6947, 5980, 7240, 8251, 4135, 4917, 4875, 5634, 6194, 5171, 6442, 5332, 3193, 4400, 5261, 7117, 4861, 4113, 4460, 4480, 3972, 1660, 5159, 4109, 4957, 4646, 4585, 3248, 3401, 33569, 5200, 5577, 4978, 4255, 3512, 3808, 4860, 4827, 3776, 3771, 4724, 7013, 7406, 9149, 6289, 6748, 7289, 7478, 7393, 4401, 2972, 3830, 5378, 5177, 5055, 5327, 5604, 5758, 5945, 7111, 6945, 6992, 6472, 6783, 6732, 5443)

HOMEWORK
--------

-   HOWEWORK WordCount 1: `sortBy`
-   HOMEWROK WordCount 2: `dbutils.fs`

##### HOMEWORK WordCount 1. `sortBy`

Let's understand `sortBy` a bit more carefully.

``` scala
val example = "Master, Master!   It's me, Sméagol... mhrhm*%* But they took away our precious, they wronged us. Gollum will protect us..., Master, it's me Sméagol."
```

>     example: String = Master, Master!   It's me, Sméagol... mhrhm*%* But they took away our precious, they wronged us. Gollum will protect us..., Master, it's me Sméagol.

``` scala
val words = example.replaceAll("\\s+", " ") //replace multiple whitespace characters (including space, tab, new line, etc.) with one whitespace " "
       .replaceAll("""([,?.!:;])""", "") // replace the following punctions characters: , ? . ! : ; . with the empty string ""
       .toLowerCase() // converting to lower-case
       .split(" ")
```

>     words: Array[String] = Array(master, master, it's, me, sméagol, mhrhm*%*, but, they, took, away, our, precious, they, wronged, us, gollum, will, protect, us, master, it's, me, sméagol)

``` scala
val rddWords = sc.parallelize(words)
```

>     rddWords: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[1790] at parallelize at <console>:38

``` scala
rddWords.take(10)
```

>     res28: Array[String] = Array(master, master, it's, me, sméagol, mhrhm*%*, but, they, took, away)

``` scala
val wordCounts = rddWords
                  .map(x => (x,1))
                  .reduceByKey(_+_)
```

>     wordCounts: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[1815] at reduceByKey at <console>:42

``` scala
val top10 = wordCounts.sortBy(_._2, false).take(10)
```

>     top10: Array[(String, Int)] = Array((master,3), (us,2), (sméagol,2), (they,2), (me,2), (it's,2), (precious,1), (away,1), (will,1), (our,1))

Make your code easy to read for other developers ;)
Use 'case classes' with well defined variable names that everyone can understand

``` scala
val top10 = wordCounts.sortBy({
  case (word, count) => count
}, false).take(10)
```

>     top10: Array[(String, Int)] = Array((master,3), (us,2), (sméagol,2), (they,2), (me,2), (it's,2), (precious,1), (away,1), (will,1), (our,1))

If you just want a total count of all words in the file

``` scala
rddWords.count
```

>     res29: Long = 23

##### HOMEWORK WordCount 2: `dbutils.fs`

Have a brief look at what other commands dbutils.fs supports. We will introduce them as needed.

``` scala
dbutils.fs.help // some of these were used to ETL this data into dbfs:/datasets/sou 
```

<p class="htmlSandbox"><div class = "ansiout"><b>dbutils.fs</b> provides utilities for working with FileSystems. Most methods in
this package can take either a DBFS path (e.g., "/foo"), an S3 URI ("s3n://bucket/"), or
another Hadoop FileSystem URI.

For more info about a method, use <b>dbutils.fs.help("methodName")</b>.

In notebooks, you can also use the %fs shorthand to access DBFS. The %fs shorthand maps
straightforwardly onto dbutils calls. For example, "%fs head --maxBytes=10000 /file/path"
translates into "dbutils.fs.head("/file/path", maxBytes = 10000)".
      <h3>fsutils</h3><b>cp(from: String, to: String, recurse: boolean = false): boolean</b> -> Copies a file or directory, possibly across FileSystems<br /><b>head(file: String, maxBytes: int = 65536): String</b> -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8<br /><b>ls(dir: String): SchemaSeq</b> -> Lists the contents of a directory<br /><b>mkdirs(dir: String): boolean</b> -> Creates the given directory if it does not exist, also creating any necessary parent directories<br /><b>mv(from: String, to: String, recurse: boolean = false): boolean</b> -> Moves a file or directory, possibly across FileSystems<br /><b>put(file: String, contents: String, overwrite: boolean = false): boolean</b> -> Writes the given String out to a file, encoded in UTF-8<br /><b>rm(dir: String, recurse: boolean = false): boolean</b> -> Removes a file or directory<br /><br /><h3>cache</h3><b>cacheFiles(files: Seq): boolean</b> -> Caches a set of files on the local SSDs of this cluster<br /><b>cacheTable(tableName: String): boolean</b> -> Caches the contents of the given table on the local SSDs of this cluster<br /><b>uncacheFiles(files: Seq): boolean</b> -> Removes the cached version of the files<br /><b>uncacheTable(tableName: String): boolean</b> -> Removes the cached version of the given table from SSDs<br /><br /><h3>mount</h3><b>chmod(path: String, user: String, permission: String): void</b> -> Modifies the permissions of a mount point<br /><b>grants(path: String): SchemaSeq</b> -> Lists the permissions associated with a mount point<br /><b>mount(source: String, mountPoint: String, encryptionType: String = "", owner: String = null): boolean</b> -> Mounts the given source directory into DBFS at the given mount point<br /><b>mounts(): SchemaSeq</b> -> Displays information about what is mounted within DBFS<br /><b>refreshMounts(): boolean</b> -> Forces all machines in this cluster to refresh their mount cache, ensuring they receive the most recent information<br /><b>unmount(mountPoint: String): boolean</b> -> Deletes a DBFS mount point<br /><br /></div></p>