# PortSwigger — Lab: SQL injection UNION attack, determining the number of columns returned by the query

**Category:** SQL Injection

## Objective
Determine how many columns are returned by the original SQL query, as a prerequisite for a full UNION-based SQLi attack.

## Recon
Injectable parameter is the `category` URL parameter on a product category page (e.g. `?category=Gifts`). The main/home page doesn't expose this parameter, so the injection point only exists once inside a specific category.

## Approach
First tried the ORDER BY technique (`' ORDER BY 1--`, incrementing the number) to find the column count via a database error message. This didn't work — the app appears to return the same generic response regardless of whether the ORDER BY index was valid or out of range, so there was no visible difference to detect.

Switched to the UNION SELECT technique instead — injecting a UNION query with a guessed number of NULL values and increasing the count until the page loaded successfully instead of erroring.

## Payload
' UNION SELECT NULL,NULL,NULL--

## Why It Worked
A UNION query only succeeds if the number of columns matches the original SELECT statement exactly. Using NULL for each column sidesteps data-type mismatches (NULL is valid in any column type), so the only thing being tested is column count. 3 NULLs returned a valid page with no error, confirming the original query returns exactly 3 columns.

## Result
Confirmed the query returns 3 columns, which sets up the next stage of the UNION attack (injecting actual data into one of those 3 columns to extract information from other tables).

## Takeaway
When ORDER BY doesn't reveal column count (because the app suppresses detailed DB errors), UNION SELECT with NULLs is a reliable fallback — it works purely on structural success/failure of the query, not on error message content. Also: on databases like Oracle, UNION SELECT syntax requires a FROM DUAL clause since Oracle doesn't allow SELECT without FROM.
