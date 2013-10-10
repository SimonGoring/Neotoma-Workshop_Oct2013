**Navigation - [Home](../README.html) - [Basic Search](../BasicSearches/BasicSearches.html)**

What is a 'Pollen Object' and how do I get one?
========================================================
The `neotoma` package tries to collect and return data in a format that makes sense to the users and can be easily used by other packages.  The different functions in `neotoma` return different types of objects.  You already saw that `get_sites` returns a table that is a `data.frame`.  This is because the variables of interest for sites are both numeric (latitude and longitude) and character (site descritption).

Function | Returned variable
get_contacts | `data.frame` with contact information for investigators.
get_datasets | `list` of `lists` with information about each dataset.
get_download | `list` including assemblage information.
get_publication | `data.frame` with publication information.
get_sites | `data.frame` with site information.
get_table | `data.frame`, content depends on the table of interest.
get_taxa | `data.frame` with taxon information.
