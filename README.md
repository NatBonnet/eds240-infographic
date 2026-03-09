# EDS 240: Data Visualization and Communication
## Final Infographic: Light attraction behavior in moth populations historically exposed to light pollution versus in dark sky habitats

## Author
Nathalie Bonnet

## Instructors
Sam Shanny-Csik

Annie Adams

## About
This repository houses the work to create a final data visualization in EDS 240. The goal of this workflow is to create a final infographic that communicates the findings of a study on the impact of urban light pollution on moth behavior. 

## Repository structure
```
├───data
│ 
├───drafting-viz_files
│   ├───figure-html
│   └───libs
│       
├───exploration_files
│   ├───figure-html
│   └───libs
│       
├───figures
├───icons
├───images
└───rosm.cache
    
```
`data` is contained by the `.gitignore` file, and contains the `.txt` data downloaded from Dryad.

`drafting-viz_files` contains html outputs from the rendering of `drafting-viz.qmd` contained in the root directory. Similarly, `exploration_files` contain html outputs from `exploration.qmd`. 

`figures` contains the PNG and PDF outputs from plots used in the info graphic. 

`icons` contain any SVG files to be used in the final info graphic.

`rosm.cache` was used to create the map with all its dependencies. 


## Data access
The data used to create final figures were accessed publicly via Dryad: https://datadryad.org/dataset/doi:10.5061/dryad.v1885#citations

To get the full information used in the study, the following code was used to associate coordinate points and light pollution statuses:
```
light_pol <- read.delim(here("data", "light_pol.txt"), skip = 2) %>%
  clean_names() %>%
  # Separate location column
  separate_wider_delim(
    cols = location_country,
    delim = "/",
    names = c("loc_name", "country")
  ) %>%
  # Remove any white space from separated columns
  mutate(country = str_squish(country), loc_name = str_squish(loc_name)) %>%
  # Rename population column to be more relevant
  rename(pollution_status = population) %>%
  # Recode values to be more intuitive
  mutate(
    pollution_status = recode(
      pollution_status,
      "dark-sky" = "non polluted",
      "light-polluted" = "polluted"
    ),
    # Recode country codes to full names
    country = recode(country, "F" = "France", "CH" = "Switzerland"),
    # Fix location name with conversion error
    loc_name = recode(loc_name, "Kleinl�tzel" = "Kleinlützel")
  ) %>%
  # Make a new column with pollution bin values
  mutate(
    pollution_bin = case_when(
      loc_name == "Blochmont"       ~ "0.25–0.4",
      loc_name == "Kleinlützel"     ~ "0.4-1",
      loc_name == "Kiffis"          ~ "0.4-1",
      loc_name == "Doucier"         ~ "0.4-1",
      loc_name == "Lutterbach"      ~ "1-3",
      loc_name == "Allschwil"       ~ "20-40",
      loc_name == "Reinach"         ~ "20-40",
      loc_name == "Hegenheim"       ~ ">40",
      loc_name == "Basel Kannenfeld" ~ ">40",
      loc_name == "Basel Spalentor" ~ ">40"
    ),
    # New Latitude and Longitude column with values from paper
    lat = case_when(
      loc_name == "Blochmont"        ~ 47 + 26 / 60 + 16 / 3600,
      loc_name == "Kleinlützel"      ~ 47 + 25 / 60 + 55 / 3600,
      loc_name == "Kiffis"           ~ 47 + 26 / 60 + 18 / 3600,
      loc_name == "Doucier"          ~ 46 + 39 / 60 + 40 / 3600,
      loc_name == "Lutterbach"       ~ 47 + 46 / 60 + 12 / 3600,
      loc_name == "Allschwil"        ~ 47 + 32 / 60 + 52 / 3600,
      loc_name == "Reinach"          ~ 47 + 31 / 60 + 50 / 3600,
      loc_name == "Hegenheim"        ~ 47 + 33 / 60 + 43 / 3600,
      loc_name == "Basel Kannenfeld" ~ 47 + 33 / 60 + 59 / 3600,
      loc_name == "Basel Spalentor"  ~ 47 + 33 / 60 + 28 / 3600
    ),
    lon = case_when(
      loc_name == "Blochmont"        ~  7 + 14 / 60 + 10 / 3600,
      loc_name == "Kleinlützel"      ~  7 + 22 / 60 + 57 / 3600,
      loc_name == "Kiffis"           ~  7 + 17 / 60 + 59 / 3600,
      loc_name == "Doucier"          ~  5 + 41 / 60 + 21 / 3600,
      loc_name == "Lutterbach"       ~  7 +  3 / 60 + 36 / 3600,
      loc_name == "Allschwil"        ~  7 + 32 / 60 +  8 / 3600,
      loc_name == "Reinach"          ~  7 + 36 / 60 + 25 / 3600,
      loc_name == "Hegenheim"        ~  7 + 31 / 60 + 14 / 3600,
      loc_name == "Basel Kannenfeld" ~  7 + 34 / 60 + 19 / 3600,
      loc_name == "Basel Spalentor"  ~  7 + 34 / 60 + 53 / 3600)) %>% 
  mutate(pollution_bin = factor(pollution_bin, levels = c("0.25–0.4", 
                                                           "0.4-1", 
                                                           "1-3", 
                                                           "20-40", 
                                                           ">40")
                                 )
         )
```

## References

Altermatt, Florian, and Dieter Ebert. 2016. “Reduced Flight-to-Light Behaviour of Moth Populations Exposed to Long-Term Urban Light Pollution.” Biology Letters 12 (4): 20160111. https://doi.org/10.1098/rsbl.2016.0111.
Borges, R. M., H. Somanathan, and A. Kelber. 2016. “Patterns and Processes in Nocturnal and Crepuscular Pollination Services.” The Quarterly Review of Biology 91 (4): 389–418. https://doi.org/10.1086/689481.
Macgregor, C. J., and A. S. Scott-Brown. 2020. “Nocturnal Pollination: An Overlooked Ecosystem Service Vulnerable to Environmental Change.” Emerging Topics in Life Sciences 4 (1): 19–32. https://doi.org/10.1042/ETLS20190134.
NASA. 2020. “NASA Worldview.” https://worldview.earthdata.nasa.gov/.