# Project Planning
A step-by-step approach to begin with **Integrated Advertisement Management System (IAMS)**:

### **Step 1: Planning and Requirements Gathering**
1. **Features and Functionalities**  
   What the system should do:
   - User roles (admin, advertiser, viewer, etc.).
   - Manage advertisements (creation, editing, deletion).
   - View analytics (clicks, views, performance).
   - Payment or subscription handling (if applicable).

2. **Wireframes**  
   Basic layout sketches of the application using [Figma](figma.com/...) and pen, paper.

3. **Database Design**  
   - Tables we'll need (e.g., users, ads, categories, analytics).
   - Relationships between the tables (e.g., users create ads, ads belong to categories).
   - See [Schemas Creation section](#schemas-creation) for designing Schemas

### **Step 2: Development Environment Setup**
1. **Laravel Setup**  
   - We'll use Laravel.
   - `.env` file for database configuration.

2. **Database Setup**  
   - Will be using MySQL database for the project.
   - Laravel migrations for managing schemas.

3. **Bootstrap Integration**  
   - Install Bootstrap (via CDN or npm).
   - Set up a base layout file in Laravel with a responsive navbar.

4. **Version Control**  
   - Initialize a Git repository.
   - Create meaningful `.gitignore` rules (e.g., ignore `vendor`, `.env`).

### **Step 3: Core Features**
   See GitHub issues

### **Step 4: Testing and Deployment**
1. **Testing**  
   - Test features like:
    - Ad creation, role-based access, and analytics.
    - Validate forms and ensure security measures (e.g., CSRF, SQL injection prevention).

2. **Deployment**  
   - Deploy the app to a server (e.g., AWS, DigitalOcean, or Laravel Forge).
   - Set up a CI/CD pipeline for seamless updates.

### **Step 5: Enhancements**
1. **Add Analytics**  
   - Chart libraries (e.g., Chart.js, Apex Charts) for visualizing ad performance.
   
2. **Integrate JavaScript Interactivity**  
   - Custom interactivity using vanilla JavaScript and jQuery.

3. **Mobile Responsiveness**  
   - Ensure the app is fully responsive on different devices like desktop, mobile and tablets etc.

## Schemas Creation
A detailed explanation of the fully functional database using MySQL Server, including schemas, ERD, and relationships.

### **Step 1: Requirements Analysis and Defining Data Entities**
From IAMS requirements, the following are the key entities that might be required:

1. **Users**: Admins, advertisers, and viewers.  
2. **Advertisements**: Information about each ad.  
3. **Categories**: To group advertisements.  
4. **Analytics**: Tracking performance (clicks, views, etc.).  
5. **Payments (Optional)**: For subscription-based services.

### **Step 2: ERD (Entity-Relationship Diagram) Design**
An **ERD** visually represents database structure. The tool for creating ERD is: **MySQL Workbench**. Visit [ERD creation](#erd-creation) for creation on ERD.

1. **Users Table**
   - Fields: `id`, `name`, `email`, `password`, `role`, `created_at`, `updated_at`.

2. **Advertisements Table**
   - Fields: `id`, `user_id` (FK), `title`, `description`, `media_url`, `status`, `created_at`, `updated_at`.

3. **Categories Table**
   - Fields: `id`, `name`, `description`, `created_at`, `updated_at`.

4. **Ad Performance Table**
   - Fields: `id`, `ad_id` (FK), `views`, `clicks`, `created_at`, `updated_at`.

5. **Payments Table (Optional)**  
   - Fields: `id`, `user_id` (FK), `amount`, `payment_date`, `status`.

### **Step 3: Relationships**
1. **Users and Advertisements**  
   - **1-to-Many**: A user can create multiple advertisements.  
   - **Key**: `user_id` in the `advertisements` table is a **foreign key**.

2. **Advertisements and Categories**  
   - **Many-to-One**: An advertisement belongs to one category.  
   - **Key**: `category_id` in the `advertisements` table is a **foreign key**.

3. **Advertisements and Ad Performance**  
   - **1-to-1**: Each ad has one performance record.  
   - **Key**: `ad_id` in the `ad_performance` table is a **foreign key**.

4. **Users and Payments**  
   - **1-to-Many**: A user can have multiple payments.  
   - **Key**: `user_id` in the `payments` table is a **foreign key**.

### **Step 4: Database Schemas**
1. Open **MySQL Workbench** or your preferred MySQL client.
2. Run the following SQL commands:

#### **Create Database**
```sql
CREATE DATABASE iams;
USE iams;
```

#### **Create Tables**
##### Users Table
```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    role ENUM('admin', 'advertiser', 'viewer') DEFAULT 'viewer',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

##### Categories Table
```sql
CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

##### Advertisements Table
```sql
CREATE TABLE advertisements (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    category_id INT NOT NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    media_url VARCHAR(255),
    status ENUM('active', 'inactive', 'archived') DEFAULT 'active',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (category_id) REFERENCES categories(id)
);
```

##### Ad Performance Table
```sql
CREATE TABLE ad_performance (
    id INT AUTO_INCREMENT PRIMARY KEY,
    ad_id INT NOT NULL,
    views INT DEFAULT 0,
    clicks INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (ad_id) REFERENCES advertisements(id)
);
```

##### Payments Table (Optional)
```sql
CREATE TABLE payments (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    payment_date DATE NOT NULL,
    status ENUM('pending', 'completed', 'failed') DEFAULT 'pending',
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### **Step 5: Database Testing**
1. **Insert Sample Data**
   ```sql
   INSERT INTO users (name, email, password, role) 
   VALUES ('John Doe', 'john@example.com', 'hashedpassword', 'advertiser');
   
   INSERT INTO categories (name, description) 
   VALUES ('Electronics', 'Ads for electronics products');
   
   INSERT INTO advertisements (user_id, category_id, title, description, media_url) 
   VALUES (1, 1, 'Smartphone Sale', 'Discount on latest smartphones.', 'example.com/smartphone.jpg');
   ```

2. **Run Queries to Check Relationships**
   ```sql
   SELECT * FROM advertisements
   INNER JOIN users ON advertisements.user_id = users.id
   INNER JOIN categories ON advertisements.category_id = categories.id;
   ```

### **Step 6: Database Schema Maintanance**
1. Laravel **migrations** to manage schema changes.  
   Example for Users Table:
   ```bash
   php artisan make:migration create_users_table
   ```

2. Run migrations:
   ```bash
   php artisan migrate
   ```

### **Step 7: Generate ERD**
While using Laravel, we'll generate an ERD using packages like **Laravel ER Diagram Generator**.

```bash
composer require beyondcode/laravel-er-diagram-generator
php artisan generate:erd
```

This will provide a visual representation of your database.

## ERD Creation
Creating an Entity-Relationship Diagram (ERD) from project requirements involves several stages. Here's a detailed step-by-step guide to help you design an ERD for your **Integrated Advertisement Management System (IAMS)**.

### **Step 1: Understand Requirements**
Start by thoroughly analyzing the project requirements and identifying the key entities and their relationships.  

#### Example Requirements for IAMS:
- **Users**: Admin, Advertiser, Viewer. Users can create advertisements and make payments.  
- **Advertisements**: Each ad has a title, description, media, and status.  
- **Categories**: Ads are grouped into categories.  
- **Performance Metrics**: Track clicks and views for ads.  
- **Payments**: Track payments made by users.

### **Step 2: Identify Entities**
Entities represent tables in your database. Think of them as the "nouns" in your requirements.

#### Identified Entities for IAMS:
1. **Users**: Stores user details.  
2. **Advertisements**: Stores ad details.  
3. **Categories**: Stores category information.  
4. **Ad Performance**: Tracks metrics like clicks and views.  
5. **Payments**: Stores payment records.

### **Step 3: Define Attributes for Each Entity**
Each entity will have attributes (columns). Focus on the **primary key (PK)** and other fields.

#### Examples:
- **Users**
  - PK: `id`
  - Attributes: `name`, `email`, `password`, `role`, `created_at`, `updated_at`.

- **Advertisements**
  - PK: `id`
  - Attributes: `user_id` (FK), `category_id` (FK), `title`, `description`, `media_url`, `status`, `created_at`, `updated_at`.

- **Categories**
  - PK: `id`
  - Attributes: `name`, `description`, `created_at`, `updated_at`.

- **Ad Performance**
  - PK: `id`
  - Attributes: `ad_id` (FK), `views`, `clicks`, `created_at`, `updated_at`.

- **Payments**
  - PK: `id`
  - Attributes: `user_id` (FK), `amount`, `payment_date`, `status`.

### **Step 4: Define Relationships**
Establish relationships between entities. Relationships are based on how entities interact.

#### Types of Relationships:
1. **One-to-Many (1:M)**: A user can create many ads, but each ad belongs to one user.  
2. **Many-to-One (M:1)**: An ad belongs to one category, but a category can have many ads.  
3. **One-to-One (1:1)**: An ad has one performance record.  
4. **Many-to-Many (M:N)**: (Optional) If users have multiple roles.

#### Example Relationships for IAMS:
- A **user** can create multiple **advertisements**.  
- An **advertisement** belongs to one **category**.  
- Each **advertisement** has one **performance record**.  
- A **user** can make multiple **payments**.

### **Step 5: Draw the ERD**
Use tools like **MySQL Workbench**, **Lucidchart**, or **dbdiagram.io** to visually create the ERD.

#### Example Steps with MySQL Workbench:
1. Open **MySQL Workbench**.
2. Create a new EER (Enhanced Entity-Relationship) Diagram:
   - Go to **File > New Model**.
   - Select **Add Diagram** to create a new ERD canvas.
3. Add Entities:
   - Click on the table icon and add tables for `users`, `advertisements`, `categories`, `ad_performance`, and `payments`.
   - Define attributes for each table and mark the primary key (PK).
4. Establish Relationships:
   - Use the "Place Relationship" tool to connect tables:
     - **Users -> Advertisements** (1:M).
     - **Advertisements -> Categories** (M:1).
     - **Advertisements -> Ad Performance** (1:1).
     - **Users -> Payments** (1:M).

#### Example ERD Diagram:
- **Users** (PK: id) → **Advertisements** (FK: user_id).
- **Advertisements** (PK: id) → **Categories** (FK: category_id).
- **Advertisements** (PK: id) → **Ad Performance** (FK: ad_id).
- **Users** (PK: id) → **Payments** (FK: user_id).

### **Step 6: Add Constraints**
For each relationship, specify the following:
1. **Foreign Key (FK)**: Connect entities (e.g., `user_id` in advertisements references `id` in users).
2. **Cardinality**: Define the number of relationships (e.g., 1 user can create many ads, but each ad belongs to 1 user).

#### Example Constraints:
- **Users → Advertisements**
  - FK: `user_id` references `users.id`.
  - Cardinality: One-to-Many.
- **Advertisements → Categories**
  - FK: `category_id` references `categories.id`.
  - Cardinality: Many-to-One.

### **Step 7: Review and Validate**
1. Ensure every entity has a primary key.
2. Verify relationships align with project requirements.
3. Check for normalization (avoid redundant data).

### **Step 8: Export ERD to SQL (Optional)**
If using a tool like MySQL Workbench, export the ERD as SQL:
1. Go to **File > Export > Forward Engineer SQL CREATE Script**.
2. Save the generated SQL file and execute it in your MySQL Server.

### **Step 9: Update as Needed**
As your project evolves, update the ERD to reflect any new requirements or relationships.
