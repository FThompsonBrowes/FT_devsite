---
layout: project
title: 'Implementing Tableaus JavaScript API'
caption: How to filter multiple dashboards together using iFrames
description: > 
    Building on the Tableau Knowledge Base resources, this post is meant to provide detailed instructions and the code required to implement Tableau's JavaScript API and filter multiple dashboards simutaneously on your personal or company website. 
date: '01-01-2019'
image: 
  path: /assets/img/projects/crc960-asset.png
  srcset: 
    1920w: /assets/img/projects/crc960-asset.png
    960w:  /assets/img/projects/crc480-asset.png
    480w:  /assets/img/projects/crc240-asset.png
links:
  - title: Link to GitHub Code
    url: https://github.com/FThompsonBrowes/Tableau_JavaScript_API
sitemap: false
---

Tableau Knowledge Base offers a basic introduction to "Filter a Dashboard From Another Dashboard" with resources available <a href="https://kb.tableau.com/articles/howto/filtering-a-dashboard-using-action-filters-passed-from-another-dashboard" target="_blank">here</a>.

This project for CanWaCH utilized the Tableau JavaScript API to filter a series of four dashboards simutaneously. The Tableau GitHub Repository with the full code in English and French is linked above. This post in intended to provide more thorough instructions for anyone who wishes to utilize the same method for their own projects. 
  
View the working set of dashboards here: 
  
  * <a href="http://insertlink" target="_blank">Set of 4 Tableau Dashboards - EN</a>
  * <a href="http://insertlink" target="_blank">Set of 4 Tableau Dashboards - FR</a>

Before you begin, this post requires: 

  1) a Tableau Public account
  
  2) create and upload all the Tableau Workbooks that you want to filter together to your Tableau Public account. Each workbook should contain **1 tableau dashboard only**. 

*Note: The set of Tableau Workbooks must contain a shared data field and all workbooks must use the shared data field as a filter on all **tableau workbook sheets**.*

### Step 1: 
The first step is to create an iFrame for a new page on your website using the site generator of your choice (WordPress, Drupal, Jekyll etc.). 

*Note: Step 1 includes setting up the first iFrame and Tableau Dashboard. The Tableau Workbook URL used in the first iFrame will display at the top of the webpage. The remaining iFrames and dashboards will be added in Step 3 below. The Tableau Workbook URL used in the second iFrame will display below the first etc. etc. Step 3 determines the order the dashboards will appear.*

```java
<div class="node__content l-embed">
        
            <div class="clearfix text-formatted field field--name-field-embed-code field--type-text-long field--label-hidden field__item"><script src="https://public.tableau.com/javascripts/api/tableau-2.min.js"></script><script src="https://public.tableau.com/javascripts/api/tableau-2.6.0.min.js"></script>
<div id="vizContainer"></div>
<form method="get" id="mainform" target="first_iframe" action="INSERT YOUR FIRST TABLEAU WORKBOOK URL HERE">
<input type="hidden" name=":embed" value="yes">
<input type="hidden" name=":showVizHome" value="no">
<input type="hidden" name=":display_count" value="yes">
```

### Step 2: 
Build the filter that all dashboards will use. This will be the same data field that all workbooks share and has been added as a filter to all the sheets of your Tableau Workbooks. It is also possible to use more than one filter. 

**Simple Filter:**

The below code uses a data field "selGoC" in the Tableau Workbooks. This is a terrible name if you're not the person who made this visual! See the markdown in the code below for how to update the label for your website filter. 

```java
  /*This line renames the Tableau data field from "selGoC" in the workbook to use "Funding Source" as the name of the filter on the website.*/
  <label for="selGoC">Funding Source:</label> 
  <select id="selGoC" name="GoC"> 
  /*The following are the three filter options in Tableau. Again the default options in the Tableau workbook of "All, 0, 1" are not helpful to the reader. This renames all the defaults to useful labels in the filter drop-down that will appear on the website.*/
  <option value="">All</option>
  <option value="1">Government of Canada</option>
  <option value="0">Other Funding</option>
  /*This will hide option 2 so that it does not appear in the dropdown filter on the website for improved UX.*/
  <!-- <option value="2">2</option> --> 
</select>
```

*If you only have 1 filter also include the below three lines of code. If you have more than 1 filter, only add the below three lines of code once after all filters have been entered.* 

```java
</select>
<input type="submit" id="mainsubmit" value="Show Country Snapshot">
</form>
```

**Complex Filter:** 

If your filter has many options and is a long drop-down list (Ex. all the countries in the world), you can build a more complex filter such as the code below: 

