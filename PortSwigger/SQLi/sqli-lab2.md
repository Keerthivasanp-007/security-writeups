# PortSwigger — Lab: SQL injection vulnerability allowing login bypass

**Category:** SQL Injection

## Objective
Log in as administrator without knowing the password.

## Reacon
The login form likely uses a query like:
SELECT * FROM users WHERE username = '...' AND password = '...'

## Approach
Remembered that we can comment down the upcoming password query directly while typing in the login username

## Payload
administrator'--

## Why It Worked
SELECT * FROM users WHERE username = 'administrator'--' AND password = 'anything'

The query takes in username and comments down password query where it doesn't matter what password we type

## Result
Gain access to the administrator account

## Takeaway
A simple SQL comment can completely bypass authentication if input is not sanitised.
