---
timeToRead: 7
authors: []
title: Building an Amazon review Tracker with Replit and Selenium
excerpt: ''
date: 
hero: ''
draft: true

---
# Using Replit, Selenium and SMTP to Create an Amazon Review Tracker

# Introduction

In this blog I am going to explain how I created an Amazon review Tracking tool for my business use, using selenium, replit and SMTP. I hope this blog would be useful for those who wants to use either replit, smtp or selenium on their projects. You can see the code here -

[https://github.com/akshaypt7/amazon_review_tracker](https://github.com/akshaypt7/amazon_review_tracker "https://github.com/akshaypt7/amazon_review_tracker")

### Context

For a Brand, customer feedback is super important and once you start growing you will have a few hundred products and a few thousand reviews to track. To track all these details weekly is quite dificult. But doing this will help us to respond to customer feedback more promptly and take actions before an issues goes out of hand. For most brands in platforms like amazon, a proper understanding of customer reviews on thier products is very important.

### **Constraints in creating this tool**

Few of the constraints I had before creating this was my limited web-development knowledge and the tracker should be able to be used by my colleagues with zero programming knowledge. This led me to Replit, it seemed like I could use replit features like database, terminal app and I could host it online and my colleagues could run if from thier own device without any prior experience.

**At the end we had a tool,**

* which takes in the Product IDs that we give it.
* Tracks all the positive and negative reviews it received till that date for each product.
* Stores the product ratings in a database for the dates we have used the tool.
* Showcases the positive and negative reviews we received for each product on all the dates we have run the tool on. (This will help us to take action)
* Emails the product reviews data as a csv file.
* A terminal app that can be used to run this tool from any device by any member of the team.

T**he final csv file will look like this:**

![Change the quantity of reviews to make it more dramatic.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/34faa759-f4e0-43aa-9bb0-a249d83e89e2/Screenshot_2022-03-10_at_4.47.33_PM.png)

Change the quantity of reviews to make it more dramatic.

On each date we can see how many total positive ratings the product recieved. So by comparing with ratings from other dates, we can see the overall customers sentiment for the product over the period. (same is done for the negative reviews)

## Important Libraries used

* Selenium - Selenium is a powerful library, which runs the browser like google chrome without any user supervision. We can for example use it to book tickets, scrape website data etc. We can provide it with instructions and it does exactly what we tell it to do.
* Replit - Replit is the backbone of this project. The most important thing it helps us do is to run the programme from any device and to store the product review data in its database. Also we used replit’s terminal app, as the user interface for our colleagues to use.
* SMTP - This library helps us to send emails with csv attachments (product reviews) to the team members.

> **_By the end of the blog you will get an idea on how to use replit, selenium and smtp in your projects._**

### We **have broken down the problem into smaller parts:**

1. User Input
2. Browsing Amazon product pages and calculating the ratings.
3. Storing the data in a database to refer at a later date.
4. Checking if the Tracker has ran today.
5. Displaying the product reviews as a table .
6. Deleting products from the database
7. Creating a csv file of the positive and negative reviews for each product.
8. Sending the csv files to an email-id.

First, we have to figure out how to run the brower-automation tool, selenium in replit. Also we want selenium to run in the background, we don't necessarily want others to see the browser searching each products in amazon.

The below is the code for that:

    chrome_options = Options()
    chrome_options.add_argument('--no-sandbox')
    chrome_options.add_argument('--disable-dev-shm-usage')
    
    # running selenium in background
    chrome_options.add_argument("--headless")
    

<aside> 💡 **Replit Database** We need an database to store all the product ratings of each product for the previous dates in which the tool has run. So we could compare the ratings we recived recently with the past. This was created in replit by import replit’s own database.

from replit import db

</aside>

### 1. User Input

When building this product and getting feedback from my teammates I understood that there are lot of scenarios that I have not taken into consideration earlier.

As naive as it is, I thought the first version I made would be sufficient for my teammates. It went through all the products that we had, calculated the positive and negative reviews and displayed it in the terminal. Once I showed it to my team, they had lot of suggestions, such as the option to display the product for which the reviews are being calcualted and the data to be shared in a csv format, and also having an option to add & delete products from the database. These are just few of the suggestions and it made me understand how important it is to get feedback as soon as possible and how much more fun it is to have the stakeholders included in the process of budiling the tool.

We used the function **user_input** for this part of the problem, here we would ask the user if she wants to add any new asins to track. The function will let you know if there is any typos or if the product the user added is already being tracked.

As we start the programme, the terminal will print out all the Products(asin) the programme is currently tracking.

The inputs from the users will be amazon product ID known as ASIN, these inputs will be stored in the database with their ratings (more details on the database will be shared below)

<aside> 💡 I think most of the user input related errors can be solved by using while loops and if statements :)