```java
<label for="selCountry">Country:</label>
/*Note: The Parameters.Country is a Tableau Parameter Action included in the Workbooks and Dashboards!*/
<select id="selCountry" name="Parameters.Country"> 
   <option value="Afghanistan">Afghanistan</option>
   <option value="Åland Islands">Åland Islands</option
   /*...<Add more lines with remaining choices following the same format as above>*/
</select>
<input type="submit" id="mainsubmit" value="Show Country Snapshot">
</form>
```

### Step 3: 
Next, we need to set-up the code to add the remaining iFrames and dashboards. Repeat the following code for all remaining workbooks. You'll notice if we refer back to Step 1, the code includes: 

```java
  target="first_iframe" action="https://public.tableau.com/views/INSERT YOUR FIRST TABLEAU WORKBOOK URL HERE""> 
```

When we repeat the code from step 1, change this line to reference the **second workbook** that you want to use and change target="first_iframe" to target="second_iframe" instead. You can also refer to the GitHub Repo link above for the full code. 

Code Example: 

```java
<div style="display: none; hidden: true;" id="hidden-frame">Nothing here...<form method="get" id="mainform" target="second_iframe" action="https://public.tableau.com/views/INSERT YOUR SECOND TABLEAU WORKBOOK URL HERE">
<input type="hidden" name=":embed" value="yes">
<input type="hidden" name=":showVizHome" value="no">
<input type="hidden" name=":display_count" value="yes">
```

Next, repeat the code for the filters from above. This will enable the filters to work on the second Tableau Workbook. 

```java
<label for="selGoC">Funding Source:</label>
<select id="selGoC" name="GoC">
  <option value="">All</option>
  <option value="1">Government of Canada</option>
  <option value="0">Other Funding</option>
  <!-- <option value="2">2</option> -->
</select>
```

Once all dashboards have been added, remember to add the following code:

```java
</select>
<input type="submit" id="mainsubmit" value="Show Country Snapshot">
</form>
```

### Step 4: 
To complete the filters for our webpage, we need to create a function so that the page reloads when the filters are selected.

```java
</div>
<script type="text/javascript">
window.onload = function() {

document.getElementById("mainform").addEventListener("submit", submitForm);

function submitForm(e) {
  if(e.preventDefault) {
    e.preventDefault();
  }
```

### Step 5:
Now that we have the filter boxes created, we need to connect the filters to the Tableau dashboards. The below code is the "magic" that will apply the chosen filters to all the iFrames and each Tableau Workbook per iFrame. 

The code below includes a section per iFrame and Tableau Workbook. For this example, the code is included for the first two Tableau Public Workbooks for brevity *(The full code in available on the GitHub repo linked above for all 4 separate iFrames and Tableau Workbooks)*. 

```java
 var hiddenDiv = document.getElementById("hidden-frame");

  this.target = 'first_iframe';
  this.action= 'https://public.tableau.com/views/Beta2020_6_EN_CS_p1of4_V2_16886160818890/CountrySnapshot';
  this.submit();

  var secondForm = this.cloneNode(true);
  console.log(secondForm);
  secondForm.action = "https://public.tableau.com/views/Beta2020_7_EN_CS_p2of4_V1_NewRep_Org/CountrySnapshot";
  secondForm.method = "get";
  secondForm.elements["Parameters.Country"].value = this.elements["Parameters.Country"].value;
  secondForm.elements["GoC"].value = this.elements["GoC"].value;
  secondForm.target = "second_iframe";
  hiddenDiv.appendChild(secondForm);
  secondForm.submit();
  }
```

### Step 6: 
Finally, we need to set-up a default view when the page first loads and before any filters are applied. The code to choose the default page settings and iFrame sizes is below. 

```java
/* Set the starting display. */
/* The default filter is set to display the country Haiti.*/
document.getElementById('mainform').elements["Parameters.Country"].value = "Haiti";  
document.getElementById('mainsubmit').click();

};
</script>

/* Set the iFrame dimensions for the webpage. This should match the dimensions of the Tableau Dashboard.*/ 
<iframe name="first_iframe" width="1100" height="1250" frameborder="0"></iframe> 
<iframe name="second_iframe" width="1100" height="750" frameborder="0"></iframe></div>
      
      </div>
```

Hopefully these instructions are helpful for anyone who wants to upload multiple tableau dashboards to a webpage! This project was completed for The Canadian Partnership for Women and Children's Health by Blackcat Informatics® Inc. and was written by Patrick Audley with support by Fallyn Thompson. 