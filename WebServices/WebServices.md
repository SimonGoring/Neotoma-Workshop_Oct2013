**Navigation - [Home](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/README.md) - [Intro to R](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/IntroToR/IntroR_1.md) - [Web Services & APIs](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/WebServices/WebServices.md) - [Basic Search](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/BasicSearches/BasicSearches.md) - [Pollen Objects](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/PollenObjects.md) - [Manipulating Pollen](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/ManipulatingPollen.md)**

-------------------------------------

Using Web Services and the Neotoma API
========================================================

`neotoma` is an R package that uses an [API](http://en.wikipedia.org/wiki/Application_programming_interface) (Application Programming Interface).  An API allows different applications to talk to one another.  In most cases you will see an API being used in a web environment, where commands are passed through the URL using particular, fixed, variables.

The really great thing about R and web-based APIs is that it improves our ability to perform reproducible research.  At the end of this workshop you will have the R code required to do several pieces of analysis and, because we are interfacing with Neotoma, you and I will both have the same data sets without ever having to swap emails, USB drives or floppy disks.  If these were exciting and amazing results (and even if they were mediocre, ho-hum results) this would mean that reviewers could be sure the results were accurate, and people hoping to learn from or build on the results could do so more easily.  There is an excellent paper by Roger Peng in *Science* (PDF [here](http://omsj.org/reports/peng%202011.pdf)) that talks more about the principles and importance of reproducible research in the computational sciences.  If you need more proof (do you?) Piwowar and Vision ([2013](https://peerj.com/articles/175/)) report higher rates of citation for papers with open data.

What does a web API look like?
--------------------------------------------------------
You use APIs on a daily basis and you might not even know it.  Take a second right now to go to Google and type in a search term, then hit enter.  You should see something like this:

```r
https://www.google.com/#hl=en&safe=on&sclient=psy-ab&q=why+is+a+horse's+head+in+my+bed%3F&oq=why+is+a+horse's+head+in+my+bed%3F&fp=c5ac5d05622acf2c
```
![Horse head picture](../images/horsehead-6.png)

**Figure 1**. *This seems like a good time to use my knowledge of Google's API.*

The URL string is both an address and a command to the API that interfaces between your web browser and Google's databases.

You can see I searched for the string "why is a horse's head in my bed", it is prefaced by "q=".  Google's servers know that when they are reading the URL everything followed by the hash sign (**#**) is part of a set of variables.

Google has published the Custom Search API on the web [here](https://developers.google.com/custom-search/v1/cse/list) so for your first exercise you should build your own search.

**Exercise 1:** Build a search request using the Google search API, try to use at least three variables.  What did you search for?  Did it work?


Neotoma API
--------------------------------------------------------
[Neotoma](http://www.neotomadb.org) has the same kind of API, although it is specialized to serve up the paleoecological records stored in the database.  We'll discuss the API in more detail, but if you want to spend time on your own, you can check out the reference documents [here](http://api.neotomadb.org/doc/about).

The Neotoma API is just a way for applications on your computer (such as R) to interact with the Neotoma database.  It allows you to search for sites, publications, authors and data sets using the Neotoma URL.  For example the *sites* command:

```
http://api.neotomadb.org/v1/data/sites?altmin=0&altmax=100
```

will return a crazy wall of text.  If you look at it more closely you will see that it starts with the word *success*, and then contains site data for all sites in the Neotoma database that have elevations between 0 and 100ft.  Try changing *altmax=100* to *altmax=high*.  What happens then?  Now, try to figure out that the highest elevation site in Neotoma is (hint, it shows up as 2 separate sites), what is the site name and what is the elevation?

If you can figure that out, try to play around with some of the other API calls using a browser.  Once you see how silly it is to do that over and over again, let's move to R:

**Navigation - [Home](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/README.md) - [Intro to R](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/IntroToR/IntroR_1.md) - [Web Services & APIs](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/WebServices/WebServices.md) - [Basic Search](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/BasicSearches/BasicSearches.md) - [Pollen Objects](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/PollenObjects.md) - [Manipulating Pollen](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/ManipulatingPollen.md)**