</aside>

### 2. Browsing Amazon Product page

I used selenium library to browse the product pages and scrape the ratings from the website. The entire code for this section can be found in the fucntion **_browser._**

After using one of the versions, one of my colleagues suggested that they would also need the product title next to the product ID’s since it would be easier for them to identify the product. Along with it I made few other changes like combining the 1 star, 2star and 3 star ratings as total negative reviews and 5 star as the total postive reviews.

These product ratings, product title and ID are then saved into replit database.

I also figured that its better we give the selenium browser a sleep time in between product searches in amazon, helps not to bring in unwanted attention.

### 3. Storing the ratings in a database

So for storing the above data that we scraped we had few options. We needed a database which stores the data on all the dates we ran the tracker.

For example, you can see, we first ran the tracker on 2 March 2022, and the last one was on 9 March 2022. This would help us see how the customer responses are for each product over the period of time and help us to take any action if there is any sudden spike (like in negative reviews)

![Change the quantity of reviews to make it more dramatic.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/34faa759-f4e0-43aa-9bb0-a249d83e89e2/Screenshot_2022-03-10_at_4.47.33_PM.png)

Change the quantity of reviews to make it more dramatic.

In databases, I had a bit of experience in working with sqlite, also my developer friend suggested some of his favourite databases like airtable, PostgreSQL etc.. but all of these seemed too complex for this simple usecase and finally I ended using replit database.

It was so simple and intuitive to use, its moreover like a dictionary in python.

### 4. Checking if the Tracker has ran today.

Again, this was something that was suggested by one of my early users, since we would be adding many more products to track, it would take some time for the programme to calculate all the ratings. It would make sense not to run the programme for browsing the product pages, if the tracker was already used that day. Because there wont be any drastic changes in ratings within a day.

Also if by any case if we need to add new products to track, the programme will ask the user if he needs run the tracker again today.

This is done by using the function **_today_tracked_**

### 5. Displaying the product reviews as a table

When I had few columns and products, I used pandas dataframe to display the products and its ratings. But as soon as more columns(dates we have run the tracker) and more products came in, this way of displaying had issyes. But this was an easy problem to solve I ended up using tabulate module, to create a table out of this data.

You can find the code for this in the function **_create_table_**

### 6. Deleting products from the database

This function was needed because there would be times where the user would have entered a different product or if the product is no longer sold. Here we ask the product id(asin) from the user and it gets deleted. The table is then displayed to the user.

### 7. Creating a csv file of the postive and negative reviews for each product

Here we create two csv files from our databse. The details include Product ID, Product Title and ratings at each of the dates the tracker was used. This was done using pandas.

We use the function **_create_csv()_** for this.

### 8. Sending the csv files to an email-id

For the tool to send emails with attachments I found the best simple option to use the library smtplib. Learning more about smtp, helped me to have a better understanding on how emails are send and received over the internet.

Here I wanted to use an environment variable, this helps me to hide my email-id and its password from others, which is used to send emails to the users.

Again, replit came to the rescue. Replit had a very simple way for us to use environments variables in our project

> 

### Bringing all this together

I dont how I could have pulled this off without the help of Replit, as some one who has very less experience in programming, all other options seemed too complicated. But using replit was very intuitive and I ended up creating this tool in a few days.

Replit has helped to create this tool to be used by anyone from our team and from anywhere. We could store the database without any complex solutions and ended up using replit’s terminal app as the user interface.