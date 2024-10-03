# Library Management System Database

## Overview
This project implements a comprehensive database schema for a library management system. It provides the foundation for tracking books, patrons, checkouts, and various library operations efficiently.

## Features
- Complete book management (titles, authors, publishers, categories)
- Patron account tracking
- Checkout and return system
- Hold and waitlist functionality
- Notification system

## Database Schema

### Core Tables
1. **categories** - Book categories/genres
2. **books** - Main book information
3. **authors** - Author details
4. **publishers** - Publisher information
5. **book_copy** - Individual physical copies of books

### Relationship Tables
6. **book_author** - Links books to authors (many-to-many)
7. **book_details** - Extended book information

### Patron and Operations Tables
8. **patron_accounts** - Library member information
9. **checkout** - Tracks borrowed books
10. **hold** - Manages book holds
11. **waitlist** - Handles waiting lists for popular books
12. **notifications** - System for patron notifications

## Implementation Guide

### Prerequisites
- PostgreSQL 12.0 or higher (recommended)
- psql command-line tool or any PostgreSQL client

### Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/library-management-system.git
cd library-management-system
```

2. Create the database:
```bash
psql -U postgres
CREATE DATABASE library_management;
\c library_management
```

3. Run the schema script:
```bash
psql -U postgres -d library_management -f schema.sql
```

## Usage Examples

### Adding a New Book
```sql
-- Add publisher if not exists
INSERT INTO publishers (publisher_id, name)
VALUES (1, 'Penguin Books');

-- Add author if not exists
INSERT INTO authors (author_id, author_name)
VALUES (1, 'George Orwell');

-- Add category if not exists
INSERT INTO categories (id, name)
VALUES (1, 'Fiction');

-- Add the book
INSERT INTO books (book_id, title, category_id, publisher_id, isbn)
VALUES (1, '1984', 1, 1, '9780451524935');

-- Link book to author
INSERT INTO book_author (book_id, author_id)
VALUES (1, 1);

-- Add physical copy of the book
INSERT INTO book_copy (copy_id, book_id, status)
VALUES (1, 1, 'available');
```

### Checking Out a Book
```sql
-- Assuming patron_id 1 exists
INSERT INTO checkout (checkout_id, copy_id, patron_id, checkout_date, due_date)
VALUES (1, 1, 1, CURRENT_DATE, CURRENT_DATE + INTERVAL '14 days');

-- Update book copy status
UPDATE book_copy
SET status = 'checked_out'
WHERE copy_id = 1;
```

### Common Queries

#### Find All Available Copies of a Book
```sql
SELECT b.title, bc.copy_id
FROM books b
JOIN book_copy bc ON b.book_id = bc.book_id
WHERE bc.status = 'available';
```

#### Get Patron's Current Checkouts
```sql
SELECT b.title, c.checkout_date, c.due_date
FROM checkout c
JOIN book_copy bc ON c.copy_id = bc.copy_id
JOIN books b ON bc.book_id = b.book_id
WHERE c.patron_id = 1 AND c.return_date IS NULL;
```

## Best Practices

1. **Use Transactions**: When performing multiple related operations, use transactions:
```sql
BEGIN;
-- Perform operations
COMMIT;
```

2. **Regular Backups**: Implement regular database backups:
```bash
pg_dump library_management > backup_filename.sql
```

3. **Indexing**: Consider adding indexes for frequently queried columns:
```sql
CREATE INDEX idx_book_title ON books(title);
```

## Performance Considerations

- Use appropriate data types (e.g., ISBN as VARCHAR(13))
- Implement indexes on frequently searched columns
- Use EXPLAIN ANALYZE to optimize queries
- Consider partitioning large tables (e.g., checkout history)

## Maintenance

### Regular Tasks
1. Archive old records
2. Update statistics
3. Reindex tables
4. Check for data integrity

### Sample Maintenance Queries
```sql
-- Update table statistics
ANALYZE checkout;

-- Find and resolve orphaned records
SELECT * FROM book_copy bc
LEFT JOIN books b ON bc.book_id = b.book_id
WHERE b.book_id IS NULL;
```

## Security Considerations

1. **User Management**: Create appropriate user roles
```sql
CREATE ROLE librarian LOGIN PASSWORD 'secure_password';
GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA public TO librarian;
```

2. **Data Validation**: Always validate data before insertion
3. **Secure Connections**: Use SSL for database connections

## Troubleshooting

### Common Issues and Solutions

1. **Slow Queries**
   - Use EXPLAIN ANALYZE to identify bottlenecks
   - Check and update indices
   - Optimize query structure

2. **Data Integrity**
   - Run periodic checks:
```sql
-- Check for books without copies
SELECT book_id, title FROM books
WHERE book_id NOT IN (SELECT book_id FROM book_copy);
```

## Contributing

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Create a new Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

---

For more information or support, please open an issue in the GitHub repository.