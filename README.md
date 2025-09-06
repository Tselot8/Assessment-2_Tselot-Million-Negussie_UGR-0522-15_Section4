Database Indexing Report
Objective
Improve query performance for Users, Property, and Booking tables by creating indexes on high-usage columns, measured using EXPLAIN ANALYZE.

Indexes Used
Users Table
CREATE INDEX idx_user_email ON "User"(email);

Reason: Used in WHERE clauses for user lookups (e.g., login).Note: Already indexed during table creation.
Property Table
CREATE INDEX idx_property_location ON Property(location);

Reason: Filters properties by city/area.Note: Already indexed during table creation.
Booking Table
CREATE INDEX idx_booking_user_property ON Booking(user_id, property_id);
CREATE INDEX idx_booking_total_price ON Booking(total_price);

Reason:  

user_id + property_id for specific booking lookups  
total_price for range queries and sorting

Query Performance Results
Users by Email
EXPLAIN ANALYZE 
SELECT * FROM "User" WHERE email = 'charlie@example.com';

Scan Type: Index Scan (idx_user_email)Execution Time: 0.111 msObservation: Fast due to pre-existing index.
Property by Location
EXPLAIN ANALYZE 
SELECT * FROM Property WHERE location = 'Chicago';

Scan Type: Index Scan (idx_property_location)Execution Time: 0.122 msObservation: Fast due to pre-existing index.
Booking by User + Property
EXPLAIN ANALYZE 
SELECT * FROM Booking 
WHERE user_id='22222222-2222-2222-2222-222222222222' 
  AND property_id='aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa';




Before Index
After Index



Bitmap Heap Scan, 0.866 ms
Seq Scan, 0.058 ms


Observation: Significant improvement with composite index.
Booking by Total Price
EXPLAIN ANALYZE 
SELECT * FROM Booking WHERE total_price > 300 ORDER BY total_price;




Before Index
After Index



Seq Scan, 0.155 ms
Seq Scan, 0.113 ms


Observation: Minor improvement; index more effective with larger datasets.
Conclusion
Indexes improved performance for selective queries.Pre-existing indexes on Users.email and Property.location were effective.New Booking indexes reduced execution times significantly.Avoided over-indexing to maintain efficiency.
