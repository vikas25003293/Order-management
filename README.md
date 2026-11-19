# Order Management System (Java + JDBC + MySQL)

## 1. Project introduction

A console-based, menu-driven Order Management System. It connects Java to a MySQL database through JDBC and supports these operations on an `Orders` table:

1. Insert three orders
2. Search orders by status (only quantity > 2, highest unit price first)
3. Increase the quantity of an existing order
4. Update an order's status (Pending / Shipped / Delivered)
5. Display total order value
6. Delete a Cancelled order

Database: `ecommerce_db` | Table: `Orders`

| Column        | Type         |
|---------------|--------------|
| order_id      | INT (Primary Key) |
| customer_name | VARCHAR(100) |
| product_name  | VARCHAR(100) |
| quantity      | INT          |
| status        | VARCHAR(30)  |
| unit_price    | DOUBLE       |

`unit_price` was not in the question's attribute list, but operations (b) and (e) need it, so it was added.

## 2. Software requirements

- IntelliJ IDEA (Community or Ultimate)
- JDK 17 or newer
- MySQL Server 8.x and MySQL Workbench
- Maven (bundled with IntelliJ, nothing to install)
- Internet connection the first time, so Maven can download MySQL Connector/J

## 3. Java 17 setup

1. In IntelliJ: File > Project Structure > Project.
2. Set SDK to a JDK 17+ (use "Add SDK > Download JDK" if none is listed, e.g. Temurin 17).
3. Set Language level to 17.

## 4. MySQL installation requirement

Install MySQL Server and MySQL Workbench from the official MySQL installer. Remember the root password you set during installation, because you need it in step 8. Make sure the MySQL service is running (default port 3306).

## 5. Open the project in IntelliJ IDEA

1. Unzip `OrderManagementSystem.zip`.
2. In IntelliJ: File > Open, select the `OrderManagementSystem` folder (the one containing `pom.xml`), click OK.
3. If asked, choose "Trust Project" and "Open as Project".

## 6. Create the database using MySQL Workbench

1. Open MySQL Workbench and connect to your local instance (root).
2. You do not need to create anything by hand: the script in step 7 creates the database.

## 7. Execute `ecommerce_db.sql`

1. In Workbench: File > Open SQL Script, choose `database/ecommerce_db.sql`.
2. Click the lightning-bolt Execute button.
3. The last line shows 8 sample orders (IDs 101-108) in the result grid.

The script drops and recreates the `Orders` table each time, so you can re-run it to reset the data.

## 8. Configure MySQL username and password

1. In IntelliJ's Project panel go to `src/main/resources/`.
2. Copy `db.properties.example` and rename the copy to `db.properties` (same folder).
3. Open `db.properties` and replace `YOUR_MYSQL_PASSWORD` with your real MySQL root password:

```
db.url=jdbc:mysql://localhost:3306/ecommerce_db
db.username=root
db.password=YOUR_MYSQL_PASSWORD
```

`db.properties` is git-ignored and is not included in the ZIP, so no real password is shared.

## 9. Reload Maven dependencies

Right-click `pom.xml` > Maven > Reload project (or click the small Maven refresh icon). Wait for the progress bar at the bottom to finish. The `mysql-connector-j` library should appear under External Libraries.

## 10. Run `Main.java`

Open `src/main/java/com/ecommerce/Main.java` and click the green arrow next to `public static void main`, then choose Run 'Main.main()'. You should see "Connected to MySQL successfully!" followed by the menu.

## 11. How to test each menu option

Sample data uses IDs 101-108, so use IDs 1, 2, 3 for the insertion demo.

| Option | What to try | Expected |
|--------|-------------|----------|
| 1 | Insert ID 1, 2, 3 with any details. Then try ID 101 as one of them. | "Order 1 inserted successfully!" ...; for 101: "Order ID already exists! Please enter another ID." and it asks again |
| 2 | Status `Pending` | Orders 101, 103, 105 (quantity > 2), sorted by price: 101 (55000), 105 (18999), 103 (1299) plus any new ones you added |
| 2 | Status `Cancelled` | "No matching orders found." (their quantities are 2) |
| 3 | ID 102, additional 5 | "Order quantity updated successfully!" |
| 3 | ID 999, additional 5 | "Order ID not found!" |
| 3 | ID 102, additional 0 or -3 | Rejected (must be greater than zero) |
| 4 | ID 101, status `Shipped` | Shows current record, updates, shows updated record |
| 4 | ID 101, status `Lost` | "Invalid status! Allowed values: Pending, Shipped, Delivered." |
| 4 | ID 999 | "Order not found!" |
| 5 | (none) | Total Order Value printed |
| 6 | ID 104 | "Cancelled order deleted successfully!" |
| 6 | ID 103 (Pending) | "Cannot delete order! Only Cancelled orders can be deleted." |
| 6 | ID 999 | "Order ID not found!" |
| 0 | (none) | Goodbye message, program ends |
| any other number or text | `9`, `abc` | "Invalid choice!" and the menu appears again |

## 12. Common errors and solutions

| Message | Cause and fix |
|---------|---------------|
| "Configuration file db.properties was not found." | You did not create `db.properties`. Do step 8. |
| "Please replace YOUR_MYSQL_PASSWORD..." | The password placeholder is still there. Enter your real password. |
| "Incorrect MySQL username or password." | Wrong `db.username` or `db.password`. Test the same login in Workbench. |
| "Database 'ecommerce_db' does not exist." | Run `database/ecommerce_db.sql` (step 7). |
| "Cannot reach MySQL server." | MySQL service is not running, or the port is not 3306. Start the service or fix `db.url`. |
| `No suitable driver found` / red `com.mysql` in pom | Maven did not download the driver. Check internet, then step 9. |
| `release version 17 not supported` | Project SDK is below 17. Redo section 3. |
| Order ID already exists | Primary keys must be unique. Enter another ID (sample data uses 101-108). |

## Project structure

```
OrderManagementSystem/
├── pom.xml
├── database/ecommerce_db.sql
├── src/main/java/com/ecommerce/{Main, DBConnection, OrderManagement}.java
├── src/main/resources/db.properties.example
├── README.md
└── .gitignore
```
