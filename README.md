# SQL Report

## Part 1
Code for creating the restaurants table: 
```
CREATE TABLE restaurants (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    category TEXT NOT NULL,
    price_tier TEXT NOT NULL,
    neighborhood TEXT NOT NULL,
    opening_hours TEXT NOT NULL,
    closing_hours TEXT NOT NULL,
    average_rating REAL NOT NULL,
    good_for_kids INTEGER NOT NULL
    );
```

Code for creating the reviews table: 
```
CREATE TABLE reviews (
    id INTEGER PRIMARY KEY,
    restaurant_id INTEGER NOT NULL,
    username TEXT NOT NULL,
    comment TEXT NOT NULL,
    rating INTEGER NOT NULL,
    created DATETIME DEFAULT CURRENT_TIMESTAMP
    );
```
[Restaurant Data](data/rest_data.csv) \
Code for importing the above csv file:
``` 
.mode csv
.import data/rest_data.csv restaurants
```

1. Find all cheap restaurants in a particular neighborhood (pick any neighborhood as an example).
```
SELECT * FROM restaurants WHERE neighborhood = 'Battery Park City' AND price_tier = 'cheap';
```

2. Find all restaurants in a particular genre (pick any genre as an example) with 3 stars or more, ordered by the number of stars in descending order.
```
SELECT * FROM restaurants WHERE category = 'Japan' AND average_rating >= 3 ORDER BY average_rating DESC;
```

3. Find all restaurants that are open now (see hint below).
```
SELECT * FROM restaurants WHERE strftime('%H:%M', 'now') BETWEEN opening_hours AND closing_hours;
```

4. Leave a review for a restaurant (pick any restaurant as an example).
```
INSERT INTO reviews (restaurant_id, username, comment, rating)
    VALUES (
    (SELECT id FROM restaurants WHERE name = 'Fay Group'),
    'aa7270',
    'Amazing food and incredible variety!',
    5
    );
```

5. Delete all restaurants that are not good for kids.
```
DELETE FROM restaurants WHERE good_for_kids = 0;
```

6. Find the number of restaurants in each NYC neighborhood.
```
SELECT neighborhood, COUNT(*) FROM restaurants GROUP BY neighborhood;
```

## Part 2
Code for creating the users table:
```
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    email TEXT NOT NULL,
    username TEXT NOT NULL,
    password TEXT NOT NULL
    );
```

Code for creating the posts table:
```
CREATE TABLE posts (
    id INTEGER PRIMARY KEY,
    type TEXT NOT NULL,
    visibility INTEGER DEFAULT 1 NOT NULL,
    text TEXT NOT NULL,
    created DATETIME DEFAULT CURRENT_TIMESTAMP,
    to_user_id INTEGER NOT NULL, 
    from_user_id INTEGER NOT NULL
    );
```
[User Data](data/user_data.csv) \
[Posts](data/post_data.csv) \
Code for importing the above csv files: 
```
.mode csv
.import data/user_data.csv users
.import data/post_data.csv posts
```

1. Register a new User.
```
INSERT INTO users (username, password, email) VALUES ('aalex', '1234', 'aa7270@nyu.edu');
```

2. Create a new Message sent by a particular User to a particular User (pick any two Users for example).
```
INSERT INTO posts (type, visibility, text, to_user_id, from_user_id)
VALUES ('message', 1, 'Did you do your Physics homework?', 251, 600);
```

3. Create a new Story by a particular User (pick any User for example).
```
INSERT INTO posts (type, visibility, text, to_user_id, from_user_id)
VALUES ('story', 1, 'This is my first day at NYU!', -1, 666);
```

4. Show the 10 most recent visible Messages and Stories, in order of recency.
```
SELECT * FROM posts WHERE visibility = 1 ORDER BY created DESC LIMIT 10;
```

5. Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.
```
SELECT * FROM posts WHERE visibility = 1 AND from_user_id = 3 AND to_user_id = 546 AND type = 'message' ORDER BY created DESC LIMIT 10;
```

6. Make all Stories that are more than 24 hours old invisible.
```
UPDATE posts SET visibility = 0 WHERE type = 'story' AND ROUND((JULIANDAY('now') - JULIANDAY(created)) * 24) > 24;
```

7. Show all invisible Messages and Stories, in order of recency.`
```
SELECT * FROM posts WHERE visibility = 0 ORDER BY created DESC;
```

8. Show the number of posts by each User.
```
SELECT username, COUNT(*) FROM users left join posts ON users.id = posts.from_user_id GROUP BY username;
```

9. Show the post text and email address of all posts and the User who made them within the last 24 hours.
```
SELECT posts.text, users.email FROM users left join posts ON users.id = posts.from_user_id WHERE ROUND((JULIANDAY('now') - JULIANDAY(created)) * 24 )< 24;
```

10. Show the email addresses of all Users who have not posted anything yet.
```
SELECT email FROM users
WHERE id NOT IN (SELECT from_user_id FROM posts);
```

