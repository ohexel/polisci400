# PoliSci400
Analysis of Kim and Grofman's The Political Science 400

Data from [Kim and Grofman, The Political Science 400](https://www.cambridge.org/core/journals/ps-political-science-and-politics/article/political-science-400-with-citation-counts-by-cohort-gender-and-subfield/C1EDBF7220760F01A5C4A685DB3B3F44/core-reader): "a database with the 4,089 currently tenured or tenure-track faculty, along with emeritus faculty, at US PhD-granting departments ca. 2017-2018."

The authors do not publicly provide their data as far as I can see. [Appendix A](https://static.cambridge.org/resource/id/urn:cambridge.org:id:binary:20190104104300674-0444:sup-mat:20190104104300674-0444:S1049096518001786sup002.pdf) provides data on the 400 most cited faculty. The table containts names, current (2017) school of employment, PhD-granting institution, year of PhD, and citation counts. According to the authors, the full dataset also contains fields of interest and gender.

## Fun stuff
My first impulse (nothing to do with the fact that I just copied some code from somewhere else) was to extract the text and collapse the resulting list of lists into one big list of character vectors.

`full_text <- unlist(strsplit(txt, "\\n"))`

Now we have a list of character vectors. Each vector (except the first three) is one person. To break this down further, we could try `strsplit()` together with a regular expression like so: `strsplit(text, "[[:space:]]{n,})`. Unfortunately, neither `n=1` nor `n=2` allow us to consistently distinguish between "U of Washington" and "MyLastName MyUniversity." Now, at first glance, it looks like the information is in a fixed-width format. So we could write all the vectors to a text file and import the data back into R via `read.fwf`. Unfortunately, it turns out that the length of the vectors varies.

`table(nchar(full_text))`

But lo, if we look at the data, we notice that the length of the vectors is identical *within* each page. So let's back up and read in the data page by page. Looking at the data, we see that they're formatted as if each variable is left-aligned within a column. So, we need to find the left-hand word boundaries and the word lengths. If we define word boundaries as "a space followed by one or more alphanum- eric characters," we have a problem. 

Problem: there are word boundaries that we want to ignore: middle names and compound university names. 

Solution: within a page, all rows should have certain left-hand word boundaries in common, namely the ones that represent "variable boundaries." 

Problem: Turns out that the last column is right-aligned, i.e. on p.6 where citation counts go from 5 to 4 digits, our "common left-hand boundaries" rule will merge PhD year and citation count.  
