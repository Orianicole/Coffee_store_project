# Coffee Store Project ☕️

This is a new project I have been working on. The data has been taken from 3 different databases but the idea is to work on it as if it was all part of a single project.

> Although the databases correspond to different projects, they have been adapted to give congruence to the cafeteria project.

🎯 The idea comes from a simulation where a data analyst starts working in a coffee shop. The project is divided into 3 sections: the first one where we have the recipes of each drink and the prices, this helps us to calculate the value of each drink, which is the most profitable and the least. The second is the analysis of sales in its two branches along with a dashboard that illustrates those results. And the last part is an analysis of the reviews of each store.

**The tools I chose to use in this project are: Rstudio, Power Bi, and the most fun in my opinion, my logic and research skills.*

# Part 1: Price analysis 

    ## Insert the database, i’ll be working with this database:
  [https://www.kaggle.com/datasets/agungpambudi/trends-product-coffee-shop-sales-revenue-dataset](https://www.kaggle.com/datasets/agungpambudi/trends-product-coffee-shop-sales-revenue-dataset)
    
   

    ## Import libraries:
    library(tidyverse)
    library(ggplot2)

    ## From the price table I am going to remove all the columns, 
    leaving only price_small which will be joined with recipes, 
    I choose to work with the small size in principle since the database 
    tells us that customers can pay 0.70 to upgrade the size.
    
    prices <- subset(prices, select = -c (price_medium, price_large, simple, double))

    ## The table recipes contains the names of the column in the first 
    row, so let's put as a name

    columnname <- recipes[1, ]
    recipes <- recipes[-1, ]
    colnames(recipes) <- columnname
    
    ## With a left join I will join the two tables so that I have 
    the recipes table ready to work with.
    
    recipes <- left_join(prices, recipes, by = "drink_type")
    
    ## Now I am going to rename all the columns, in the original table
     they are expressed in centiliters and I am going to work with 
     milliliters. Besides, the column names are long and are not expressed 
     in an easy way.

    recipes <- recipes %>% rename(milk_ml = `milk (in cl)`)
    recipes <- recipes %>% rename(ice_cubes = `ice cubes`
    recipes <- recipes %>% rename(coffee_dose_ristretto = `coffee dose (in number of ristrettos`)
    recipes <- recipes %>% rename(coffee_dose_ristretto = `coffee dose (in number of ristrettos)`)
    And the same for all the columns
    
    ## Since I am only going to work with small size I am going to delete 
    the rest, I know that there are some coffees that are measured by 
    coffee size and not by drink size.
    
    recipes <- subset(recipes, drink_size == 'small' | coffee_size == 'simple')
    
    ## The next step is to convert all the columns to numerical since I 
    will need to multiply them by 100 to obtain the value in milliliters
    
    numericcolumns <- c(“milk_ml”, "aroma_pumps_10ml", "fruit_extract_10ml", 
    "chocolate_pumps_30ml",  "powder_5ml", "powder_15ml", "cold_brew_ml",
    "coco_milk_ml", "black_tea_ml",  "apple_juice_ml", "orange_juice_ml", 
    "speculoos_ml", "filter_coffee_ml",)
     
    recipes[numericcolumns] <- lapply(recipes[numericcolumns], as.numeric)
    
    recipes[numericcolumns] <- lapply(recipes[numericcolumns], function(x) as.numeric(x) * 10)

Here is where I searched a well-known shopping website for each product. Find the most appropriate prices and calculate the price of each ingredient by converting from ounces to milliliters and dividing the total price by milliliters. 

> The prices found are an estimate, I am warned that having a business, the prices are different since buying more quantity is cheaper.

![Ingredients table](https://github.com/Orianicole/Coffee_store_project/blob/99a942c039e232a3a154beda04c0299e231ca963/Charts/ingredients_price.png)

    ## With these prices I will create the cost table and multiply each 
    value by the cost of each ingredient.
    
    cost <- recipes

    cost$milk_ml <-  cost$milk_ml * 0.0012
    cost$cold_brew_ml[cost$cold_brew_ml == '120'] <- 0.42 
    cost$tea_bags <- cost$tea_bags * 0.04
    cost$black_tea_ml[cost$black_tea_ml == '250'] <- 0.6
    cost$honey_grams <- cost$honey_grams * 0.11
    
    ## Then I'm going to add up all the ingredients for each drink 
    to calculate the net price and to that I'm going to add about 2 dollars 
    which includes an approximate cost of electricity, barista's salary
    and other things that the drink needs
    
    cost$total <- rowSums(cost[, sapply(cost, is.numeric) &
    colnames(cost) != "price_small"], na.rm = TRUE)
    
    cost$total <- cost$total + 2

    ## The next step is to calculate the difference between the net price 
    and the price of the beverage, and then plot it on a graph to make it 
    easier to read.

    cost$difference <- cost$price_small - cost$total

    ggplot(cost, aes(x = reorder(drink_type, -difference), y = difference)) 
    + geom_point(stat = "identity", color= "skyblue", size= 3) 
    + geom_text(aes(label = difference), vjust = -1.2, color = "black", 
    + size = 1.8) + labs(title = "Profitability of Beverages",
    x = "Beverages", y = "Difference") 
    + theme(axis.text.x = element_text(angle = 90, hjust = 1, size = 6))

![Profitability ](https://github.com/Orianicole/Coffee_store_project/blob/99a942c039e232a3a154beda04c0299e231ca963/Charts/profitability.png)


# Part 2: Sales analysis
Dataset from: [https://www.kaggle.com/datasets/deryae0/coffee-shop-chain-recipes-and-prices](https://www.kaggle.com/datasets/deryae0/coffee-shop-chain-recipes-and-prices)

To have the table ready to analyze what I did was:
Filter to only 2 locations, create a new column with the size of the cafe and delete the columns that do not serve us.
I divided the data into two tables: in one we have all the sales with time, day, branch and what product was sold, and another where we have how many products were sold and the unit price, if the table would correspond to the data set of part 1, this is where I would also have a column with the net price of each drink.
Also create a calendar table and a time table to be able to analyze days and hours.
Finally create a dashboard that compares the sales by location. 

![Dashboard_1](https://github.com/Orianicole/Coffee_store_project/blob/99a942c039e232a3a154beda04c0299e231ca963/Charts/dashaboard.jpeg) 

![Dashboard_by_month](https://github.com/Orianicole/Coffee_store_project/blob/99a942c039e232a3a154beda04c0299e231ca963/Charts/dashboard_bymonth.jpeg)


# Part 3: Reviews analysis


    ## Insert the database, i’ll be working with this database:
  [https://www.kaggle.com/datasets/kanchana1990/starbucks-nyc-1000-reviews](https://www.kaggle.com/datasets/kanchana1990/starbucks-nyc-1000-reviews)

    ## Installing packages
    library(tidyverse)
    library(textcat)
    library(cld2)
     
    ## Opening files
    Hells <-read_csv("/Users/oriananicolerasello/Desktop/starbucks_ny_broadway.csv")
    Lower <- read_csv("/Users/oriananicolerasello/Desktop/starbucks_ny_reserve_roastery.csv")
     
    # I’m going to transform the database, i want to merge it in only one table but first I need to create a new column to know which store it belongs
    
    store <- sample(c("Hells Kitchen"), size = nrow(Hells), replace = TRUE)
    sbbroad <- cbind(Hells, store)
    store <- sample(c("Lower Manhattan"), size = nrow(Lower), replace = TRUE)
    sbroast<- cbind(Lower, store) 
    coffeedb <- rbind(Hells, Lower)

    ## We don’t really need to clean this database, so lets analyze

    coffeedb %>%  dplyr::filter(store == "Hells Kitchen") %>% summary()
    coffeedb %>%  dplyr::filter(store == "Lower") %>% summary()
    
    meanhells <-  Hells %>% count(rating) %>% mutate(percentage = n / sum(n) * 100
	meanlower <-  Lower %>% count(rating) %>% mutate(percentage = n / sum(n) * 100)
	 
	## We create charts
	
    ggplot(data = meanlower, aes(x=rating, y=percentage, fill= rating)) +
    geom_col() + geom_text(aes(label = paste0(sprintf("%.2f", percentage), "%"), 
    y = percentage), vjust = -0.5) + labs(x='Rating on Stars', 
    y='Percentage', title = 'Percentage of Rating in Roastery Store', 
    caption = 'Image by Oriana Rasello') +
    scale_fill_gradient(low = "red", high = "green") +
    theme(legend.position = "none")
    
    result <- coffeedb %>% + mutate(year = year(publishedDate)) %>%
    + group_by(year, rating)
    
    ggplot(result, aes(x = rating, fill = store)) +
    geom_bar(position = "dodge") + facet_wrap(~year) +
    labs(x = "Rating", y = "Total", fill = "Store", title = "Rating by year and Store")

![Hells_Reviews](https://github.com/Orianicole/Coffee_store_project/blob/99a942c039e232a3a154beda04c0299e231ca963/Charts/hells_review.jpeg)

![Lower_Reviews](https://github.com/Orianicole/Coffee_store_project/blob/99a942c039e232a3a154beda04c0299e231ca963/Charts/lower_review.jpeg)

![Rating_by_year_and_store](https://github.com/Orianicole/Coffee_store_project/blob/99a942c039e232a3a154beda04c0299e231ca963/Charts/ranking_by_year.jpeg)

    ## Finally we are going to use the text cat library to see the 
    different languages of the reviews.

> In this part I would use an API to convert all the other languages into English, this is an example of what I would do, in this case I am only going to use the English reviews.

 

     coffeedb <- coffeedb %>%  mutate(language = textcat(text))
     coffeedb %>% filter(language != 'english') %>% count(language) %>%
     summarise(total = sum(n))
    
    # A tibble: 1 × 1
     total
    <int>
     654
    
    coffeedb %>% filter(language == 'english') %>% count(language) %>%
    summarise(total = sum(n))
    
    # A tibble: 1 × 1
    total
    <int>
    414

    ## I would like to make a wordcloud to see which are the most repeated 
    words in the reviews, for that I am going to filter the words that have 
    at least 4 letters and I am going to count their frequency.

    words <- strsplit(text "\\s+")[[1]]
    filter_words<- words[nchar(words) >= 4]
    frequency <- table(filter_words)
    df_frequency <- as.data.frame(frequency)
    colnames(df_frequency) <- c("Word", "Freq")
    wordcloud2::wordcloud2(df_frequency)

The result you would get would be something like this 

![Word_cloud](https://github.com/Orianicole/Coffee_store_project/blob/2ab51e8a8485d91981671f8c0b6f2503e6458cd8/Charts/wordcloud.png)


