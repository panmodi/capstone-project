# capstone-project

Use [OpenML](https://www.openml.org/search?type=data&status=active&id=43438&sort=runs) which has books data derived from GoodReads site. We can use this to predict if a book can be successful from the metadata of books like author, publisher, genre and series info. Also some books doesn't have genres, use description of the book to predict the genres. 

Jupyter notebook: [Capstone_project-PanktiModi.ipynb](https://github.com/panmodi/capstone_project/blob/main/Capstone_project-PanktiModi.ipynb)

Data used: [OpenML](https://www.openml.org/search?type=data&status=active&id=43438&sort=runs)

### Load and clean up data

* Uploaded the data from [OpenML](https://www.openml.org/search?type=data&status=active&id=43438&sort=runs) with fetch_openml
* Stored them locally as books_dataset.csv
* Checked the missing data and dropped unwanted columns which are not needed for now
#### Clean up data details
* Checked if 'author' can be filled with 'author_link'. Link is not working most of the times. The number is farely low(6) so dropped them
* Checked 'title' as some values are already space, filling NaN with space. Quite a lot of 'title' has more than 1 space as values. Processed that and made all of those as single space
* Processed 'author' also same way by making more than 1 space as single space
* Created books['is_series'] column from books['series'] 
* Created books['no_of_other_books_in_series'] from processing books['books_in_series'] 
* books['award_count'] from books['awards']
* books['year_published'] from processing books['date_published']
* books['is_high_rated'] from books['average_rating'] which can be used as 'y' to predict
* books['primary_genre'] from processing and using first genre from books['genre_and_votes'] and ignoring votes number. Filled NaN with 'Other'
* books['publisher'] filled NaN with 'Other'
Additional clean up
* Take out secondary_genre too. Split like books['primary_genre']. Fill all NaN with 'Other'
* Ignoring some authors like ",", "Other Author","NOT A BOOK" etc. Checked manually, 'NOT A BOOK' is usually some articles
* Genre cleaning by combining some genre in to one. Targeting top 20 genres. Unique Genres reduced to 388 from original 466. Now books which are NOT part of top 20 genres are reduced from ~27% to ~18%


### Visualizations of data
Added code for visualisation of the data with various charts
* Average Ratings
* Top 15 Authors with the Most Books. Data is cleaned by ignoring space and comma as author. 'NOT A BOOK' is usually some articles in goodreads
* Top 10 authors star distibution. We can see 'J K Rowling' is here twice for 'Harry Potter' series for diffent editions with it's atrist.
* Popular books vs niche books. Book can be considered popular if rating_count > 10k
* Top 10 frequent Publishers. Ignoring other as it comes as top publisher as in clean up we replaced all spaces and NaN with 'Other'
* Distribution of Books by Rating and Awards Won
* Rating Trend Over Time (Since 1980). Used the column which we added 'year_published'
##### Additional Visualizations
* Top 20 Most Popular Genres
* Top 20 genre coverage. This is visulization of data which comparision I have done manually in previous cells
* Series vs. Standalone Books by Genre
  
### Baseline Model
Used few of the columns which has books metadata like 'author','publisher','primary_genre','is_series','no_of_other_books_in_series','year_published' as X and 'is_high_rated' will be used as y

```
Accuracy: 0.7083918763571337
[[5033 2270]
 [2296 6059]]
```

* This model's accuracy is around 70.8%. 
* I tried all the commented columns dataset from previous cell of code with same model above and always getting results ~70%. Meaning with provided columns the model has converged and now stable. We need to use some other columns and models to come up with better results.
* The current model can be used by marketing/sales team to predict and target which books to market more on and can have higher sales

### Logistic Regression, KNN algorithm, Decision Tree, RidgeClassifier and SVM
```
                     Train Time  Train Accuracy  Test Accuracy
Model                                                         
Logistic Regression    0.564514        0.862488       0.708392
KNN                    0.064912        0.994717       0.690637
Decision Tree          1.731062        0.994745       0.671925
Ridge                  0.126910        0.757821       0.688338
SVM                   40.505464        0.796852       0.702005
```
* Logistic Regression is still performing best
* Accuracy Gap in the Ridge is the least. Model will likely perform exactly the same on totally new data.

### Logistic Regression, KNN algorithm, Decision Tree, RidgeClassifier and SVM model with hyperparameters
```
[[5033 2270]
 [2296 6059]]
[[4832 2471]
 [2345 6010]]
[[4741 2562]
 [2891 5464]]
[[4941 2362]
 [2291 6064]]
[[5017 2286]
 [2341 6014]]
                                             Best Params  Average Train Time  Train Accuracy  Test Accuracy
Model                                                                                                      
logisticregression          {'logisticregression__C': 1}            1.505547        0.862488       0.708392
knn                             {'knn__n_neighbors': 11}           15.798916        0.994745       0.692426
decisiontree             {'decisiontree__max_depth': 15}            0.261292        0.683673       0.651744
ridge                              {'ridge__alpha': 1.0}            0.375346        0.925907       0.702836
svc                 {'svc__C': 10, 'svc__kernel': 'rbf'}           41.033309        0.969262       0.704496
```
* Now logisticregression, ridge and svc have 70% accuracy
* svc takes a long time to come
* Ridge model takes the least amount of time

### Deep Neural Network
```
NN Accuracy: 0.6814
[[5148 2155]
 [2833 5522]]
```
Deep Neural Network is not helping much here as accuracy is reduced. 

### Secondary genre with 30% weightage
Adding 'secondary_genre' also as one the feature but only with 30% weightage. Suggestion of using weightage for 'secondary_genre' column was suggested by Jessica in our 1 on 1 session. 
```
New Accuracy with Weighted Secondary Genre: 0.7119
[[5085 2218]
 [2293 6062]]
```
We see minor improvement. We can try out various % weightage like 40%, 50% for 'secondary_genre' column. We can also try grid_search by passing few options. 
### Using 'genre_cleaned'
Use books['genre_cleaned'] instead of books['primary_genre'] and see if that helps in higher accuracy.
```
Accuracy: 0.7075616298377826
[[5024 2279]
 [2300 6055]]
```
With genre_cleaned we get accuracy of 70.73. Not much change from original usage of primary_genre. With above all combinations,accuracy stays between 68% to 70% which is very similar to realistic world where 7 out of 10 books' success can be predicated by few features added above. We also see that adding secondary genre with 30% weightage had also some impact(not much though)

### Natural Language Processing (NLP)
Predict the genre of the book where we have filled NaN with 'Other'. Use books['description']. Try with all the 'primary_genre' and then try with top 20 'primary_genre'. Compare the diffrence in performance. Create another column books['final_genre'] and fill the new genre if confidence score is higher than 0.50(50%)
* Intially tried to predict all the genres from 'primary_genre'. code is taking long time to process as we have a lot of variation of primary_genre. We can narrow down that but focusing on top 20 genres
* Narrowed down to books with top 20 Genres (excluding 'Other'). Tried few options with CountVectorizer and TfidfVectorizer.
* Code will get the probabilities for all 20 genres for each 'Other' book. Find the index of the highest probability for each book and find the value of that highest probability (the confidence score)
* I verified some of these manually from my dataset. Original dataset has link to the goodreads book. From the book I checked either in google or from title I could see that predictions on top 15 which are dispalyed here seems correct.
* Created books['final_genre'] with predicted value when predictions with > 50% confidence
* Some stats for how many we could fillout successfully
```
--- Confidence Analysis for 'Other' Genres ---
Total 'Other' books to predict:      2837
Predicted with > 50% confidence:   707
Percentage of 'Other' successfully filled: 24.92%
Average confidence score:           0.38
```


### Future work
