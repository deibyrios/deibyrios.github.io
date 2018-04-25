---
pagetitle: Analysis of Data Quality
output:
      html_document:
        keep_md: true
---


## Analysis of Data Quality
  
  
### Remodeling of existing and construction of new buildings   
  
As mentioned in the Description of Data, the dataset we were able to get, shows the details for every building permit filed with the Department of Buildings, from 1990 to date. This dataset is a list of building permits filed for a particular day and associated data and contains building permits for both new buildings and alterations / remodeling.

After downloading the original dataset (1.4GB, 3,341,659 observations),read it, take a look at it and inspected variables/column names:


```r
permits <- read.csv("data/DOB_Permit_Issuance.csv")
#head(permits)
colnames(permits) #print variables / column names
```

```
##  [1] "BOROUGH"                          "Bin.."                           
##  [3] "House.."                          "Street.Name"                     
##  [5] "Job.."                            "Job.doc..."                      
##  [7] "Job.Type"                         "Self_Cert"                       
##  [9] "Block"                            "Lot"                             
## [11] "Community.Board"                  "Zip.Code"                        
## [13] "Bldg.Type"                        "Residential"                     
## [15] "Special.District.1"               "Special.District.2"              
## [17] "Work.Type"                        "Permit.Status"                   
## [19] "Filing.Status"                    "Permit.Type"                     
## [21] "Permit.Sequence.."                "Permit.Subtype"                  
## [23] "Oil.Gas"                          "Site.Fill"                       
## [25] "Filing.Date"                      "Issuance.Date"                   
## [27] "Expiration.Date"                  "Job.Start.Date"                  
## [29] "Permittee.s.First.Name"           "Permittee.s.Last.Name"           
## [31] "Permittee.s.Business.Name"        "Permittee.s.Phone.."             
## [33] "Permittee.s.License.Type"         "Permittee.s.License.."           
## [35] "Act.as.Superintendent"            "Permittee.s.Other.Title"         
## [37] "HIC.License"                      "Site.Safety.Mgr.s.First.Name"    
## [39] "Site.Safety.Mgr.s.Last.Name"      "Site.Safety.Mgr.Business.Name"   
## [41] "Superintendent.First...Last.Name" "Superintendent.Business.Name"    
## [43] "Owner.s.Business.Type"            "Non.Profit"                      
## [45] "Owner.s.Business.Name"            "Owner.s.First.Name"              
## [47] "Owner.s.Last.Name"                "Owner.s.House.."                 
## [49] "Owner.s.House.Street.Name"        "Owner�..s.House.City"            
## [51] "Owner�..s.House.State"            "Owner�..s.House.Zip.Code"        
## [53] "Owner.s.Phone.."                  "DOBRunDate"                      
## [55] "PERMIT_SI_NO"                     "LATITUDE"                        
## [57] "LONGITUDE"                        "COUNCIL_DISTRICT"                
## [59] "CENSUS_TRACT"                     "NTA_NAME"
```
<br>

