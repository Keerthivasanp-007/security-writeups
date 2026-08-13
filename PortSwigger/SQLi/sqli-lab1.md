# PortSwigger — SQL Injection Lab 1: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

**Date:** Aug 13 2026
**Category:** SQL Injection

## Objective
The app filters products by category via a URL parameter. The goal is to bypass this filter to reveal hidden/unreleased products that shouldn't normally be visible.

## Recon
Noticed the category filter was reflected directly in the URL (`?category=Gifts`), suggesting the value was likely being inserted straight into a SQL query without sanitization.

## Approach
Modified the category parameter directly in the URL instead of using the UI, injecting a payload to break out of the intended query structure.

## Payload
' OR 1=1--

## Why It Worked
The app's query likely looked like:
`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

The payload closes the string early with `'`, adds `OR 1=1` to make the WHERE clause always true (matching every row), and uses `--` to comment out the rest of the original query — including the `released = 1` check that was hiding certain products.

## Result
All products were returned, including ones that were meant to stay hidden/unreleased.

## Takeaway
User input was concatenated directly into a SQL query instead of using parameterized queries/prepared statements — the classic root cause of SQLi. Proper fix: never build queries via string concatenation with user input.