# capstone-project

Use [OpenML](https://www.openml.org/search?type=data&status=active&id=43438&sort=runs) which has books data derived from GoodReads site. We can use this to predict if a book can be successful from the metadata of books like author, publisher, genre and series info

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

### Visualizations of data
Added code for visualisation of the data with various charts
* Average Ratings
* Top 15 Authors with the Most Books. Data is cleaned by ignoring space and comma as author. 'NOT A BOOK' is usually some articles in goodreads
* Top 10 authors star distibution. We can see 'J K Rowling' is here twice for 'Harry Potter' series for diffent editions with it's atrist.
* Popular books vs niche books. Book can be considered popular if rating_count > 10k
* Top 10 frequent Publishers. Ignoring other as it comes as top publisher as in clean up we replaced all spaces and NaN with 'Other'
* Distribution of Books by Rating and Awards Won
* Rating Trend Over Time (Since 1980). Used the column which we added 'year_published'

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


### Future work

#### Clean up data
* Make all space ‘title’, ‘author’ as ‘Other’
* Clean up Genre more by combining some e.g. make Romance-Romantic Suspense, Romance-M M Romance as Romance
* See if 'description' can be used to fillout 'primary_genre' column where we filled NaN with 'Other' 

