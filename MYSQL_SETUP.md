# MySQL Database Setup Guide

## Prerequisites
- MySQL Server 8.0+ installed
- MySQL Workbench (optional, for GUI management)

## Step 1: Install MySQL

### Windows:
1. Download MySQL Installer from: https://dev.mysql.com/downloads/installer/
2. Run the installer and choose "Developer Default"
3. Set root password during installation (e.g., "root")
4. Complete the installation

### Verify Installation:
```bash
mysql --version
```

## Step 2: Create Database

### Option A: Using MySQL Command Line
```bash
# Login to MySQL
mysql -u root -p

# Create database
CREATE DATABASE smartclass_db;

# Create user (optional - use root for development)
CREATE USER 'smartclass'@'localhost' IDENTIFIED BY 'smartclass123';
GRANT ALL PRIVILEGES ON smartclass_db.* TO 'smartclass'@'localhost';
FLUSH PRIVILEGES;

# Verify
SHOW DATABASES;
USE smartclass_db;
```

### Option B: Using MySQL Workbench
1. Open MySQL Workbench
2. Connect to Local Instance
3. Click "Create Schema" icon
4. Name: `smartclass_db`
5. Click "Apply"

## Step 3: Configure Application

The application is already configured for MySQL in `application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/smartclass_db?createDatabaseIfNotExist=true
spring.datasource.username=root
spring.datasource.password=root
```

**Update the password** in `backend/src/main/resources/application.properties` if you used a different password.

## Step 4: Run the Application

```bash
cd F:\Face\backend
mvn clean install
mvn spring-boot:run
```

The application will automatically create all required tables in MySQL.

## Step 5: Verify Tables Created

```sql
USE smartclass_db;
SHOW TABLES;

-- You should see:
-- - users
-- - student
-- - attendance_record
```

## Switching Between H2 and MySQL

### To use H2 (for development):
In `application.properties`, comment MySQL and uncomment H2:
```properties
# Comment MySQL
# spring.datasource.url=jdbc:mysql://localhost:3306/smartclass_db

# Uncomment H2
spring.datasource.url=jdbc:h2:file:./data/smartclassdb
spring.datasource.driverClassName=org.h2.Driver
```

### To use MySQL (for production):
Keep the current MySQL configuration active.

## Common Issues

### Issue 1: "Access denied for user 'root'@'localhost'"
- Solution: Update password in `application.properties`

### Issue 2: "Communications link failure"
- Solution: Ensure MySQL service is running
  ```bash
  # Windows
  net start MySQL80
  ```

### Issue 3: "Unknown database 'smartclass_db'"
- Solution: The database will be created automatically if you have `createDatabaseIfNotExist=true` in the URL
- Or manually create: `CREATE DATABASE smartclass_db;`

### Issue 4: Port 3306 already in use
- Solution: Change MySQL port or stop other MySQL instances

## Database Schema

The following tables will be created automatically:

1. **users** - Login credentials (Admin, Teacher, Student)
2. **student** - Student information (name, roll number, class)
3. **attendance_record** - Attendance logs with timestamps

## Backup and Restore

### Backup:
```bash
mysqldump -u root -p smartclass_db > backup.sql
```

### Restore:
```bash
mysql -u root -p smartclass_db < backup.sql
```

## Production Deployment

For production, consider:
1. Creating a dedicated database user (not root)
2. Using environment variables for credentials
3. Enabling SSL connections
4. Setting up regular backups
5. Using connection pooling settings

Example production config:
```properties
spring.datasource.url=jdbc:mysql://your-server:3306/smartclass_db?useSSL=true
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
spring.datasource.hikari.maximum-pool-size=10
```
