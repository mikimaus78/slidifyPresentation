---
title       : Baseball Photo Auction Sales Predictor
subtitle    : using Linear Model
author      : Haris Berbic
job         : Teacher
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## Introduction

For the Data Science Specialization, Course Developing Data Project we have task to do some predictive application.
I choose to build Linear model to predict Sale Price for Photo Auction on Ebay for specific player. 
User may select one of the feature from player and minimum bids build  Linear model.
The result is prediction price of player photos on auction.

--- .class #id 

## Data summary
Application needs 3 cvs files:

1. players.cvs with fields:
PersonPriceID, PersonID, CategoryName, ListedCount, AvgPrice, MaxPrice, MinPrice, SaleCount, GradedCount, GradedSaleCount, GradedAvgPrice, AuctionCount, AuctionSaleCount, AuctionAvgPrice, AuctionMedianPrice, FirstName, LastName, PlayerName, DeceasedYear, ItemAuctionSellPercent

2. sellers.cvs with fields:
SellerID, SellerName, ListingCount, SaleCount, AuctionCount, AuctionSaleCount, SellerSaleAvgPriceRatio, SellerAvg, SellerItemAvg, SellerClosePercent

3. Photos.cvs with fields:
Price, StartingBidPercent, StartingBid, AvgPrice, QuantitySold, ItemAuctionSellPercent, Authenticated, SellerSaleAvgPriceRatio, IsHOF, AuctionCount, SellerAuctionSaleCount, PriceBuckets, AuctionMedianPrice

4.LogisticTrainingSet.csv with fieds:
QuantitySold, Price, StartingBidPercent, SellerName, SellerClosePercent, Category, PersonID, StartingBid, AvgPrice, ItemAuctionSellPercent, AuctionSaleCount, AuctionMedianPrice

--- .class #id 

## Loading and displaying player data to end user

We use standard read.csv function to load data into memory

```r
players.data <- read.csv(file='players.csv', sep=',', h=T)
```
We subset data based on player name and only for Photos

```r
player.data<-subset(players.data, PlayerName == input$player & CategoryName == 'Photos')
```
We display player data 

```r
output.data <- player.data [c("PlayerName","AuctionCount", "AuctionSaleCount","AuctionAvgPrice","AuctionMedianPrice","MaxPrice","MinPrice")]
```

--- .class #id 
## Loading data for calculation

First we load data in order to make calculation

```r
players.data <- read.csv(file='players.csv', sep=',', h=T)
player.data<-subset(players.data, PlayerName == input$player & CategoryName == 'Photos')
sellers.data <- read.csv(file='sellers.csv', sep=',', h=T)
seller.data<-subset(sellers.data, SellerName == 'probstein123')
```

--- .class #id 

## Building test frame

```r
test.data <- data.frame(AuctionMedianPrice = numeric(N),
                            AvgPrice = numeric(N),
                            ItemAuctionSellPercent = numeric(N),
                            StartingBidPercent = numeric(N),
                            StartingBid = numeric(N),
                            Authenticated = numeric(N),
                            SellerSaleAvgPriceRatio = numeric(N),
                            IsHOF = numeric(N),
                            AuctionCount = numeric(N),
                            SellerAuctionSaleCount = numeric(N))

test.data[1, ] <- c(AuctionMedianPrice, AvgPrice, ItemAuctionSellPercent, 
                    StartingBidPercent, StartingBid, Authenticated, 
                    SellerSaleAvgPriceRatio, IsHOF, AuctionCount, 
                    SellerAuctionSaleCount)
```

--- .class #id 
## Loading train data

Then we load train data

```r
data.file.name <- 'Photos.csv' 
train.raw.data  <- read.csv(file=data.file.name, sep=',', h=T)
```
And optimize set of features

```r
train.data <- train.raw.data[c("AuctionMedianPrice", "Price", "AvgPrice", 
            "ItemAuctionSellPercent", "StartingBidPercent", "StartingBid", 
            "Authenticated", "SellerSaleAvgPriceRatio", "IsHOF", 
            "AuctionCount", "SellerAuctionSaleCount")]
train.data$Price=log(train.data$Price)
```

--- .class #id 
## Prediction

We crate model using Recursive Partitioning and Regression Trees

```r
tree<-rpart(Price ~.,data=train.data)
predCart<-predict(tree, test.data, type = "vector")
prediction = exp(predCart)
```
and display it to the end user


```r
paste("Predicted Sale Price : $", toString(round(prediction,digits=2)))
```

--- .class #id 
## Thanks for watching!

