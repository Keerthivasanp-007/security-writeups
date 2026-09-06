# PortSwigger — SQL injection UNION attack, finding a column containing text

**Category:** SQL Injection

## Objective
Find out which column can actually hold a string, so later I can use it to display real data.

## Recon
Same category param as before, url looks like ?category=Gifts

## Approach
First had to find column count again by trying UNION with different numbers of NULLs - wrong count gave me internal server error. Once i got the right count, tried putting a random string 'jSbAOY' in place of NULL one at a time starting from column 1, till one didn't error out.

## Payload
' UNION SELECT NULL,'jSbAOY',NULL--

## Why It Worked
NULL works everywhere no matter what type the column is, but a string only works if that column is actually text type. So if i put a string in a number column it breaks. 2nd column didn't break so thats the text one.

## Result
'jSbAOY' showed up on the page, so column 2 takes strings.

## Takeaway
This app just shows Internal Server Error for everything wrong, doesn't tell you the actual SQL error like some apps do. Kinda annoying to debug without Burp probably, since browser doesn't show you the full response.
