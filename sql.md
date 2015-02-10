Movie ( mID, title, year, director ) 
Reviewer ( rID, name ) 
Rating ( rID, mID, stars, ratingDate ) 

//Find the titles of all movies directed by Steven Spielberg. 
select title from Movie
where director = 'Steven Spielberg';

//Find all years that have a movie that received a rating of 4 or 5, and sort them in increasing order. 
select year from Movie
where mID in (select mID from Rating
    where stars >= 4
)
order by year;

//Find the titles of all movies that have no ratings. 
select title from Movie
where mID not in (
    select mID from Rating);
    
//Some reviewers didn't provide a date with their rating. Find the names of all reviewers who have ratings with a NULL value for the date. 
select name from Reviewer
where rID in (
    select rID from Rating
    where ratingDate is null);
    
//Write a query to return the ratings data in a more readable format: reviewer name, movie title, stars, and ratingDate. Also, sort the data, first by reviewer name, then by movie title, and lastly by number of stars. 
select distinct Reviewer.name, Movie.title, Rating.stars,Rating.ratingDate
from Reviewer, Movie, Rating
where Reviewer.rID = Rating.rID and
    Movie.mID = Rating.mID
order by Reviewer.name, Movie.title, Rating.stars;

//For all cases where the same reviewer rated the same movie twice and gave it a higher rating the second time, return the reviewer's name and the title of the movie. 
select name, title
from Reviewer, Rating, Movie, Rating r2
where Reviewer.rID = Rating.rID and Movie.mID = Rating.mID
and r2.rID = Rating.rID and r2.mID = Rating.mID
and Rating.stars < r2.stars and Rating.ratingDate < r2.ratingDate;

//For each movie that has at least one rating, find the highest number of stars that movie received. Return the movie title and number of stars. Sort by movie title.
select title, max(stars) from Movie, Rating
where Movie.mID = Rating.mID
group by Movie.mID
order by title;

//For each movie, return the title and the 'rating spread', that is, the difference between highest and lowest ratings given to that movie. Sort by rating spread from highest to lowest, then by movie title. 
select title, max(stars) - min(stars) as spread
from Movie, Rating
where Movie.mID = Rating.mID
group by Movie.mID
order by spread desc, title;

//Find the difference between the average rating of movies released before 1980 and the average rating of movies released after 1980. (Make sure to calculate the average rating for each movie, then the average of those averages for movies before 1980 and movies after. Don't just calculate the overall average rating before and after 1980.) 
select abs(before1980.avgRating - after1980.avgRating)
from ( select avg(avgMovie) as avgRating
        from (select avg(stars) as avgMovie
                from Rating, Movie
                where Rating.mID = Movie.mID
                and Movie.year < 1980
                group by Movie.mID)) as before1980,
        (select avg(avgMovie) as avgRating
        from (select avg(stars) as avgMovie
                from Rating, Movie
                where Rating.mID = Movie.mID
                and Movie.year > 1980
                group by Movie.mID)) as after1980;



select name
from Reviewer join Rating using(rID)
group by name
having count(name) >= 3;
