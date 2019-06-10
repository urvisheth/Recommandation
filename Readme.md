#  Content-based recommendation system 

In this a small content-based recommendation system is developed. The MovieLens dataset (http://movielens.org, a movie recommendation service) is curated by a research group at the University of Minnesota (https://grouplens.org/datasets/movielens/). I have used the small dataset that consists of 100004 ratings and 1296 tag applications across 9125 movies. These data were created by 671 users between January 09, 1995 and October 16, 2016. This dataset was generated on October 17, 2016. This dataset is available to program (see ml-latest-small.zip file). For this problem, program will focus on two files in the dataset: ratings.csv and movies.csv. The file ratings.csv contains ratings of 671 users, each user is identified by a unique user ID. The file
movies.csv contains movies, titles and genres, each movie is identified by a unique movie ID.

Here, a movie recommender engine has been developed that will create a profile of a user and then match 10 randomly selected movies to the user’s profile; the top 5 movies will be suggested to the user as possible movies to watch.

20430232 mod 671 is used for the user id for the profile. 

See below how to build the user profile.

After constructing the user’s profile, I will randomly choose 10 movies from the movies.csv file. As an example, let’s say I randomly chose the following 10 movie IDs: 1967, 7319, 4602, 1550, 3355, 91535, 4297, 4169, 606, 1361. Then, the movie with movie ID 1967 in the movies.csv file is “Labyrinth (1986)”; the movie with movie ID 7319 is “Club Dread (2004)”; the movie with movie ID 4602 is “Harlem Nights (1989)”, and so on. I have created a profile for each movie. See below how to build the movie profiles. Once I has constructed the user’s profile, and 10 movie profiles, program will use the cosine similarity metric to get the similarity between the user and each of the movies. program will then output the top 5 movies with the highest similarity that match the user’s profile. R has a package called lsa from which program can get the cosine similarity function. 

## Building a user profile

To build a user’s profile, program will examine all of the movies that the user has watched. (Be careful, one user has actually watched 2,391 movies and if program happen to randomly pick that user then make sure program test The code on a user who has watched less number of movies. In fact, it is a good idea while developing the user’s profile that program do it on a user ID that has watched the least amount of movies. The least amount of movies watched is 20, and user ID 1 is a good example of such a user.)

The profile will be constructed in 20 dimensions. Each genre of the movies that the user has watched becomes an attribute. program will go through all of the movies that a user has watched and extract the genres of the movies. The genre is a “|”-separated list. In R, program can use the strsplit() function to tokenize a string using a delimiter (“|”) into its constituent tokens. Read up on the strsplit() function for more information.

These are the 20 genres that are present in the movies.csv file:

	"Action", "Adventure", "Animation", "Children", "Comedy", "Crime", "Documentary", "Drama", "Fantasy",
	"Film-Noir", "Horror", "IMAX", "Musical", "Mystery", "Romance", "Sci-Fi", "Thriller", "War", "Western", "(no
	genres listed)".

To build a user profile, program will create a data frame with 20 columns and as many rows as the number of movies that the user has watched. Each movie is decomposed into its constituent genres and the appropriate cells in the dataframe corresponding to the genre are set to ‘1’. For example, the spreadsheet below shows the decomposition of the 20 movies that user ID 1 has watched into its constituent genres (the first column is the movie ID).

To create a user profile, program will sum up the 1’s in each column (or attribute) and divide them by the number of movies watched by that user. 

## Building a movie profile

To build a movie profile, program will again extract all of the genres that the movie can be decomposed into and the column corresponding to the genre of the movie gets set to a ‘1’ and all other columns get set to ‘0’. For example, movie ID number 145 is decomposed into the following genres: “Action”, “Comedy”, “Crime”. “Drama”, “Thriller”. The movie vector corresponding to this movie will be:
<1, 0, 0, 0, 1, 1, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0>

In the vector above, the first element corresponds to genre “Action”, the second “Adventure”, and so on. 

Once program have built the user profile and movie profiles, program are now ready to run the cosine similarity and present the results. Again, as an example, the cosine similarity of the user profile vector and the movie profile vector shown above is: 0.666

The output should be as follows:

	User ID X chose the following 10 movies: 18, 47, 255, 269, 471, 445, 640, 680, 1589, 1562
	Of these, the following 5 movies are recommended:

			MovieId 	MovieName 	Similarity
			1562 		Title 1 	0.671
			445 		Title 2 	0.584
			680 		Title 3 	0.221
			471 		Title 4 	0.195
			255 		Title 3 	0.108

#  Collaborative Filtering 

