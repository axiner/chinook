# Assignment

## SQL
In order to complete these exercises, you'll need the `psql` command line tool.

#### psql

In your command line, type
```bash
psql chinook_development
```
Don't forget to use semi-colons after each SQL statement!

To quit, simply type ctrl + d.

### Exercises

Of course, these can be done as one or more steps.

Check out [W3Schools' SQL Reference](http://www.w3schools.com/sql/sql_syntax.asp) as a reference.

**Answer with SQL code, not the query results!**

1) Count how many tracks belong to the MediaType "Protected MPEG-4 video file".
```SQL

- SELECT *  FROM media_types where name = 'Protected MPEG-4 video file';
# id for 'Protected MPEG-4 video file' is 3
- SELECT COUNT(*) FROM tracks WHERE media_type_id = 3;

```

2) Find the least expensive Track that has the Genre "Electronica/Dance".
```SQL
/* Add your answer below */
- SELECT * from genres;
#id number is 15 for genre "Electronica/Dance"
- SELECT MIN(unit_price) from tracks WHERE genre_id = 15;
# The least price is 0.99
- SELECT name from tracks WHERE unit_price = 0.99;

```

3) Find the all the Artists whose names start with A.
```SQL
/* Add your answer below */
SELECT name FROM artists where name like 'A%';
```

4) Find all the Tracks that belong to the first Playlist.
```SQL
/* Add your answer below */
- Method 1
SELECT name from tracks WHERE id IN (SELECT track_id from playlists_tracks WHERE playlist_id = 1);

- Method 2
SELECT tracks.name FROM tracks INNER JOIN playlists_tracks ON tracks.id = playlists_tracks.track_id;

```

## Active Record Query Interface
In order to complete these exercises, you'll need to launch your Rails console with
```bash
$ rails c
```

### Practice

Getting to know the Active Record Query Interface will definitely pay off in the long run. You should read through the [Rails Guide on Active Record Query Interface](http://guides.rubyonrails.org/active_record_querying.html)  and get to know it well.

With Chinook, you can start your rails console and try out the following queries. The cool part is, the console will also show you the SQL it actually generated and ran to execute the query.

All these practice examples are making use of some of the most commonplace methods. That said, DO TRY the other ones. And don't forget, **you can chain these different methods** to refine your results!

#### Finding
##### Retrieving a Single Object
[These methods](http://guides.rubyonrails.org/active_record_querying.html#retrieving-a-single-object) will return a single object. Notice that the SQL queries that are generated always use `LIMIT 1` and sometimes include an `ORDER BY` clause.

`.find`, `.first`, `.last`, `.find_by` are very commonly used methods and you'll see them often.

```ruby
artist = Artist.find(22)
```
```ruby
album = Album.first
```
```ruby
track = Track.last
```
```ruby
my_playlist = Playlist.find_by(name: "90’s Music")
```

##### Retrieving Multiple Objects
[These methods](http://guides.rubyonrails.org/active_record_querying.html#retrieving-multiple-objects) help you find multiple objects. You can also use `.find` with an array of ids.

Other popular multiple object finders are `.all`, `.find_each`, `.find_in_batches`

```ruby
tracks = Track.find([1, 10, 55])
```
```ruby
Genre.find_each do |a|
  puts a.name
end
```

#### Conditions
`.where` is the go-to method when it comes to conditionally finding data. You can pass in conditions as SQL strings, Ruby arrays or hashes. Strings are considered unsafe, however!

Instead of...
```ruby
Artist.where("name = 'AC/DC'")
```
...you should properly sanitize any user input by using an array to prevent SQL injection!
```ruby
Artist.where("name = ?", 'AC/DC')
```

Using a hash
```ruby
Artist.where(name: 'AC/DC')
```

#### Order
You can order your queries based on one or more column with `.order`. [See more details here](http://guides.rubyonrails.org/active_record_querying.html#ordering). It can be used alone or chained with other query statements.

```ruby
Album.where(artist_id: 1).order(created_at: :desc)
```

#### Limit
You can also [limit the amount of results](http://guides.rubyonrails.org/active_record_querying.html#limit-and-offset) you get with `.limit`. Best combined with other queries.

```ruby
Playlist.limit(5)
```

#### Calculations
`.count`, `.maximum`, `.minimum`, `.average`, `.sum` are common calculation methods you can run on your results. [See more details here](http://guides.rubyonrails.org/active_record_querying.html#calculations)

```ruby
Album.count
```

```ruby
Track.minimum(:unit_price)
```

### Exercise
Of course, these can be done as one or more steps.

**Answer with Ruby code, not the query results!**

1) Count how many tracks belong to the "Hip Hop/Rap" genre
```ruby
#Find the primary id of the "Hip Hop/Rap" genre
Genre.where("name = ?", "Hip Hop/Rap")
#Count the tracks within the genre "Hip Hop/Rap" through ".count"
Track.where(genre_id: 17).count

```
2) Find the most expensive Track that has the MediaType "MPEG audio file".
```ruby
#Find the primary id of the media type "MPEG audio file"
MediaType.where("name = ?", "MPEG audio file")
#Find the track that has the maximun value in "unit price" whithin the "MPEG audio file" media type.
Track.where(media_type: 1).maximum(:unit_price)


```
3) Find the 2 oldest Artists.
```ruby
# Put the "created_at" column of the Artists list in ascending order, and shows the top 2 with "limit".
Artist.order(created_at: :asc).limit(2)

```
4) Find all the Tracks that belong to the first Playlist.
```ruby
#since Track has_one_and_belongs_to(as stated in the models) playlists, we can use "joins" to search through two lists at the same time and use playlists.id as a clue to find all the tracks within playlist 1.
Track.joins(:playlists).where("playlists.id = 1")

```
5) Find all the Tracks that belong to the 2 most recent playlists. *(HINT: This takes at least two ActiveRecord queries)*
```ruby
# First,find the primary ids of the 2 most recent playlists
Playlist.order(updated_at: :desc).limit(2)
# Then use ids as clues to find all the tracks that belong to the two playlists respectively
Track.joins(:playlists).where("playlists.id = 17")
Track.joins(:playlists).where("playlists.id = 18")
```
