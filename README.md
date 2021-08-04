# Fame-Is-Becoming-a-Star-Written-in-the-Stars

I sometimes joke that as an Aries I don’t believe in zodiac signs. But could there still be some pattern, e.g. in the sense that people born in spring are more prone to success than those born during the winter months?

In this post, we will provide a definitive answer with one of the most fascinating datasets I have ever encountered, so read on!

The data we will be using is from the extraordinary Pantheon project:

    Pantheon is an observatory of collective memory focused on biographies with a presence in at least 15 languages in Wikipedia. We have data on more than 85,000 biographies, organized by countries, cities, occupations, and eras. Explore this data to learn about the characters that shape human culture. Pantheon began as a project at the Collective Learning group at MIT.

To test whether zodiac signs have any bearing on success we will do the following three steps:

    Load the Pantheon project data of famous people, subset all living persons born in the US and calculate their zodiac signs.
    Load the distribution of zodiac signs of all US citizens.
    Test whether there is a statistically significant difference between both distributions.

The latest Pantheon data can be loaded from here: Pantheon datasets. It is a bzip compressed comma delimited file, which can without any intermediate steps directly loaded into R:

pantheon <- read.csv("data/person_2020_update.csv.bz2", encoding = "UTF-8")
data <- pantheon[pantheon$bplace_country == "United States" & pantheon$alive == TRUE, ]
nrow(data)
## [1] 10106

head(data$name, 25)
##  [1] "Donald Trump"       "Jimmy Carter"       "Sylvester Stallone"
##  [4] "Hillary Clinton"    "Steven Spielberg"   "Martin Scorsese"   
##  [7] "Clint Eastwood"     "Al Pacino"          "Bill Gates"        
## [10] "Cher"               "Robert De Niro"     "Morgan Freeman"    
## [13] "Warren Buffett"     "Jack Nicholson"     "Al Gore"           
## [16] "Noam Chomsky"       "Danny DeVito"       "Woody Allen"       
## [19] "Stephen King"       "Joe Biden"          "Dustin Hoffman"    
## [22] "Bob Dylan"          "Tina Turner"        "Meryl Streep"      
## [25] "Bill Clinton"

We see that we get more than 10,000 famous persons from the US, where I listed the first 25.

Now for the data on the distribution of zodiac signs for the US population as a whole. You can download the file from here: distribution_zodiac_US (source).

distr_zodiac_US <- read.csv("data/distribution_zodiac_US.csv") # change path accordingly
distr_zodiac_US <- structure(distr_zodiac_US$Percent.of.US.Population, names = distr_zodiac_US$Zodiac.Sign)

After having all the data available we will calculate the zodiac signs with the DescTools package (on CRAN) and create a table with both distributions:

library(DescTools)
birthdates <- substr(data$birthdate[nchar(data$birthdate) > 0], 6, 10)
zodiac <- table(Zodiac(as.Date(birthdates, "%m-%d")))
zodiacs <- rbind(distr_zodiac_US[names(zodiac)], prop.table(zodiac))
row.names(zodiacs) <- c("whole US", "celeb US")
zodiacs <- 100*zodiacs
round(zodiacs, 2)
##          Capricorn Aquarius Pisces Aries Taurus Gemini Cancer  Leo Virgo Libra
## celeb US      8.20     6.30   9.00  8.10   8.30    9.2   8.40 7.10  9.30  8.70
## whole US      7.54     7.56   8.75  8.72   7.74    8.7   9.01 8.97  8.49  8.63
##          Scorpio Sagittarius
## celeb US    9.40        7.30
## whole US    8.03        7.87

We see that there are differences, e.g. there are more celebrities with the zodiac sign Leo than in the general population, but are those differences statistically significant (to understand the concept of statistical significance please see: From Coin Tosses to p-Hacking: Make Statistics Significant Again!)?

To evaluate we use perform a chi-squared goodness-of-fit test:

chisq.test(x = zodiacs["celeb US", ], p = zodiacs["whole US", ]/100)
## 
##  Chi-squared test for given probabilities
## 
## data:  zodiacs["celeb US", ]
## X-squared = 1.1756, df = 11, p-value = 0.9999

The result is crystal clear: both distributions are not statistically different (the p-value is way above the significance level of 0.05), or put another way, your zodiac sign says nothing about your success in life! The numbers don’t lie (and the stars are silent).

If you have more ideas on how to use this fantastic dataset, please let me know
