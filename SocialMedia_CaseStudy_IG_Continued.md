# Questions

1. **Objective:** Identify the first five users on our social media platform for the purpose of acknowledging and rewarding their long-standing contributions.
  -- Our oldest users are'Darby_Herzog','Emilio_Bernier52' 'Elenor88' 'Nicole71' and 'Jordyn.Jacobson2' we could send them a plaque rewarding them for being our earliest users and encourage them to share it on their feed.
```
SELECT * FROM users 
ORDER BY created_at
LIMIT 5;
```

2. **Objective:** Determine the most active day for registrations on our social media platform to strategically schedule and optimize our “join now” advertising efforts on that particular day.

   | Day        | Registrations |
   |------------|---------------|
   | Thursday   | 16            |
   | Sunday     | 16            |
```
SELECT dayname(created_at) as day, COUNT(*) as total FROM users
GROUP BY day
ORDER BY total DESC;
```

3. **Objective:** Identify and categorize inactive users who have never posted on the platform.

   | User               | Activity |
   |--------------------|----------|
   | Aniya_Hackett      | NULL     |
   | Bartholome.Bernhard | NULL     |
   | Bethany20          | NULL     |
   | Darby_Herzog       | NULL     |
   | David.Osinski47    | NULL     |
   | Duane60            | NULL     |
   | Esmeralda.Mraz57   | NULL     |
   | Esther.Zulauf61    | NULL     |
   | Franco_Keebler64   | NULL     |
   | Hulda.Macejkovic   | NULL     |
   | Jaclyn81           | NULL     |
   | Janelle.Nikolaus81 | NULL     |
   | Jessyca_West       | NULL     |
   | Julien_Schmidt     | NULL     |
   | Kasandra_Homenick  | NULL     |
   | Leslie67           | NULL     |
   | Linnea59           | NULL     |
   | Maxwell.Halvorson  | NULL     |
   | Mckenna17          | NULL     |
   | Mike.Auer39        | NULL     |
   | Morgan.Kassulke    | NULL     |
   | Nia_Haag           | NULL     |
   | Ollie_Ledner37     | NULL     |
   | Pearl7             | NULL     |
   | Rocio33            | NULL     |
   | Tierra.Trantow     | NULL     |
```
SELECT username, image_url
FROM users
LEFT JOIN photos ON users.id = photos.user_id
WHERE photos.id IS NULL;
```

4. **Objective:** We want to identify the user with the most likes on a single photo in the last year to reward them with a prize. Identify the user so we can reach out to them.

   | User          | Likes | Image ID |
   |---------------|-------|----------|
   | Zack_Kemmer93 | 48    | 145      |
```
SELECT users.username, photos.id, COUNT(photos.id) AS total_likes, photos.image_url
FROM users
JOIN photos ON users.id = photos.user_id
JOIN likes ON likes.photo_id = photos.id
GROUP BY photos.id
ORDER BY total_likes DESC
LIMIT 1;
```

5. **Objective:** Determine the average frequency of user posts on the platform to gain insights into user engagement and content generation patterns.
```
SELECT COUNT(*) / (SELECT COUNT(*) FROM users) AS avg_posts FROM photos;
```
6. **Objective:** Determine what's trending on our social media platform to inform our next marketing campaign. We want to use the top 5 hashtags to help boost our next media posts.

   | Hashtag | Frequency | Count |
   |---------|-----------|-------|
   | smile   | 21        | 59    |
   | beach   | 20        | 42    |
   | party   | 17        | 39    |
   | fun     | 13        | 38    |
   | concert | 18        | 24    |

```
SELECT tag_name, tag_id, COUNT(tag_id) AS times_used FROM photo_tags
JOIN tags ON photo_tags.tag_id = tags.id
GROUP BY tag_id
ORDER BY times_used DESC
LIMIT 5;
```

7. **Objective:** We want to cut down on bots using our platform, help us identify some potential bots on the site so we can deactivate their account and monitor these tendencies in the future.

   - I decided to look through which accounts had either liked or commented on every single post
     - Thought process – count the number of total photos on the platform
     - Each photo can only be liked once per user
     - See which users had liked all photos on the platform by joining the tables and counting each instance of user_id

   | User               | Count | Total Photos |
   |--------------------|-------|--------------|
   | Aniya_Hackett      | 5     | 257          |
   | Bethany20          | 91    | 257          |
   | Duane60            | 54    | 257          |
   | Jaclyn81           | 14    | 257          |
   | Janelle.Nikolaus81 | 76    | 257          |
   | Julien_Schmidt     | 57    | 257          |
   | Leslie67           | 75    | 257          |
   | Maxwell.Halvorson  | 24    | 257          |
   | Mckenna17          | 41    | 257          |
   | Mike.Auer39        | 66    | 257          |
   | Nia_Haag           | 71    | 257          |
   | Ollie_Ledner37     | 36    | 257          |
   | Rocio33            | 21    | 257          |
```
-- Question 7
-- First atempt
SELECT username, COUNT(photo_id) AS total_photos, user_id, COUNT(user_id) AS total_likes FROM users
JOIN likes ON users.id = likes.user_id
GROUP BY user_id
HAVING total_likes = (SELECT COUNT(*) FROM photos)
ORDER BY total_likes DESC;

-- Cleaner code
SELECT 
	username,
    user_id,
    COUNT(*) AS total
FROM users
JOIN likes
	ON users.id = likes.user_id
GROUP BY likes.user_id
HAVING total = (SELECT COUNT(*) FROM photos);
```
