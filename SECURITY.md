# Laboratory 9: Building a Basic XAMPP Web Server with a Browser Input Form

**All code in one file** · Original concept: *Gadget Inventory*

> Your instructor said the sample is a guide only, so this uses its own concept (gadget inventory) with the same structure and safety practices.

---

## Contents

1. [Setup notes](#setup-notes)
2. [Part B: Database (SQL)](#part-b-database-sql)
3. [Problem 2: `test.php`](#problem-2-testphp)
4. [Problem 4: `config.php`](#problem-4-configphp)
5. [Problem 5: `form.php`](#problem-5-formphp)
6. [Problem 6: `save.php`](#problem-6-savephp)
7. [Problem 7: `records.php`](#problem-7-recordsphp)
8. [Problem 8: Test inputs](#problem-8-test-inputs)
9. [Answers to the explain tasks](#answers-to-the-explain-tasks)
10. [Run order](#run-order)

---

## Setup notes

**Project folder:** `C:\xampp\htdocs\lab_xampp\`

| File | Purpose |
|---|---|
| `test.php` | Confirms PHP is working |
| `config.php` | Database connection (reused by other pages) |
| `form.php` | Browser input form |
| `save.php` | Validates and saves form data |
| `records.php` | Displays saved records |

- Use a **code editor** (VS Code / Notepad++), **not MS Word**. Word replaces quotes with curly quotes that break PHP.
- Save each file with the **exact name** shown.
- The default MySQL port in XAMPP is **3306**. The sample used 3307; only use 3307 if your MySQL really runs on that port (check the XAMPP Control Panel).
- The sample's `index.php` loaded `db.php` while the connection file was named `config.php`. Here every page uses `config.php` so the names match.

---

## Part B: Database (SQL)

Open `http://localhost/phpmyadmin/`, click the **SQL** tab, paste the code, then click **Go**.

```sql
-- GADGET INVENTORY LAB
CREATE DATABASE IF NOT EXISTS gadget_inventory_lab
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;

USE gadget_inventory_lab;

CREATE TABLE IF NOT EXISTS gadgets (
  id INT AUTO_INCREMENT PRIMARY KEY,
  gadget_name VARCHAR(80) NOT NULL,
  category VARCHAR(50) NOT NULL,
  quantity INT NOT NULL DEFAULT 0,
  price DECIMAL(10,2) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Optional starter rows (run once only)
INSERT INTO gadgets (gadget_name, category, quantity, price) VALUES
('Wireless Mouse', 'Accessories', 25, 499.00),
('Mechanical Keyboard', 'Accessories', 12, 1899.50),
('USB-C Hub', 'Adapters', 30, 750.00);

-- Verify the records
SELECT * FROM gadgets;
```

---

## Problem 2: `test.php`

**File:** `lab_xampp/test.php`
**URL:** `http://localhost/lab_xampp/test.php`

```php
<?php
echo "<h1>My XAMPP server is working!</h1>";
echo "<p>Server time: " . date("Y-m-d H:i:s") . "</p>";
echo "<p>PHP version: " . phpversion() . "</p>";
```

---

## Problem 4: `config.php`

**File:** `lab_xampp/config.php`

```php
<?php
// Database connection for local XAMPP practice.
$host     = '127.0.0.1';
$port     = 3306;               // change to 3307 only if your MySQL uses it
$dbname   = 'gadget_inventory_lab';
$username = 'root';
$password = '';                 // default XAMPP setting

$dsn = "mysql:host={$host};port={$port};dbname={$dbname};charset=utf8mb4";

$options = [
    PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
    PDO::ATTR_EMULATE_PREPARES   => false,
];

try {
    $pdo = new PDO($dsn, $username, $password, $options);
} catch (PDOException $e) {
    // Technical details go to the server log, not to the visitor.
    error_log('Gadget DB connection failed: ' . $e->getMessage());
    http_response_code(500);
    exit('Database connection failed. Check that MySQL is running '
       . 'and that config.php contains the correct settings.');
}
```

---

## Problem 5: `form.php`

**File:** `lab_xampp/form.php`
**URL:** `http://localhost/lab_xampp/form.php`

```php
<?php
// Fixed messages only (never echo raw text from the URL).
$messages = [
    'invalid' => 'Please enter valid information in every field.',
    'error'   => 'Something went wrong. Please try again.',
];
$error = $messages[$_GET['error'] ?? ''] ?? '';
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Add Gadget</title>
    <style>
        body, input, button { font-family: "Segoe UI", Arial, sans-serif; }
        .error { color: #b00020; }
    </style>
</head>
<body>
    <h2>Gadget Inventory</h2>
    <h3>Add Gadget</h3>

    <?php if ($error !== ''): ?>
        <p class="error"><?= htmlspecialchars($error) ?></p>
    <?php endif; ?>

    <form method="post" action="save.php">
        Gadget Name:
        <input type="text" name="gadget_name" maxlength="80" required>
        <br><br>

        Category:
        <input type="text" name="category" maxlength="50" required>
        <br><br>

        Quantity:
        <input type="number" name="quantity" min="0" step="1" required>
        <br><br>

        Price (PHP):
        <input type="number" name="price" min="0" step="0.01" required>
        <br><br>

        <button type="submit" name="save">Save Gadget</button>
    </form>

    <p><a href="records.php">View all records</a></p>
</body>
</html>
```

---

## Problem 6: `save.php`

**File:** `lab_xampp/save.php`

Validation plus a prepared statement.

```php
<?php
require 'config.php';

// Only accept form submissions.
if ($_SERVER['REQUEST_METHOD'] !== 'POST' || !isset($_POST['save'])) {
    header('Location: form.php');
    exit;
}

$name     = trim($_POST['gadget_name'] ?? '');
$category = trim($_POST['category'] ?? '');
$quantity = filter_var($_POST['quantity'] ?? '', FILTER_VALIDATE_INT,
                       ['options' => ['min_range' => 0]]);
$price    = filter_var($_POST['price'] ?? '', FILTER_VALIDATE_FLOAT);

// Server-side validation (never trust the browser).
if ($name === '' || $category === ''
    || mb_strlen($name) > 80 || mb_strlen($category) > 50
    || $quantity === false
    || $price === false || $price < 0) {
    header('Location: form.php?error=invalid');
    exit;
}

try {
    // Prepared statement: input is treated as data, not SQL.
    $stmt = $pdo->prepare(
        "INSERT INTO gadgets (gadget_name, category, quantity, price)
         VALUES (?, ?, ?, ?)"
    );
    $stmt->execute([$name, $category, $quantity, $price]);
} catch (PDOException $e) {
    error_log('Insert failed: ' . $e->getMessage());
    header('Location: form.php?error=error');
    exit;
}

header('Location: records.php?saved=1');
exit;
```

---

## Problem 7: `records.php`

**File:** `lab_xampp/records.php`
**URL:** `http://localhost/lab_xampp/records.php`

```php
<?php
require 'config.php';

$gadgets = $pdo->query(
    "SELECT * FROM gadgets ORDER BY id"
)->fetchAll();
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Gadget Records</title>
    <style>
        body, table { font-family: "Segoe UI", Arial, sans-serif; }
        .ok { color: #1b5e20; }
    </style>
</head>
<body>
    <h2>Gadget Records</h2>

    <?php if (isset($_GET['saved'])): ?>
        <p class="ok">Gadget saved successfully.</p>
    <?php endif; ?>

    <p><a href="form.php">+ Add another gadget</a></p>

    <table border="1" cellpadding="8">
        <tr>
            <th>ID</th>
            <th>Gadget Name</th>
            <th>Category</th>
            <th>Quantity</th>
            <th>Price (PHP)</th>
            <th>Date Added</th>
        </tr>

        <?php foreach ($gadgets as $g): ?>
        <tr>
            <td><?= (int) $g['id'] ?></td>
            <td><?= htmlspecialchars($g['gadget_name']) ?></td>
            <td><?= htmlspecialchars($g['category']) ?></td>
            <td><?= (int) $g['quantity'] ?></td>
            <td><?= number_format((float) $g['price'], 2) ?></td>
            <td><?= htmlspecialchars($g['created_at']) ?></td>
        </tr>
        <?php endforeach; ?>
    </table>
</body>
</html>
```

---

## Problem 8: Test inputs

Type these into `form.php`:

| # | Gadget Name | Category | Quantity | Price |
|---|---|---|---|---|
| 1 | Bluetooth Speaker | Audio | 15 | 1250.00 |
| 2 | Webcam 1080p | Peripherals | 8 | 980.50 |
| 3 | Power Bank 10000 | Power | 20 | 699.00 |

**Harmful-input test.** It should display as plain text and **not** run:

- **Gadget Name:** `<script>alert('XSS')</script>`
- **Category:** `Test'); DROP TABLE gadgets;--`

The page must show the text as-is, and the `gadgets` table must still exist.

---

## Answers to the explain tasks

*(Put these in your own words.)*

**Problem 2: Why does the time change?**
PHP runs on the server every time the page is requested, and `date()` returns the current time, so each refresh generates new output even though the file was not edited.

**Problem 4: Why one `config.php` loaded with `require`?**
The connection settings are written once, so a change (password, port, database name) is made in one place instead of every page. It also reduces typing mistakes and keeps pages shorter and consistent.

**Problem 7: Which function displays values safely?**
`htmlspecialchars()` converts characters like `<`, `>`, `"`, `'` and `&` into HTML entities so they show as text instead of running as code. It helps prevent **Cross-Site Scripting (XSS)** attacks. (Prepared statements, used in `save.php`, prevent **SQL injection**.)

---

## Run order

1. Start **Apache** and **MySQL** in the XAMPP Control Panel.
2. Run the SQL in phpMyAdmin.
3. Create the 5 files in `C:\xampp\htdocs\lab_xampp\`.
4. Open `http://localhost/lab_xampp/test.php`.
5. Open `http://localhost/lab_xampp/form.php` and add 3 records.
6. Open `http://localhost/lab_xampp/records.php` to see them.