In this assignment, program will develop a small collaborative filtering-based recommendation system. We will use the same dataset that was used in Problem Above. The task is to develop recommendation engine that will predict the rating that a user will give to a movie.

	To do this, we will work with user ID 191. User ID 191 has watched (and rated) the following 29 movies:
		10 34 47 110 150 153 161 165 185 208 231 292 296 300 318 339 344 349 356 380 434 454 457 480 588 590 592 593 595

There are 36 users who match user ID 191; the following 12 users with high similarity scores will be considered for this problem:
			

						User IDs 		Jaccard Similarity
						User ID 513 		0.4358974
						User ID 317 		0.4033613
						User ID 415 		0.3255814
						User ID 375 		0.3049645
						User ID 64 		0.2753623
						User ID 556 		0.2727273
						User ID 82 		0.2527473
						User ID 225 		0.2420382
						User ID 657 		0.2262774
						User ID 266 		0.2216216
						User ID 568 		0.2105263
						User ID 50 		0.2009804


Because we are interested in measuring the performance of our recommender engine’s prediction, we will treat 4 movie IDs from User ID 191 as test observations. These four movie IDs are: 150, 296, 380, and 590. Write down the recommendation that user ID 191 provides to these movies separately and set the ratings of these movies to NA for User ID 191. We will assume that User ID 191 has not rated these movies, allowing us to predict the rating that User ID 191 would give to that movie and to check the error of our model using RMSE.


## (a) Prediction using user-user similarity

program will randomly pick 5 users from the above table. Using these 5 users and User ID 191, program will create a utility matrix, U, where the rows are the users and the columns are the movies. U will be filled with the ratings that a particular user gave a movie, if the user watched that movie. If the user did not watch the movie, the cell will contain an NA. Each User ID in the above table may have watched more movies than User ID 191 has, or less movies, or equal amount of movies. To ensure U has the same number of columns, program will use the intersection of the movies that User ID 191 has watched with each of the randomly chosen 5 users. U will end up being a 6x29 matrix. (Hint: See ?intersect() in R to get the intersection of movies User ID 191 has with other users.)


Using the utility matrix U, run the collaborative filtering algorithm as outlined in the lecture on recommender systems (Lecture 12, slide 28, Option 2). The similarity program will use between users is the Jaccard Similarity given to program in the above table. program will establish a neighbourhood of 3 users from the 5 randomly chosen users; so |N| = 3. Use neighbours who exhibit the highest Jaccard similarities; i.e. order the 5 randomly chosen users by their Jaccard Similarity score and pick the three users with the highest scores.

Using this neighbourhood, program will predict the ratings that User ID 191 will give to these movies using the jaccard similarity

Once program have predicted the ratings that User ID 191 will give to the 4 movies, program will calculate the RMSE.

The output should be of the following format:
	
	User ID 191, 5 random user IDs: 375, 657, 513, 50, 225.
	Using user-user similarity, User ID 191 will rate the movies as follows:
	150: X.X
	296: X.X
	380: X.X
	590: X.X
	RMSE: Y.Y


## (b) Prediction using item-item similarity: 

program will randomly pick 5 users from the above table. Using these 5 users and User ID 191, program will create a utility matrix, U, where the rows are the movies and the columns are the users. U will be filled with the ratings that a particular user gave a movie, if the user watched that movie. If the user did not watch the movie, the cell will contain an NA. Each User ID in the above table may have watched more movies than User ID 191 has, or less movies, or equal amount of movies. To ensure U has the same number of rows, program will use the intersection of the movies that User ID 191 has watched with each of the randomly chosen 5 users. U will end up being a 29x6 matrix. (Hint: See ?intersect() in R to get the intersection of movies User ID 191 has with other users.)

Using the utility matrix U, run the collaborative filtering algorithm as outlined in the lecture on recommender systems (Lecture 13, slides 29-33). The similarity program will use between users is the Pearson correlation similarity as shown in the slides. program will establish a neighbourhood of 3 items, |N| = 3. (Hint: To get the mean of each row in U, use apply(U, 1, function(x) mean(x, na.rm=T)). See ?apply().)

Using this neighbourhood, program will predict the ratings that User ID 191 will give to these movies as shown in Lecture 13, slides 29-33. (If program do encounter a case where the highest similarity row contains an item (movie) that the user 191 has not rated, simply treat that movie as a 0 (average rating). Essentially this implies that the particular item is not contributing to the computation of r_{xi}.)

Once program have predicted the ratings that User ID 191 will give to the 4 movies, program will calculate the RMSE. The output should be of the following format:

	User ID 191, 5 random user IDs: 375, 657, 513, 50, 225.
	Using item-item similarity, User ID 191 will rate the movies as follows:
	150: X.X
	296: X.X
	380: X.X
	590: X.X
	RMSE: Y.Y
