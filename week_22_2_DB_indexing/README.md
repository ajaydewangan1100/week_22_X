## Indexing postgres (using Docker )

1. Run postgres in docker - `docker run  -p 5432:5432 -e POSTGRES_PASSWORD=mysecretpassword -d postgres`
2. Go under container using executable - `docker exec -it container_id /bin/bash`
3. Now we are under container, if we want to run postgres CMDs we can use `psql` (Bydefault given under postgres DB) -
   `psql -U postgres`
4. Create user table (paste cmd under psql CLI) -

   ```
   CREATE TABLE users (
   user_id SERIAL PRIMARY KEY,
   email VARCHAR(255) UNIQUE NOT NULL,
   password VARCHAR(255) NOT NULL,
   name VARCHAR(255)
   );
   ```

5. Create posts table (paste cmd under psql CLI) -

   ```
   CREATE TABLE posts (
   post_id SERIAL PRIMARY KEY,
   user_id INTEGER NOT NULL,
   title VARCHAR(255) NOT NULL,
   description TEXT,
   image VARCHAR(255),
   FOREIGN KEY (user_id) REFERENCES users(user_id)
   );
   ```

6. Insert some dummy data in - (5 users, and 50000 posts for each user) -

   ```
   DO $$
   DECLARE
       returned_user_id INT;
   BEGIN
       -- Insert 5 users
       FOR i IN 1..5 LOOP
           INSERT INTO users (email, password, name) VALUES
           ('user'||i||'@example.com', 'pass'||i, 'User '||i)
           RETURNING user_id INTO returned_user_id;

           FOR j IN 1..500000 LOOP
               INSERT INTO posts (user_id, title, description)
               VALUES (returned_user_id, 'Title '||j, 'Description for post '||j);
           END LOOP;
       END LOOP;
   END $$;
   ```

7. Can run query like this - ` SELECT * FROM posts WHERE user_id=1 LIMIT 5;`
8. If we want to analyse the query - ` EXPLAIN ANALYSE SELECT * FROM posts WHERE user_id=1 LIMIT 5;`
   (Note - we can analyse the time it is taking for the query)

9. Create `user_id` field index - `CREATE INDEX idx_user_id ON posts (user_id);`
   (Now if we analyse we can see execution time of the query)

### Complaex indexing (indexing multiple columns) -

    (first do query with id and title and analyse, then after indexing again analyse)

    `CREATE INDEX idx_posts_user_id_title ON posts (description, title);`

    ` SELECT * FROM posts WHERE title='title' AND description='my title';`
