-- Create database schema
CREATE SCHEMA finance_db;

-- Use the schema
USE finance_db;

-- Create tables
CREATE TABLE accounts (
    account_id INT PRIMARY KEY,
    account_name VARCHAR(50)
);

CREATE TABLE transactions (
    transaction_id INT PRIMARY KEY AUTO_INCREMENT,
    date DATE,
    account_id INT,
    amount DECIMAL(10, 2),
    transaction_type VARCHAR(10),
    FOREIGN KEY (account_id) REFERENCES accounts(account_id)
);

CREATE TABLE financial_statements (
    statement_id INT PRIMARY KEY AUTO_INCREMENT,
    date DATE,
    account_id INT,
    revenue DECIMAL(10, 2),
    expenses DECIMAL(10, 2),
    assets DECIMAL(10, 2),
    liabilities DECIMAL(10, 2),
    equity DECIMAL(10, 2),
    FOREIGN KEY (account_id) REFERENCES accounts(account_id)
);
-- Insert sample data into accounts table
INSERT INTO accounts (account_id, account_name) VALUES
(1, 'Cash'),
(2, 'Accounts Receivable'),
(3, 'Inventory'),
(4, 'Property, Plant, and Equipment'),
(5, 'Accounts Payable'),
(6, 'Long-term Debt'),
(7, 'Common Stock'),
(8, 'Retained Earnings');

-- Insert sample data into transactions table
INSERT INTO transactions (date, account_id, amount, transaction_type) VALUES
('2024-01-01', 1, 1000.00, 'credit'),
('2024-01-02', 2, 500.00, 'debit'),
('2024-01-03', 3, 200.00, 'credit'),
('2024-01-04', 4, 300.00, 'debit'),
('2024-01-05', 5, 400.00, 'credit');

-- Insert sample data into financial statements table
INSERT INTO financial_statements (date, account_id, revenue, expenses, assets, liabilities, equity) VALUES
('2024-01-01', 1, 10000.00, 7000.00, 15000.00, 5000.00, 10000.00),
('2024-01-01', 2, 12000.00, 8000.00, 16000.00, 6000.00, 11000.00),
('2024-01-01', 3, 13000.00, 9000.00, 17000.00, 7000.00, 12000.00);
-- Total transactions by type
SELECT
    transaction_type,
    SUM(amount) AS total_amount
FROM
    transactions
GROUP BY
    transaction_type;
-- Current balances of all accounts
SELECT
    a.account_name,
    SUM(CASE WHEN t.transaction_type = 'credit' THEN t.amount ELSE 0 END) - 
    SUM(CASE WHEN t.transaction_type = 'debit' THEN t.amount ELSE 0 END) AS balance
FROM
    transactions t
JOIN
    accounts a ON t.account_id = a.account_id
GROUP BY
    a.account_name;
-- Revenue and expenses trend over time
SELECT
    date,
    SUM(revenue) AS total_revenue,
    SUM(expenses) AS total_expenses
FROM
    financial_statements
GROUP BY
    date
ORDER BY
    date;
-- Assets, liabilities, and equity trend over time
SELECT
    date,
    SUM(assets) AS total_assets,
    SUM(liabilities) AS total_liabilities,
    SUM(equity) AS total_equity
FROM
    financial_statements
GROUP BY
    date
ORDER BY
    date;
-- Current Ratio = Current Assets / Current Liabilities
SELECT
    date,
    SUM(CASE WHEN account_id IN (1, 2, 3) THEN assets ELSE 0 END) / 
    SUM(CASE WHEN account_id IN (5) THEN liabilities ELSE 0 END) AS current_ratio
FROM
    financial_statements
GROUP BY
    date;
-- Debt to Equity Ratio = Total Liabilities / Total Equity
SELECT
    date,
    SUM(liabilities) / SUM(equity) AS debt_to_equity_ratio
FROM
    financial_statements
GROUP BY
    date;
-- Profit Margin = (Revenue - Expenses) / Revenue
SELECT
    date,
    (SUM(revenue) - SUM(expenses)) / SUM(revenue) AS profit_margin
FROM
    financial_statements
GROUP BY
    date;
-- Trend analysis of key financial metrics
SELECT
    date,
    SUM(revenue) AS total_revenue,
    SUM(expenses) AS total_expenses,
    SUM(assets) AS total_assets,
    SUM(liabilities) AS total_liabilities,
    SUM(equity) AS total_equity
FROM
    financial_statements
GROUP BY
    date
ORDER BY
    date;
-- Financial performance by account
SELECT
    a.account_name,
    SUM(fs.revenue) AS total_revenue,
    SUM(fs.expenses) AS total_expenses,
    SUM(fs.assets) AS total_assets,
    SUM(fs.liabilities) AS total_liabilities,
    SUM(fs.equity) AS total_equity
FROM
    financial_statements fs
JOIN
    accounts a ON fs.account_id = a.account_id
GROUP BY
    a.account_name
ORDER BY
    total_revenue DESC;
-- Financial performance by account
SELECT
    a.account_name,
    SUM(fs.revenue) AS total_revenue,
    SUM(fs.expenses) AS total_expenses,
    SUM(fs.assets) AS total_assets,
    SUM(fs.liabilities) AS total_liabilities,
    SUM(fs.equity) AS total_equity
FROM
    financial_statements fs
JOIN
    accounts a ON fs.account_id = a.account_id
GROUP BY
    a.account_name
ORDER BY
    total_revenue DESC;
