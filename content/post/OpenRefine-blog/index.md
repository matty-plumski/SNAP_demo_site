---
title: OpenRefine Tutorial
date: 2020-12-01
---

#  Open Refine With [Powerhouse Museum](https://www.maas.museum/powerhouse-museum/) collection data

Based on [this video](https://)
> Discover how to prep data for your next machine learning project using OpenRefine.

1. Install OpenRefine and open in browser: http://127.0.0.1:3333/
2. Create project and import data (phm-collection.tsv) - `either download and upload, or add via Get Data From > Web addresses URL > https://data.freeyourmetadata.org/powerhouse-museum/phm-collection.tsv`  *tip go to https://data.freeyourmetadata.org/powerhouse-museum/ and right click on file `phm-collection` and choose `copy link address` option.
3. Notice essentially dealing with a giant spreadsheet with rows and columns, each column has a menu with options for manipulating and filtering the data for that column. On the left hand side options for facet and filter - in data science, faceting allows different views of your data by breakin it up, defined by a specific characteristic - like many sides of a gem - hence the OpenRefine logo. *concept of viewing is interesting here - we have a copy of data, different views etc*
> facet
> /ˈfasɪt,ˈfasɛt/
> noun
> one side of something many-sided, especially of a cut gem.
> "a blue and green jewel that shines from a million facets"
> 
> a particular aspect or feature of something.
> "a philosophy that extends to all facets of the business"

![OR logo](https://upload.wikimedia.org/wikipedia/commons/4/4b/OpenRefine_New_Logo.png)

Two main types in OpenRefine *text facet* and *numeric facet*

4. Create `numeric facet` on ``Record ID`` column
5. "No numeric value present" - you need to tell OpenRefine to treat this information as numeric data by going to ``Edit cells > Common transforms > To number `` You can now see a histogram that shows the value range of the Record ID (from 0 - 500 000) ***tip** - you can drag the handles to select a specific range*
6. **Remove blank rows**
Note that there are two non-numeric values - this indicates there are blank rows in the data.To remove them, uncheck the `numeric` check box in the histogram, then click on the menu (triangle) in the all column and go to `Edit rows > Remove all matching rows` ~~Note: number of rows should now be reduced by two?~~
7. **Remove duplicate rows**
* Choose column in your dataset with a unique identifier, in this case we're going to choose Record ID (*tip: click x on top left corner of histogram to remove from view*)
* Sort duplicate IDs so they are next to each other by going to `Record ID` menu, then `sort ` select `numbers` and check `smallest first` then `OK`
* Note - *In OpenRefine sorting is merely a visual aid for helping you understand and clean data. to make changes made permanent, you need to click on a new menu 
* `Sort` that should have now appeared in the top centre of the interface. To make changes permanent, click on `Sort>Reorder rows permanently`. (note yellow highlighter action alert that momentarily pops up - denoting an action has been performed)
* Identical rows should now be next to each other. We can now use a feature called `blank down`. This leaves the first instance of a duplicate record intact, and turns all subsequent duplicate entries into blank cells. To do this, Go to `Record ID > Edit cells >Blank down` Alert: 20 duplicate record IDs (cells) have now been blanked (Running total 41915 rows)
* Now we want to remove all rows where the Record ID has been blanked. Go to `Record ID > Facet > Customized facets > Facet by blank` then in the left hand Facet/Filter menu, click on `true` to select the 20 rows where the Record IDs have been blanked.
* To remove them go to `All > Edit rows > Remove all matching rows`. The alert should tell us 20 rows have been removed. (running total 41895)
*tip note undo-redo functionality* - go back to any previous point in time
8. **Fix categories**
* Scroll to `Categories` column. Note each record has multiple categories separated by a pipe (|) operator (*possible place to comment on file types, and the 's' in csv and tsv*) and that there are numerous spelling mistakes and inconsistencies (e.g. capitalisations) in the data that we want to clean.
* To fix, we first want to split each value into its own cell, this is a process called **Atomisation**. To do this, under the `Categories` menu and choose `Edit cells > Split multi-valued cells` then change the separator value from `,` to `|` (important - make sure there are no extra spaces) then click `OK`. Note - we've gone from 41895 rows to 93181 rows. We will clean and remerge later.
* Now go to `Catgories > Facet > Text facet` (note if there's a warning too many choices to display, up the limit via `Set choice count limit`)
* you should now be able to see a list of every unique category. Scroll to the bottom to see `(blank)` with 821 records (this means no category was specified). Scroll through the items to see problems with the data - multiple entries due to inconsistent spelling and capitalisation when we really only want one category rather than unwanted variants (in most cases). Items which should be in the same category are separated.
* To fix this we use a process called clustering. In the `Facet/Filter` left menu, click on the `Cluster` button. This automates the process of finding similar items, and gives different options to collate and combine these
* Under Method, leave this as `key collision` but it's also useful to explore the other option `nearest neighbour` to get a sense of how clustering works. The same goes for the `Keying Function`. For this demonstration we'll use `ngram-fingerprint`
* Set `Ngram Size` to `4` to accomodate longer phrases than the default `2`
* Click on `Select All` in the bottom left of the window, but uncheck the . On the right hand side you can change to the correct value of a cell manually if required. 
* Once happy, choose `Merge Selected & Re-Cluster`. Only the unchecked first entry should remain. Click on the `close` button.
* Now we've removed inconsistences from categories, we're going to merge them back into a list for each record. Under `Categories` select `Edit cells > Join multi-valued cells`. Set the separate to `|` (a pipe). (**important** - make sure there are no extra white spaces!!) We should now be back to **41895** records (rows)

9. **Remove repeated categories from each row using [GREL](https://docs.openrefine.org/manual/grelfunctions)**
* GREL stands for General Refine Expression Language, similar to regex. It allows you to apply programmatic transformation to cells and do custom faceting. 
* Under the `Categories` menu select `Edit cells > Transform` to bring up the GREL editor.
* In the `Expression` field type `value.split('|').uniques().join('|')` then click `OK`. Explain what each step does. Notice we've updated x (1461) number of cells in the `Categories` column.
10. **Export the cleaned dataset**
* Click on `Export` menu in the top right of the interface and select `tab-separated value`. This downloads the cleaned data file as a .tsv file on your local computer.