We were able to find a Data Dictionary containing field codes and descriptions of the data sets, which can be downloaded [here](https://data.cityofnewyork.us/api/views/ipu4-2q9a/files/87608d5b-1986-4a21-a8a5-7a38e65f9993?download=true&filename=DD_DOB_Permit_Issuance_2018-03-07.xlsx "Data Dictionary for building permits").

This is a brief description of the variables of our interest:

* BOROUGH: The name of the NYC borough
* House: The house number for the building
* Street Name: The street name (combined with house number completes the address)
* Zip Code:	ZIP Code for the building's address
* Job Type:	Type of job to be performed.
* Block: Tax Block number of the location (assigned by by the Department of Finance)
* Lot: Tax Lot number of the location (assigned by by the Department of Finance)
* Bldg Type: Legal occupancy classification
* Work Type: The specific type of work covered by the permit.
* Filing Date: The date the permit application was filed with DOB.
* Owner's Business Type: The type of entity that owns the building.

Now we proceed to filter Manhattan Zip codes that are within the area of influence of phase1 and phase 2 of the 2nd Ave Line project (10065, 10021, 10075, 10028, 10128, 10029, 10035) and selected the columns of our interest:


```r
#filter Manahattan zip codes of interest:
library(tidyverse)
permits_upper_east <- filter(permits, BOROUGH=="MANHATTAN" & Zip.Code %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035))

#Select relevant columns of our interest:
permits_upper_east <- select(permits_upper_east, House..,Street.Name,Job.Type,Block,Lot,Zip.Code,
                    Bldg.Type,Work.Type,Filing.Date,Owner.s.Business.Type)
#head(permits_upper_east)
write.table(permits_upper_east, file="DOB_Permit_Issuance_filtered.csv",sep=",",row.names=F)
```

After filtering and selecting variables of our interest, now we look for missing data using extracat::visna():


```r
#install.packages('extracat')
library(extracat)
visna(permits_upper_east)
```

<img src="basemaps_files/figure-html/unnamed-chunk-3-1.png" width="100%" />

As we can see in the graph above, the quality of the data is very good, with just very few rows missing the Building Type column.

The variables "Block" and "Lot" are of our special interest as those were the main variables used four our main Exploratory Data Analysis as we describe later.
    
       
### Real State Sales

_Provide a detailed, well-organized description of data quality, including textual description, graphs, and code_
    
     
         
### Property Market Values

As mentioned in the Description of Data, we were able to find the property market values from 2018 back to 2008 with 12 datasets in total, **one dataset for each year** except 2008 that contains 2 different datasets the needed to be combined.

We were able to find a Data Dictionary containing field codes and descriptions of the data sets, which can be accessed [here](http://www1.nyc.gov/assets/finance/downloads/tar/tarfieldcodes.pdf "Data Dictionary for property market values").

After downloading and extracting the zip files for all the original datasets we read each dataset, take a look at it, read the corresponding Data Dictionary and inspect variables/column names. The data consists of Access databases so we used RODBC library to read the databases using "odbcConnectAccess('my_Access_Database_to_read.mdb')".

**Note:** The original 12 Access databases have a total size of 4.34Gb, take too long time to be read and can't be uploaded to GitHub, so we included the following commented code just **to document our work**, but we uploaded the samller data after filetering and selecting our variables of interest. 


```r
# library(tidyverse)
# #install.packages('RODBC')
# library(RODBC)
# 
# mdbConnect<-odbcConnectAccess("avroll_19.mdb")
# property <- sqlFetch(mdbConnect,"avroll")
# odbcClose(mdbConnect)
# #head(property)
# #colnames(property) #print variables / column names
# 
# property_upper_east <- property %>% 
#   filter(B==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2018)
# str(property_upper_east) 
# 
# mdbConnect<-odbcConnectAccess("avroll_18.mdb")
# new_property <- sqlFetch(mdbConnect,"avroll")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>% 
#   filter(B==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2017)
# str(new_property_upper_east) 
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# #str(property_upper_east)
# #write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
# 
# mdbConnect<-odbcConnectAccess("avroll_17.mdb")
# new_property <- sqlFetch(mdbConnect,"avroll")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>% 
#   filter(B==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2016)
# str(new_property_upper_east) 
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# #str(property_upper_east)
# #write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
# 
# mdbConnect<-odbcConnectAccess("avroll_16.mdb")
# new_property <- sqlFetch(mdbConnect,"avroll")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>% 
#   filter(B==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2015)
# str(new_property_upper_east) 
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# #str(property_upper_east)
# #write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
# 
# mdbConnect<-odbcConnectAccess("avroll_15.mdb")
# new_property <- sqlFetch(mdbConnect,"avroll")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>% 
#   filter(B==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2014)
# str(new_property_upper_east) 
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# #str(property_upper_east)
# #write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
# 
# mdbConnect<-odbcConnectAccess("avroll_14.mdb")
# new_property <- sqlFetch(mdbConnect,"avroll")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>% 
#   filter(B==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2013)
# str(new_property_upper_east) 
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# #str(property_upper_east)
# #write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
# 
# mdbConnect<-odbcConnectAccess("avroll_13.mdb")
# new_property <- sqlFetch(mdbConnect,"avroll")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>% 
#   filter(B==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2012)
# str(new_property_upper_east) 
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# #str(property_upper_east)
# #write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
# 
# mdbConnect<-odbcConnectAccess("avroll_12.mdb")
# new_property <- sqlFetch(mdbConnect,"avroll")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>% 
#   filter(B==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2011)
# str(new_property_upper_east) 
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# #str(property_upper_east)
# #write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
# 
# mdbConnect<-odbcConnectAccess("avroll_11.mdb")
# new_property <- sqlFetch(mdbConnect,"avroll")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>% 
#   filter(B==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2010)
# str(new_property_upper_east) 
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# #str(property_upper_east)
# #write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
# 
# mdbConnect<-odbcConnectAccess("avroll_10.mdb")
# new_property <- sqlFetch(mdbConnect,"avroll")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>% 
#   filter(B==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2009)
# str(new_property_upper_east) 
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# #str(property_upper_east)
# #write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
# 
# mdbConnect<-odbcConnectAccess("avroll_09_class 1.mdb")
# new_property <- sqlFetch(mdbConnect,"tc1")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>%
#   mutate(FULLVAL = NEW_FV_T) %>%
#   filter(BORO==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2008)
# str(new_property_upper_east)
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# #str(property_upper_east)
# #write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
# 
# mdbConnect<-odbcConnectAccess("avroll_09_class 2,3,4.mdb")
# new_property <- sqlFetch(mdbConnect,"tc234")
# odbcClose(mdbConnect)
# #head(new_property)
# #colnames(new_property) #print variables / column names
# 
# new_property_upper_east <- new_property %>%
#   mutate(FULLVAL = NEW_FV_T) %>%
#   filter(BORO==1 & ZIP %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>% select(BLOCK,LOT,BLDGCL,FULLVAL) %>%
#     mutate(Year = 2008)
# str(new_property_upper_east)
# 
# property_upper_east <- rbind.data.frame(new_property_upper_east, property_upper_east)
# str(property_upper_east)
# #Next, we write a new file 'property_upper_east.csv' in the working directory, for future use whenever we need to read the data again, because reading the original files takes too long time:
# write.table(property_upper_east, file="property_upper_east.csv",sep=",",row.names=F)
```

This is a brief description of the variables of our interest utilized in the code above:

* BORO: The name of the NYC borough
* ZIP:	ZIP Code for the property's address
* BLOCK: Tax Block number of the location (assigned by by the Department of Finance)
* LOT: Tax Lot number of the location (assigned by by the Department of Finance)
* BLDGCL: 2 digits building class code (see codes [here](http://www1.nyc.gov/assets/finance/jump/hlpbldgcode.html "Building class Codes"))
* FULLVAL/NEW_FV_T: Total next market value estimated for next fiscal year  

After filtering zip codes in the area of influence of phase1 and phase 2 of the 2nd Ave Line project (10065, 10021, 10075, 10028, 10128, 10029, 10035) and selecting variables of our interest, we looked for missing data using extracat::visna(), but we got 'Error in visna(property_upper_east) : No NA's in the data', so we used mi::missing_data.frame() instead, just to double check:


```r
#library(tidyverse)
property_upper_east <- read.csv("data/property_upper_east.csv") #already filtered and selected data
#install.packages('extracat')
library(mi)
show(missing_data.frame(property_upper_east))
```

```
## Object of class missing_data.frame with 299333 observations on 5 variables
## 
## There are 1 missing data patterns
## 
## Append '@patterns' to this missing_data.frame to access the corresponding pattern for every observation or perhaps use table()
## 
##                          type missing method model
## BLOCK              continuous       0   <NA>  <NA>
## LOT                continuous       0   <NA>  <NA>
## BLDGCL  unordered-categorical       0   <NA>  <NA>
## FULLVAL            continuous       0   <NA>  <NA>
## Year               continuous       0   <NA>  <NA>
## 
##         family link transformation
## BLOCK     <NA> <NA>    standardize
## LOT       <NA> <NA>    standardize
## BLDGCL    <NA> <NA>           <NA>
## FULLVAL   <NA> <NA>    standardize
## Year      <NA> <NA>    standardize
```

No we confirmed that there are no NA's in the data so the quality of the data is very good.

Again, the variables "Block" and "Lot" are of our special interest as those were the main variables used four our main Exploratory Data Analysis as we describe later.
    
     
### Property Assessed Values

As mentioned in the Description of Data, we were able to find property "Assessed Values" from 2003 to 2017 in the "Primary Land Use Tax Lot Output" (PLUTO) dataset, developed by the New York City Department of City Planning's (DCP). 

We were able to find a Data Dictionary containing field codes and descriptions of the data sets, which can be accessed [here](http://www1.nyc.gov/assets/planning/download/pdf/data-maps/open-data/pluto_datadictionary.pdf "Data Dictionary for property assessed values").

After downloading and extracting the zip files for the original datasets we read each dataset, take a look at it, read the corresponding Data Dictionary and inspect variables/column names. The datasets are shapefiles so we used the "foreign" library to read the data files of the shapefiles using "read.dbf('my_shapefile_to_read.dbf')".

**Note:** The original 15 datasets are an extensive land use and geographic data at the tax lot level, containing more than seventy fields derived from data maintained by different city agencies so they have a large size, take too long time to be read and can't be uploaded to GitHub, so we included the following commented code just **to document our work**, but we uploaded the samller data after filetering and selecting our variables of interest.


```r
# library(tidyverse)
# library(foreign) #used to read .dbf files
# 
# PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/mn_mappluto_17v1_1/MNMapPLUTO.dbf")
# #head(PLUTO)
# 
# PLUTO <- PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2017)
# str(PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_16/MNMapPLUTO.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2016)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_15/MNMapPLUTO.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2015)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_14/MNMapPLUTO.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2014)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_13/MNMapPLUTO.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2013)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_12/MNMapPLUTO.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2012)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_11/MNMapPLUTO.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2011)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_10/MNMapPLUTO.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2010)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_09/MNMapPLUTO.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2009)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_08/MNMapPLUTO.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2008)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_07/mnmappluto.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2007)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_06/mnmappluto.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(Block,Lot,BldgClass,AssessTot,XCoord,YCoord) %>%
#   mutate(Year = 2006)
# str(New_PLUTO)
# 
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_05/mnmappluto.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZIPCODE %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(BLOCK,LOT,BLDGCLASS,ASSESSTOT,XCOORD,YCOORD) %>%
#   mutate(Year = 2005)
# str(New_PLUTO)
# 
# colnames(New_PLUTO) <- c("Block","Lot","BldgClass","AssessTot","XCoord","YCoord","Year")
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_04/mnmappluto.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(ZIPCODE %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(BLOCK,LOT,BLDGCLASS,ASSESSTOTA,XCOORD,YCOORD) %>%
#   mutate(Year = 2004)
# str(New_PLUTO)
# 
# colnames(New_PLUTO) <- c("Block","Lot","BldgClass","AssessTot","XCoord","YCoord","Year")
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# 
# New_PLUTO <- read.dbf("c:/Users/yenny/Downloads/Columbia/Exploratory Data/final project/Market Values 2009-2019/Mappluto/Manhattan_03/mnmappluto.dbf")
# #head(New_PLUTO)
# 
# New_PLUTO  <- New_PLUTO %>%
#   filter(zipCode %in% c(10065, 10021, 10075, 10028, 10128, 10029, 10035)) %>%
#   select(block,lot,bldgClass,assessTotl,xCoord,yCoord) %>%
#   mutate(Year = 2003)
# str(New_PLUTO)
# 
# colnames(New_PLUTO) <- c("Block","Lot","BldgClass","AssessTot","XCoord","YCoord","Year")
# PLUTO <- rbind.data.frame(New_PLUTO, PLUTO)
# str(PLUTO)
# 
# #Next, we write a new file 'property_upper_east.csv' in the working directory, for future use whenever we need to read the data again, because reading the original files takes too long time:
# write.table(PLUTO, file="PLUTO_upper_east.csv",sep=",",row.names=F)
```

This is a brief description of the variables of our interest utilized in the code above:

* ZipCode: ZIP Code for the property's address
* Block: Tax Block number of the location (assigned by by the Department of Finance)
* Lot: Tax Lot number of the location (assigned by by the Department of Finance)
* BldgClass: 2 digits building class code (see codes [here](http://www1.nyc.gov/assets/finance/jump/hlpbldgcode.html "Building class Codes"))
* AssessTot: Total Property Assessed Value  

After filtering zip codes in the area of influence of phase1 and phase 2 of the 2nd Ave Line project (10065, 10021, 10075, 10028, 10128, 10029, 10035) and selecting variables of our interest, we looked for missing data using extracat::visna(), but we got 'Error in visna(property_upper_east) : No NA's in the data', so we used mi::missing_data.frame() instead, just to double check:


```r
#library(tidyverse)
property_upper_east <- read.csv("data/PLUTO_upper_east.csv") #already filtered and selected data
#install.packages('extracat')
library(mi)
show(missing_data.frame(property_upper_east))
```

```
## Object of class missing_data.frame with 130111 observations on 7 variables
## 
## There are 1 missing data patterns
## 
## Append '@patterns' to this missing_data.frame to access the corresponding pattern for every observation or perhaps use table()
## 
##                            type missing method model
## Block                continuous       0   <NA>  <NA>
## Lot                  continuous       0   <NA>  <NA>
## BldgClass unordered-categorical       0   <NA>  <NA>
## AssessTot            continuous       0   <NA>  <NA>
## XCoord               continuous       0   <NA>  <NA>
## YCoord               continuous       0   <NA>  <NA>
## Year                 continuous       0   <NA>  <NA>
## 
##           family link transformation
## Block       <NA> <NA>    standardize
## Lot         <NA> <NA>    standardize
## BldgClass   <NA> <NA>           <NA>
## AssessTot   <NA> <NA>    standardize
## XCoord      <NA> <NA>    standardize
## YCoord      <NA> <NA>    standardize
## Year        <NA> <NA>    standardize
```

No we confirmed that there are no NA's in the data so the quality of the data is very good.

Same than for the analysis for building permits and the analysis for property market values, the variables "Block" and "Lot" are of our special interest as those were the main variables used four our main Exploratory Data Analysis as we describe later.
          
      
### Other Datasets?

_Provide a detailed, well-organized description of data quality, including textual description, graphs, and code_