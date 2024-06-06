# postgres-vector
postgres-vector

Testing database vector operations in PostgreSQL involves several steps, from setting up the database to executing and verifying vector operations. Here’s a step-by-step guide to help you through the process:

### Step 1: Set Up PostgreSQL
1. **Install PostgreSQL:**
   - Download and install PostgreSQL from the [official website](https://www.postgresql.org/download/).

2. **Start PostgreSQL Service:**
   - On Windows, use the services manager or pgAdmin.
   - On Linux, use the command:
     ```bash
     sudo service postgresql start
     ```

### Step 2: Create a Database and a User
1. **Open psql (PostgreSQL command-line tool):**
   ```bash
   psql -U postgres
   ```

2. **Create a new database:**
   ```sql
   CREATE DATABASE vector_test_db;
   ```

3. **Create a new user:**
   ```sql
   CREATE USER vector_user WITH PASSWORD 'password';
   ```

4. **Grant privileges to the user:**
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE vector_test_db TO vector_user;
   ```

### Step 3: Connect to the Database
```bash
psql -U vector_user -d vector_test_db
```

### Step 4: Create a Table for Vectors
```sql
CREATE TABLE vectors (
    id SERIAL PRIMARY KEY,
    vector_data FLOAT8[]
);
```

### Step 5: Insert Vector Data
```sql
INSERT INTO vectors (vector_data) VALUES 
('{1.0, 2.0, 3.0}'),
('{4.0, 5.0, 6.0}'),
('{7.0, 8.0, 9.0}');
```

### Step 6: Perform Vector Operations
1. **Calculate the length of a vector:**
   ```sql
   SELECT id, vector_data, 
   sqrt((vector_data[1] * vector_data[1]) + 
        (vector_data[2] * vector_data[2]) + 
        (vector_data[3] * vector_data[3])) AS length 
   FROM vectors;
   ```

2. **Dot product of two vectors:**
   ```sql
   SELECT v1.id, v2.id,
   (v1.vector_data[1] * v2.vector_data[1]) +
   (v1.vector_data[2] * v2.vector_data[2]) +
   (v1.vector_data[3] * v2.vector_data[3]) AS dot_product
   FROM vectors v1, vectors v2
   WHERE v1.id <> v2.id;
   ```

3. **Euclidean distance between two vectors:**
   ```sql
   SELECT v1.id, v2.id,
   sqrt((v1.vector_data[1] - v2.vector_data[1]) * (v1.vector_data[1] - v2.vector_data[1]) +
        (v1.vector_data[2] - v2.vector_data[2]) * (v1.vector_data[2] - v2.vector_data[2]) +
        (v1.vector_data[3] - v2.vector_data[3]) * (v1.vector_data[3] - v2.vector_data[3])) AS distance
   FROM vectors v1, vectors v2
   WHERE v1.id <> v2.id;
   ```

### Step 7: Indexing for Faster Vector Operations (Optional)
1. **Install the `cube` extension:**
   ```sql
   CREATE EXTENSION cube;
   ```

2. **Create a table with `cube` data type:**
   ```sql
   CREATE TABLE cube_vectors (
       id SERIAL PRIMARY KEY,
       vector_data cube
   );
   ```

3. **Insert data into the `cube_vectors` table:**
   ```sql
   INSERT INTO cube_vectors (vector_data) VALUES 
   (cube(array[1.0, 2.0, 3.0])),
   (cube(array[4.0, 5.0, 6.0])),
   (cube(array[7.0, 8.0, 9.0]));
   ```

4. **Perform vector operations using the `cube` data type:**
   - **Distance between vectors:**
     ```sql
     SELECT id, (vector_data <-> cube(array[0.0, 0.0, 0.0])) AS distance
     FROM cube_vectors
     ORDER BY distance;
     ```

### Step 8: Verify Results
- Run the queries and check the results to ensure the vector operations are performed correctly.

### Additional Notes:
- For more complex operations, consider using specialized extensions like `pgvector`.
- Always backup your database before performing major operations.
- Ensure your PostgreSQL version supports the necessary extensions and data types. 

This guide should help you get started with vector operations in PostgreSQL.
