Module \# 9 Visualization in R
================

For this assignment, I have chosen to use my own data obtained from my job as a restaurant delivery driver. The dataset contains a total of observations and the variables I chose to record are:

Date | Address | Order Amount | Tip Amount | Age | Time | Tip Included | Dog Owner

``` r
df = read.csv("C:/Users/adamw/Documents/Data Textmining/Delivery Data/Delivery Data - Sheet1 (1).csv")
```

For the first visualization I plotted all of my orders on a map and filled them in by tip percentage of total order.

``` r
df$Address = as.character(df$Address)
register_google(key = "AIzaSyDLHeQB3O4nhAMtQJLRVKpGohvk5FPB3dg")
df$latlon = geocode(df$Address)
df$TipPercentage = df$TipAmt/df$OrderAmt
df$Gender = as.factor(df$Gender)

dfdog = subset(df, Dog == "Yes")
dfnodog = subset(df, Dog == "No")

dfmale = subset(df, Gender == "Male")
dffemale = subset(df, Gender == "Female")
```

``` r
tampa.map <- get_map(c(-82.49261, 27.91484), zoom = 12, maptype = "terrain")

mapSimple <- ggmap(tampa.map)

df = subset(df, df$TipPercentage <= 0.35)

mapSimple + geom_point(data = df, aes(x = latlon$lon, y = latlon$lat, 
    color = TipPercentage, ), size = 3, alpha = 6/10) + scale_color_gradient(low = "blue", 
    high = "red", limits = c(0, 0.35)) + ggtitle("Tampa Delivery Map")
```

![](Module---9-Visualization-in-R_files/figure-markdown_github/unnamed-chunk-3-1.png)

The second visualization is a scatterplot of the same variables, however this one is colored by gender.

``` r
ggplot(df, aes(df$OrderAmt, df$TipPercentage, color = df$Gender)) + 
    geom_jitter() + xlab("Order Amount") + ylab("Tip Percentage") + 
    labs(color = "Gender") + ggtitle("Percentage of Tip Based on Total Order") + 
    geom_smooth(method = "lm")
```

![](Module---9-Visualization-in-R_files/figure-markdown_github/unnamed-chunk-4-1.png)

The last visualization plots the number of orders placed throughout the evening.

``` r
f = subset(df, df$TipPercentage < 0.35)
df$Time = as.POSIXct(df$Time, format = "%H:%M:%S")

dfdog = subset(df, Dog == "Yes")
dfnodog = subset(df, Dog == "No")
dfdog$Time = as.POSIXct(dfdog$Time, format = "%H:%M:%S")
dfnodog$Time = as.POSIXct(dfnodog$Time, format = "%H:%M:%S")

dfmale$Time = as.POSIXct(dfmale$Time, format = "%H:%M:%S")
dffemale$Time = as.POSIXct(dffemale$Time, format = "%H:%M:%S")

# df$Time = as.Date(df$Time) df$Time =
# format(as.POSIXct(df$Time,format='%m/%d/%Y
# %H:%M:%S'),format='%H:%M')

ggplot(df, aes(x = Time, fill = Gender)) + geom_bar() + theme(axis.text.x = element_text(size = 12, 
    angle = 80)) + scale_x_datetime(date_breaks = "30 min", date_labels = "%H:%M") + 
    ylab("Number of Orders") + ggtitle("Number of Orders Based on Time of Day") + 
    xlab("Time (PM)")
```

![](Module---9-Visualization-in-R_files/figure-markdown_github/unnamed-chunk-5-1.png)
