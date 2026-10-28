# Security Policy

MyNotes is a collection of Markdown lessons, notes, and exercise files for educational purposes. It is not a deployed application and does not collect user data, but security still matters here: lessons should teach safe practices, and the repository should never contain harmful code or exposed secrets.

---

### Supported Versions

This repository has no versioned releases. Security fixes are applied to the current `main` branch.

### Scope

Please report issues such as:
- **Exposed secrets** — API keys, passwords, tokens, or private credentials committed in lessons, exercises, or history
- **Malicious or unsafe code** — Exercise files or snippets that run harmful commands, hide payloads, or could damage a learner's system when executed
- **Dangerous links** — Links in lessons, the README, or RESOURCES.md that point to malware, phishing pages, or compromised sites
- **Insecure teaching** — Lessons that present insecure practices (for example, hard-coded credentials or SQL built from raw user input) as recommended or correct
- **Personal information** — Private data about any individual included in the repository
- **Compromised external assets** — Image or icon sources used in the README that have been taken over or altered

Ordinary mistakes such as typos, broken links, or incorrect explanations are not security issues and should be reported through a regular GitHub issue.

### Reporting a Vulnerability

For anything sensitive, such as an exposed credential or personal information, **please do not open a public issue**. Instead:

- Use GitHub's **private vulnerability reporting** under the repository's *Security* tab, if enabled, or
- Email the maintainer at **[kelsengile.dev@gmail.com]**

Please include:
- A description of the problem and where it is located (file and line, if possible)
- Why it is a concern
- Any suggested fix

For low-risk concerns, such as an insecure example in a lesson, a public issue or pull request is fine.

### What to Expect

1. **Acknowledgment** — Reports are acknowledged within a few days.
2. **Assessment** — The issue is reviewed to confirm its scope and severity.
3. **Resolution** — The content is corrected or removed. Exposed credentials should also be revoked or rotated by their owner.
4. **Credit** — You will be credited for the report if you wish.

### Guidelines for Contributors

- Never commit secrets, API keys, tokens, or real personal data. Use clearly fake placeholder values in examples.
- Test code snippets and exercise files before submitting, and make sure they do nothing harmful.
- Lessons on security topics (such as those in `Cybersecurity`) must stay educational and defensive in focus. Do not submit working malware or step-by-step guides for attacking systems you do not own.
- Cite and verify external links, and prefer reputable sources.
- If you accidentally commit a secret, report it right away so it can be removed and revoked. Deleting it in a later commit is not enough, because it remains in the Git history.

### Responsible Disclosure

Please allow reasonable time for a fix before sharing a problem publicly, and act in good faith. Researchers who follow this policy will not face action from the maintainer.

---

*Thanks for helping keep this resource safe and trustworthy.*



==========================================================================
LABORATORY 9 - BUILDING A BASIC XAMPP WEB SERVER WITH A BROWSER INPUT FORM
ALL CODE IN ONE FILE  (original concept: "Gadget Inventory")
==========================================================================
Your instructor said the sample is a guide only, so this uses its own
concept (gadget inventory) with the same structure and safety practices.

FOLDER:  C:\xampp\htdocs\lab_xampp\
FILES:   test.php, config.php, form.php, save.php, records.php
Save each file with the exact name shown, using a code editor
(VS Code / Notepad++), NOT MS Word (curly quotes break PHP).

NOTE: Default MySQL port in XAMPP is 3306. The sample used 3307 - only
use 3307 if your own MySQL is really running on that port (check the
XAMPP Control Panel). Also the sample's index.php required "db.php" but
the connection file was named config.php - here every page uses
config.php so the names match.


##########################################################################
PART B - DATABASE  (phpMyAdmin > SQL tab > paste > Go)
http://localhost/phpmyadmin/
##########################################################################

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


##########################################################################
PROBLEM 2 - TEST PHP
File: lab_xampp/test.php
URL:  http://localhost/lab_xampp/test.php
##########################################################################

<?php
echo "<h1>My XAMPP server is working!</h1>";
echo "<p>Server time: " . date("Y-m-d H:i:s") . "</p>";
echo "<p>PHP version: " . phpversion() . "</p>";


##########################################################################
PROBLEM 4 - DATABASE CONNECTION FILE
File: lab_xampp/config.php
##########################################################################

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


##########################################################################
PROBLEM 5 - THE INPUT FORM
File: lab_xampp/form.php
URL:  http://localhost/lab_xampp/form.php
##########################################################################

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


##########################################################################
PROBLEM 6 - SAVE THE FORM DATA (validation + prepared statement)
File: lab_xampp/save.php
##########################################################################

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


##########################################################################
PROBLEM 7 - DISPLAY THE RECORDS
File: lab_xampp/records.php
URL:  http://localhost/lab_xampp/records.php
##########################################################################

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


##########################################################################
PROBLEM 8 - TEST INPUTS (type these into form.php)
##########################################################################

Record 1:  Bluetooth Speaker | Audio | 15 | 1250.00
Record 2:  Webcam 1080p      | Peripherals | 8 | 980.50
Record 3:  Power Bank 10000  | Power | 20 | 699.00

Harmful-input test (should display as plain text, NOT run):
  Gadget Name:  <script>alert('XSS')</script>
  Category:     Test'); DROP TABLE gadgets;--
  -> The page must show the text as-is, and the table must still exist.


##########################################################################
ANSWERS TO THE EXPLAIN TASKS (put in your own words)
##########################################################################

Problem 2 (why does the time change?):
  PHP runs on the server every time the page is requested, and
  date() returns the current time, so each refresh generates new output
  even though the file was not edited.

Problem 4 (why one config.php with require?):
  The connection settings are written once, so a change (password, port,
  database name) is made in one place instead of every page. It also
  reduces typing mistakes and keeps pages shorter and consistent.

Problem 7 (safe display function):
  htmlspecialchars() converts characters like < > " ' & into HTML
  entities so they show as text instead of running as code. It helps
  prevent Cross-Site Scripting (XSS) attacks.
  (Prepared statements, used in save.php, prevent SQL injection.)

##########################################################################
RUN ORDER
##########################################################################
1. Start Apache and MySQL in XAMPP Control Panel.
2. Run the SQL in phpMyAdmin.
3. Create the 5 files in C:\xampp\htdocs\lab_xampp\
4. Open http://localhost/lab_xampp/test.php
5. Open http://localhost/lab_xampp/form.php, add 3 records
6. Open http://localhost/lab_xampp/records.php to see them