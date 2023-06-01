# Evaluation of Food Establishments by Ratings Data in a JSON File

# Scope
This exercise has 3 parts:
Part 1: Database and Jupyter Notebook Set Up
Part 2: Update the Database
Part 3: Exploratory Analysis

# Tools 
MongoDB, Pandas, Python, PrettyPrint

# Background  

The UK Food Standards Agency evaluates various establishments across the United Kingdom, and gives them a food hygiene rating. You've been contracted by the editors of a food magazine, Eat Safe, Love, to evaluate some of the ratings data in order to help their journalists and food critics decide where to focus future articles

# Part 1: Database and Jupyter Notebook Set Up
### Notebook name: NoSQL_setup_uk_food.ipynb
1. Begin using NoSQL_setup_starter.ipynb.  Rename the file to NoSQL_setup_uk_food.ipynb.
2. Import the data provided in the establishments.json file from your Terminal. 
Example text to run in the terminal:  
### mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json
3. Name the database 
### uk_food
4. Name the collection 
### establishments
5. Import the libraries: PyMongo and Pretty Print (pprint).
6. Create an instance of the Mongo Client.
7. Confirm that you created the database and loaded the data properly:
8. List the databases you have in MongoDB. Confirm that uk_food is listed.
9. List the collection(s) in the database to ensure that establishments is there.
10. Find and display one document in the establishments collection using find_one and display with pprint.
11. Assign the establishments collection to a variable to prepare the collection for use.

# Part 2: Update the Database
### Notebook name: NoSQL_setup_uk_food.ipynb
Begin using NoSQL_setup_starter.ipynb.  Rename the file to NoSQL_setup_uk_food.ipynb.

### Background
The magazine editors have some requested modifications for the database before you can perform any queries or analysis for them. Make the following changes to the establishments collection.  An exciting new halal restaurant just opened in Greenwich, but hasn't been rated yet. The magazine has asked you to include it in your analysis. 
1. Add the following information to the database:
![image](https://github.com/CMccormick0003/nosql-challenge/assets/120672518/37e0940f-ba42-4841-b26c-6749df126a17)

2. Find the BusinessTypeID for "Restaurant/Cafe/Canteen" and return only the BusinessTypeID and BusinessType fields.
BusinessType returns as 'Restaurant/Cafe/Canteen' and 'BusinessTypeID' returns as 1.

3. Update the new restaurant with the BusinessTypeID you found.

### Background:  The magazine is not interested in any establishments in Dover.
4. Check how many documents contain the Dover Local Authority. 
5. Remove any establishments within the Dover Local Authority from the database.
6. Check the number of documents to ensure they were deleted.

There were no establihments found in Dover, therefore none were removed from the database.

### Background:  Some of the number values are stored as strings, when they should be stored as numbers.
7. Use update_many to convert latitude and longitude to decimal numbers.
establishments.update_many({},[{"$set":{"geocode.longitude":{'$toDouble': '$geocode.longitude'},
                                        "geocode.latitude":{"$toDouble": "$geocode.latitude"} 
                                       }}])

8. Use update_many to convert RatingValue to integer numbers.
First set any ratings that were not 1 to 5 as null:
non_ratings = ["AwaitingInspection", "Awaiting Inspection", "AwaitingPublication", "Pass", "Exempt"]
establishments.update_many({"RatingValue": {"$in": non_ratings}}, [ {'$set':{ "RatingValue" : None}} ])

Then change the data type from String to Integer:
ratings = establishments.find({"RatingValue": {"$type": "string"}})
for doc in ratings:
    rating_value = int(doc["RatingValue"])
    establishments.update_one({"_id": doc["_id"]}, {"$set": {"RatingValue": rating_value}})
    
# Part 3: Exploratory Analysis
### Notebook name: NoSQL_analysis_uk_food.ipynb
Begin using NoSQL_analysis_starter.ipynb.  Rename the file to NoSQL_analysis_uk_food.ipynb.

### Background:
Eat Safe, Love has specific questions they want you to answer, which will help them find the locations they wish to visit and avoid.  Explore the database to find the answers to the following questions, so you can provide them to the magazine editors.

### RatingValue: 
RatingValue refers to the overall rating decided by the Food Authority and ranges from 1-5. The higher the value, the better the rating.
Note: This field also includes non-numeric values such as 'Pass', where 'Pass' means that the establishment passed their inspection but isn't given a number rating. We will coerce non-numeric values to nulls during the database setup before converting ratings to integers.
### Hygiene, Structural, and ConfidenceInManagement: 
Score for Hygiene, Structural and ConfidenceManagement work in reverse; higher value means the worse the establishment is in these areas.

For each question:
- Use count_documents to display the number of documents contained in the result.
- Display the first document in the results using pprint.
- Convert the result to a Pandas DataFrame, print the number of rows in the DataFrame, and display the first 10 rows.

### Which establishments have a hygiene score equal to 20?
Number of establishments with hygiene score of 20:  41
A snipet of the dataframe of the 41 establishments is shown below:
![image](https://github.com/CMccormick0003/nosql-challenge/assets/120672518/09537b75-bd29-4170-a26a-f1f96d4d873d)

### Which establishments in London have a RatingValue greater than or equal to 4?
Note, the London Local Authority has a longer name than "London" so use $regex as part of the search.
Number of establishments in London with rating >= 4:   33
A snipet of the details of one of the establishments with rating >= 4 is shown below:
![image](https://github.com/CMccormick0003/nosql-challenge/assets/120672518/5b0c041a-0b81-4944-96f9-7e4a5effb533)

### What are the top 5 establishments with a RatingValue of 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"?
Note, compare the geocode to find the nearest locations. Search within 0.01 degree on either side of the latitude and longitude of Penang Flavours.
![image](https://github.com/CMccormick0003/nosql-challenge/assets/120672518/823c293c-2bb0-4326-8a46-3881a7d13991)

### How many establishments in each Local Authority area have a hygiene score of 0? Sort the results from highest to lowest, and print out the top ten local authority areas?
Note, use the aggregation method to analyze the data for this question.
THere are 55 Local Authorities with at least one establishment with a hygiene score of 0.  The Local Authroities with the most establishments with hygiene scores of 0 are shown below.
![image](https://github.com/CMccormick0003/nosql-challenge/assets/120672518/1a231c8c-aaaf-4007-bd4c-0c03dccf107f)

