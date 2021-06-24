# Rules

Here you will find rules to import statements from Greek banks to hledger

Banks:
* Alpha
* Winbank(Peiraios)

## What is hledger

[hledger](https://github.com/simonmichael/hledger) is a computer program for
easily tracking money, time, or other commodities, on unix, mac and windows
(and web-capable mobile devices, to some extent).

## CSV import

hledger has a powerful CSV converter built in. After saving a few declarations
in a "CSV rules file", it can read transactions from almost any CSV file. This
is described in detail in the [hledger manual](https://hledger.org/hledger.html#csv-format), but here are some quick examples.

Say you have downloaded this `checking.csv` file from a bank for the first time:
```csv
"Date","Note","Amount"
"2012/3/22","DEPOSIT","50.00"
"2012/3/23","TRANSFER TO SAVINGS","-10.00"
```

Create a rules file named `checking.csv.rules` in the same directory.
This tells hledger how to read this CSV file. Eg:
```rules
# skip the headings line:
skip 1

# use the first three CSV fields for hledger's transaction date, description and amount:
fields date, description, amount

# specify the date field's format - not needed here since date is Y/M/D
# date-format %-d/%-m/%Y
# date-format %-m/%-d/%Y
# date-format %Y-%h-%d

# since the CSV amounts have no currency symbol, add one:
currency $

# set the base account that this CSV file corresponds to
account1 assets:bank:checking

# the other account will default to expenses:unknown or income:unknown;
# we can optionally refine it by matching patterns in the CSV record:
if (TO|FROM) SAVINGS
  account2 assets:bank:savings

if WHOLE FOODS
  account2 expenses:food
```

You can [print] the resulting transactions in any of hledger's [output formats]:
```shell
$ hledger -f checking.csv print
2012-03-22 DEPOSIT
    assets:bank:checking          $50.00
    income:unknown               $-50.00

2012-03-23 TRANSFER TO SAVINGS
    assets:bank:checking         $-10.00
    assets:bank:savings           $10.00

```
