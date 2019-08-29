# Chorepleth_Map
NYC opiod Deaths




library(urbnmapr)
library(tidyverse)

head(counties)
View(counties)

dim(counties)


#The Data


opiod_deaths<- read_csv("Opiod_Death_NYS_2016.csv")


head(opiod_deaths)

## We dont know need to import the County data manually, that is included in the urbnmapr package

#We need to give the columns that contain the county names in outer
# 2 data sets identical names so that we can merge on them

## rename(new variable name = existing variable name)
opiod_deaths<- opiod_deaths%>% rename(county = Place)
head(opiod_deaths)


## Replace NA with 0
## data[is.na(data)] <- 0
opiod_deaths[is.na(opiod_deaths)]<- 0

## Convert ot all lowercase
opiod_deaths$county<- tolower(opiod_deaths$county)
counties$county <- tolower(counties$county)


## Extract NYS from the couties data
NewYork<- filter(counties, state_name=="New York")

merged_opiod_data<- opiod_deaths%>%
                    left_join(NewYork, by = "county")

head(merged_opiod_data)

# Map the data
 
merged_opiod_data%>%
  ggplot(mapping = aes(long, lat, group = group, fill = rate_per_100k)) +
  geom_polygon(color = "#ffffff", size = .25) +
  coord_map(projection = "albers", lat0 = 74, lat1 = 43) +
  theme(legend.title = element_text(),
        legend.key.width = unit(.5, "in")) +
  labs(fill = "Mortality")  

## But actually we are interested only in the 5 boroughs 

boroughs<- c("bronx", "new york", "kings","queens","richmond")

five_boros<- filter(merged_opiod_data, county %in% c(boroughs))

View(five_boros)



## Now we will map just the NYC data
five_boros%>%
  ggplot(mapping = aes(long, lat, group = group, fill = rate_per_100k)) +
  geom_polygon(color = "#ffffff", size = 1) +
  coord_map(projection = "albers", lat0 = 74, lat1 = 43) +
  theme(legend.title = element_text(),
        legend.key.width = unit(.5, "in")) +
  labs(fill = "Number Per 100k") 

###########


## now we will map just the NYC data
five_boros%>%
  ggplot(mapping = aes(long, lat, group = group, fill = rate_per_100k)) +

  scale_colour_gradient(low = "white", high = "black")+
  geom_polygon( color = "#ffffff", size = 1) +
  coord_map(projection = "albers", lat0 = 74, lat1 = 43) +
  theme(legend.title = element_text(),
        legend.key.width = unit(.5, "in")) +
  labs(fill = "Number Per 100k") 
###
## Now we will map just the NYC data
five_boros%>%
  ggplot(mapping = aes(long, lat, group = group, fill = rate_per_100k)) +
  geom_polygon( color = "black", size = 1) +
  scale_fill_gradient(low='white', high='grey20')+
  coord_map(projection = "albers", lat0 = 74, lat1 = 43) +
  theme(legend.title = element_text(),
        legend.key.width = unit(.5, "in")) +
  labs(fill = "Deaths Per 100k") + 
  ggtitle("Opiod Deaths" )+
  theme_void()

