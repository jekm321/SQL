# SQL

See [the Lambda page on Relational
Databases](https://github.com/LambdaSchool/Relational-Databases) for more
information. (Note that page is for PostgreSQL, but the SQL information is valid
here.)

## SQLite

SQLite is a popular, simple SQL database.

You can launch into a memory-only DB by running:

```
sqlite3
```

You can specify a persistent DB file with:

```
sqlite3 mydatabase.db
```

When you get to the prompt, you can type `.help` for commands.

Some helpful ones:

* `.mode column` turn on column output for `SELECT`.
* `.header on` turn on column headers for `SELECT`.
* `.read filename` execute the SQL in `filename`.
* `.open dbname` re-open a memory-only DB to a persistent file.
* `.quit` exit SQLite. (Note that if you're using a memory-only DB, all
  data is lost at this point.)

Another potentially useful third-party tool is [DB Browser for
SQLite](https://sqlitebrowser.org/), a GUI-based SQLite viewer and data
manipulator that can also run SQL queries.


## Create a Music Database

Make an albums table to hold album information:

```sql
CREATE TABLE album (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title VARCHAR(128) NOT NULL,
    release_year INTEGER
);
```

Make an artists table to hold artist information:

```sql
CREATE TABLE artist (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name VARCHAR(128) NOT NULL
);
```

Make a track table to hold track information:

```sql
CREATE TABLE track (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title VARCHAR(128) NOT NULL,
    album_id INTEGER,
    FOREIGN KEY (album_id) referenes album(id)
);
```

Make an artist_album information to hold information of artist vs album:

```sql
CREATE TABLE artist_album (
  artist_album_id INTEGER PRIMARY KEY AUTOINCREMENT,
  artist_id INTEGER,
  album_id INTEGER,
  FOREIGN KEY (artist_id) REFERENCES artist(id),
  FOREIGN KEY (album_id) REFERENCES ablum(id)
);
```

### Exercises, Day 1

Before you begin, look at the queries in `setup.sql` to get a hint as to the
column names in the following tables. We'll use `setup.sql` later.

* Create a table called `track` that holds information about a music track. It should contain:
  * An autoincrementing `id`
  * A title (of type `VARCHAR`, probably)
  * A reference to an `id` in table `album` (the album the track is on). This
    should be a _foreign key_.

* Create a table called `artist_album` to connect artists to albums. (Note that
  an artist might have several albums and an album might be created by multiple
  artists.)
  * Use foreign keys for this, as well.
 
* Run the queries in the file `setup.sql`. This will populate the tables.
  * Fix any errors at this point by making sure your tables are correct.
  * `DROP TABLE` can be used to delete a table so you can recreate it with
    `CREATE TABLE`.

* Write SQL `SELECT` queries that:
  * Show all albums.  

  ```sql
  SELECT * FROM album;
  ```

  * Show all albums made between 1975 and 1990. 

  ```sql
  SELECT * FROM album WHERE release_year > 1975 and release_year < 1990;
  ```

  * Show all albums whose names start with `Super D`. 

  ```sql
  SELECT * FROM album WHERE title LIKE 'Super D%';
  ```
  
  * Show all albums that have no release year. 

  ```sql
  SELECT * FROM album WHERE release_year is NULL;
  ```

* Write SQL `SELECT` queries that:
  * Show all track titles from `Super Funky Album`. 

  ```sql
  SELECT track.title FROM track, album WHERE track.album_id = album.id  AND album.title LIKE 'Super Funky Album';
  ```
  * Same query as above, but rename the column from `title` to `Track_Title` in
    the output. 

  ```sql
  SELECT track.title AS 'Track_Title' FROM track, album WHERE track.album_id = album.id AND album.title LIKE 'Super Funky Album';
  ```

  * Select all album titles by `Han Solo`. 

  ```sql
  SELECT album.title FROM artist, album, artist_album WHERE artist_album.artist_id = artist.id AND artist_album.album_id = album.id AND artist.name = 'Han Solo';
  ```

  ```sql
  SELECT album.title FROM album INNER JOIN artist_album ON artist_album.album_id = album.id INNER JOIN artist ON artist.id = artist_album.artist_id AND artist.name = 'Han Solo';
  ```

  * Select the average year all albums were released. 

  ```sql
  SELECT AVG(release_year) FROM album;
  ```

  * Select the average year all albums by `Leia and the Ewoks` were released. 

  ```sql
  SELECT AVG(album.release_year) FROM artist, album, artist_album WHERE artist_album.artist_id = artist.id AND artist_album.album_id = album.id AND artist.name = 'Leia and the Ewoks';
  ```

  * Select the number of artists. 

  ```sql
  SELECT COUNT() AS '#' FROM artist;
  ```

  * Select the number of tracks on `Super Dubstep Album`.

  ```sql
  SELECT COUNT(track.title) FROM track, album WHERE track.album_id = album.id AND album.title = 'Super Dubstep Album';
  ```

### Exercises, Day 2

Create a database for taking notes.

* What are the columns that a note table needs?

```sql
CREATE TABLE notes (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title VARCHAR(128),
  content VARCHAR(1024),
  author_id INTEGER,
  datetime TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (author_id) REFERENCES author(id)
);
```

* If you have a timestamp field, how do you auto-populate it with the date? 

added to create table above

* A note should have a foreign key pointing to an author in an author table.

added to create table above

* What columns are needed for the author table?

```sql
CREATE TABLE author (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name VARCHAR(128)
);
```

Write queries that:

* Insert authors to the author table.

```sql
INSERT INTO author (name) VALUES ('E. A. Poe');
INSERT INTO author (name) VALUES ('John Doe');
INSERT INTO author (name) VALUES ('Jack Frost');
INSERT INTO author (name) VALUES ('Anthony Horowitz');
INSERT INTO author (name) VALUES ('Lois Lowry');
```

* Insert notes to the note table.

```sql
INSERT INTO notes (title, content, author_id) VALUES ('Deaths', 'Need to write more depressing books', 1);
INSERT INTO notes (title, content, author_id) VALUES ('Gifts', 'Need to give kids more gifts', 3);
INSERT INTO notes (title, content, author_id) VALUES ('Series', 'Need to finish Alan Walker series', 4);
INSERT INTO notes (title, content, author_id) VALUES ('Triplet', 'Need to hardback my triplet series', 5);
INSERT INTO notes (title, content, author_id) VALUES ('Christmas', 'Make it Snow', 3);
INSERT INTO notes (title, content, author_id) VALUES ('Anon', 'Stay Anonymous', 2);
INSERT INTO notes (title, content, author_id) VALUES ('Giving', 'I wrote The Giver', 5);
```

* Select all notes by an author's name.

```sql
SELECT title, content FROM notes, author WHERE notes.author_id = author.id AND author.name = 'Jack Frost';
```

* Select author for a particular note by note ID.

```sql
SELECT name FROM author, notes WHERE notes.id = 3 AND notes.author_id = author.id;
```

* Select the names of all the authors along with the number of notes they each have. (Hint: `GROUP BY`.)

```sql
SELECT author.name, COUNT(notes.id) FROM author, notes WHERE notes.author_id = author.id GROUP BY author.name;
```

* Delete authors from the author table.
  > Note that SQLite doesn't enforce foreign key constrains by default. You have
  > to enable them by running `PRAGMA foreign_keys = ON;` before your queries.
  
  * What happens when you try to delete an author with an existing note?
    `Error: foreign key constraint failed`

  * How can you prevent this?
  ```sql
  CREATE TABLE tablename (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    mykey INTEGER,
    FOREIGN KEY (mykey) REFERENCES maintable(id) ON DELETE CASCADE
  );
  ```
  `ON DELETE CASCADE` allows the primary key from the referenced table to be deleted without a foreign key constraint

  there is also a `ON UPDATE CASCADE`

Submit a file `notes.sql` with the queries that build (`CREATE TABLE`/`INSERT`)
and query the database as noted above.