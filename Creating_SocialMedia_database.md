Creating our Instagram-like database

Step 1: What do we need to store
-	Photos 
o	tags
-	Captions and Hashtags
-	Usernames
-	Likes
-	Comments
-	Followers
-	Following
-	Step 2 a: Research – has anyone solved this problem before? Can you learn from their work and solutions to make your work faster and cleaner
-	
Step 2: Constraints
-	Each profile can only like a photo once
-	People can follow someone and not be followed back and vice versa
-	Username should be unique. 
-	Photos must be attached to a user
-	Cant follow yourself
-	Can only follow somebody one time 

Step 3: Table Design – Brainstorm potential ideas of what each table might consist of and identify some primary and foreign keys. Might not add all these features if too complex. 
-	Create tables for Users, Photos, Likes, Comments, and Followers.
-	Each table needs a primary key for uniqueness.
-	Use foreign keys to link tables for data consistency.

1.	Users Table
•	User ID (Primary Key)
•	Username
•	Full Name
•	Email
•	Password (encrypted)
•	Profile Picture
•	Bio
•	Registration Date
•	Other User-Related Information
2.	Photos Table
•	Photo ID (Primary Key)
•	User ID (Foreign Key to Users Table)
•	Photo URL
•	Caption
•	Timestamp
•	Location
•	Tags
•	Other Photo-Related Information
3.	Likes Table
•	Like ID (Primary Key)
•	User ID (Foreign Key to Users Table)
•	Photo ID (Foreign Key to Photos Table)
•	Timestamp
•	(To enforce the constraint, each row in this table represents a user liking a photo.)
4.	Comments Table
•	Comment ID (Primary Key)
•	User ID (Foreign Key to Users Table)
•	Photo ID (Foreign Key to Photos Table)
•	Comment Text
•	Timestamp
5.	Followers Table
•	Follower ID (Primary Key)
•	Follower User ID (Foreign Key to Users Table)
•	Followed User ID (Foreign Key to Users Table)
•	Timestamp
•	(This table allows users to follow other users. No need for reciprocal entries as not all follows are mutual.)
6.	Hashtags Table
•	To speed things up we will create 2 tables to handle hashtags this article suggests it is the fastest 
http://howto.philippkeller.com/2005/06/19/Tagsystems-performance-tests/
•	By Doing this we reduce duplication 
•	We cam store more info about the tags like time.. 
•	Photo_tags
1.	Photo_id
2.	Tag_id
•	Tags
1.	Id
2.	Tag_name

Step 5: Sketch out database table schemas to make things easier to look at:
![Database Schema](https://github.com/thoetz/Exploring_SQL/blob/20556876390b3651aed91f190d63bdf9797a7b87/IG_database_schema.png)


Step 6: Data Types and Attributes
-	Decide on data types for attributes (e.g., strings for names and email, date/time for timestamps).
-	Opt for the right data types to save space and enhance performance.

Step 7: Create DataBase in MY SQL
```
-- Creating and working with an instagram like data base
CREATE DATABASE instagram_clone;
USE instagram_clone;

-- Creating users table
CREATE TABLE users (
	id INT auto_increment PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
    );
DESC users;
-- make sure our table works properly
INSERT INTO users (username)
	VALUES ('thomasjgoetz');
SELECT * FROM users;
-- Creating photos table
CREATE TABLE photos (
	id INT auto_increment PRIMARY KEY,
    image_url VARCHAR(255) NOT NULL,
    user_id INT NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    FOREIGN KEY(user_id) REFERENCES users(id)
    );

DESC photos;
SHOW TABLES;

-- Create our comments table and connect it to user and photo
CREATE TABLE comments (
		id INT auto_increment PRIMARY KEY,
        comment_text VARCHAR (255) NOT NULL,
        user_id INT NOT NULL,
        photo_id INT NOT NULL, 
        created_at TIMESTAMP DEFAULT NOW(),
        FOREIGN KEY (user_id) REFERENCES users(id),
        FOREIGN KEY (photo_id) REFERENCES photos(id)
);
-- Check it 
DESC comments;

-- Create likes table
CREATE TABLE likes (
    user_id INT NOT NULL, -- user who liked the photo
    photo_id INT NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    FOREIGN KEY (user_id) REFERENCES users(id),
	FOREIGN KEY (photo_id) REFERENCES photos(id),
    PRIMARY KEY(user_id, photo_id) -- ensures no duplicate likes
);
DESC likes;

-- Follows table
-- followee will be the person being followed 
-- follower will be the person doing the following
CREATE TABLE follows (
	follower_id INT NOT NULL,
    followee_id INT NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    PRIMARY KEY(follower_id, followee_id),
    FOREIGN KEY(follower_id) REFERENCES users(id),
    FOREIGN KEY(followee_id) REFERENCES users(id)
);
DESC follows;
-- Create hashtags tables using 2 tables to increase speed and allow for more flexibility later on when querying data and looking for insights on trends etc.
-- tags table lets us do analysis on tags themselves when looking at trends
CREATE TABLE tags (
  id INTEGER AUTO_INCREMENT PRIMARY KEY,
  tag_name VARCHAR(255) UNIQUE,
  created_at TIMESTAMP DEFAULT NOW()
);
DESC tags;
-- This photo_tags table associates a tag with a photo
CREATE TABLE photo_tags (
    photo_id INTEGER NOT NULL,
    tag_id INTEGER NOT NULL,
    FOREIGN KEY(photo_id) REFERENCES photos(id),
    FOREIGN KEY(tag_id) REFERENCES tags(id),
    PRIMARY KEY(photo_id, tag_id)
);
DESC photo_tags;
show tables;
```

