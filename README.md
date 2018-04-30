# Braathwaate FrontAccounting v2.4

This is a lightly modified fork of the
[Ap.Muthu FrontAccounting v2.4 github repo](https://github.com/apmuthu/frontac24.git,)
which is a current stable version of vanilla FrontAccounting (FA).
I chose to base my modifications off this fork,
but the
[Official GitHub Mirror Repo](https://github.com/FrontAccountingERP/FA/tree/master)
would also have been a good choice.

The "wwh" branch is the main repository.
The "proposed" branches are obsolete pull requests,
but are retained because
these pull requests were not integrated into the FA core.

The braathwaate modifications are mostly user interface enhancements
designed to reduce the amount of clicking and scrolling
when performing accounting tasks,
although there are also bug fixes and feature enhancements.

Following is a list of modifications
between the braathwaate fork (BF) and FA.

## wwh customizations

## Quick entry inactive support

In FA, the list of Quick entries accumulate ad infinitum.

BF supports marking the Quick entry inactive so it does not
show up in the payment list.

## Allow bank account payments on supplier direct invoice
FA restricts payment on a supplier direct invoice
to cash accounts.
For other forms of payments,
one must select "Delayed"
and enter the payment separately.

BF allows any bank account to be used for the payment,
such as a credit card.
## Allow bank accounts in POS
FA restricts payment on a customer direct invoice
to cash accounts,
because the POS setup is restricted to cash accounts.

POS systems often allow direct deposit into bank accounts,
so BF does not restrict the POS "cash sale" to cash accounts
## apportion service items; bug fixes
This mod gives the user the option on a PO to apportion service items (freight, setup charges, discounts, etc) into prices of non-service items on the PO without changing the PO order total. By apportioning these costs into item prices, they are accounted for in inventory and thus accounted for in COGS only when then items are sold. Three methods of apportionment are provided: by item quantity, line item, and line total.

additional bug fixes:

    Viewing order totals on POs with items with decimal precision greater than user decimals could be wrong because of rounding. Changes in the code reflect the method used to generate the PO, by rounding each line item to user decimals, then adding. Prior code added then rounded.

    php.ini precision could cause disabling of FA PO decimal precision for items with precise Conversion Factors. Fix was to use php.ini precision rather than disable the feature.

    credit memos for tax exempt items do not have tax gl account, and code generated a PHP warning. This did not cause any problem other than the PHP warning, because G/L was created on the remaining total.

## ajax browser sync
In FA, when a user modifies selection criteria on a FA page and AJAX updates the page, then the user clicks to another page such as via the editor, the browser back button (or FA back link) does not return to the page as the user modified it. This forces the user to reenter the selection criteria and hit the Show/Search button again.

This mod updates the browser page history when AJAX updates the page so that the back button returns to the FA page as the user modified it.
## keep reconcile on bank trans mod
When a bank transaction is modified, FA unreconciles it, even if
just the memo or date was changed.

BF retains the reconciled status.
## new combo4 autocomplete
This is an experimental feature to allow finding items quickly.
## improve reports
FA displays an empty PDF if there is no data to display.

BF displays a message.
## Add delete link, direct link to void trans
Voiding a transaction is time-consuming and error prone in FA because
the user must provide a transaction number.

BF adds a red X icon to transactions.
Deleting transaction becomes as simple as clicking the red X
rather than having to navigate to Setup->Void.
## Add dimension to payments
FA does not dimension payments,
which causes a problem for cash basis accounting.

BF properly dimensions payments.
## allow negative quantity and amount on invoices

FA does not support invoices or payments
with negative quantities or amounts for customer refunds.
It expects that a credit memo be issued.
Nor does FA does not have the ability
to create a direct credit memo, nor does is it able to enter
purchased items and returned items on the same invoice.

BF allows this.
Besides code changes, this required altering
the "amt" field in table cust_allocations from "double unsigned"
to "double":

    mysql alter table cust_allocations modify amt double;

This has no adverse effect, and vanilla FA will also work
correctly on the altered table.

Note that negative quantities on an invoice
are returned to stock in BF.  If the customer returned an
item that cannot be returned to stock, then an inventory
adjustment will need to be made.  This is a manual operation.

## display balance in balance column and add debit/credit total

FA G/L inquiry is confusing because it displays the ending balance
under debit/credit.

BF displays the debit/credit in the proper columns
and the balance under the balance column.

## customer inquiry: display zero invoices; filter on sales type

BF displays zero invoices.
A zero invoice is often used when supplying
a customer with free product for marketing purposes.

## rename statement to register

FA uses the confusing term Bank Statement on Bank Account Inquiry
and on reports.

BF renames Bank Statement to Bank Account Inquiry and changes
reports to use Bank Register,
because data displayed is from FA
(all transactions entered)
and not from a bank statement
(all transactions processed by the bank).

## check_negative_stock allow for rounding on imprecise amounts

Decimal precise stock quantities
cause problems in FA because it is impossible to get them zeroed out.

BF assumes stock is zero when it falls below the precision
specified in Setup.

## g/l list description first to enable search by description

## BUG FIX: supplier inquiry GRN items filter by date

## FEATURE: use ajax for numeric calculations (part 1: purchasing)

FA is not a very responsive calculator or spreadsheet,
requiring the user to click an update button to do any math.

BF enables ajax on numeric text fields so that totals
are calculated as the user enters data in fields.

## add editor/delete funcs to reconcile

## allow user_transaction_days to be in future (negative)

BF allows a negative user_transaction_days in Setup so that
pages will display out into the future.

## supplier and customer name links on inquiry pages

BF makes supplier and customer names a html link
to their respective information pages
on the banking inquiry pages.
This allows one to quickly check or modify supplier/customer
information from the inquiry pages.
BF also adds the magnifying glass search icon next to these names
and this links to the supplier/customer inquiry pages.

## allow any user to edit another user transaction (disable user reference check)
BF allows a user to edit other user's transactions.

## move due date on invoice from header to footer and display only if delayed

FA displays a due date on direct supplier invoices.

BF only displays the due date if the transaction is delayed.
That is because it is meaningless if the transaction is being processed
immediately.

## allow counterparty on sales/purchases G/L, so a sales/AR journal entry balances to zero

Journal entries between sales to AR in FA result in erroneous customer balances because FA
only supports counterparty on AR and not on sales.

BF fixes this problem by adding counterparty to sales.

## remove more zero entries from Customer Balance report

The FA Customer Balance report still prints some zero entries
when no-zeros is selected.

BF fixes this problem.

## ajax enable account pull down menu on gl and bank inquiry

BF displays data without having to hit "Search".

## Scroll table instead of page

FA scrolls the whole screen on the inquiry pages.

BF scrolls the table instead of the page
and automatically positions the scroll at the latest entries.

## powderblue color on tax override

When creating a supplier invoice,
FA starts doing tax overrides after the "Update" key is pressed.
There is no visual indication that FA will stop
calculating the tax after adding another item.

BF colors the tax fields to indicate override.

## Enter key fix

FA ignores the Enter key.

BF retains the Enter key.
This means that data can be submitted with the Enter key
(as the user would expect)
and is particularly useful on the magnifying glass search popup,
because the user only needs to type the search clause
and hit enter, without having to move the mouse and click on "Search".

## Support for Enter payment on bank inquiry page

BF adds a link at the bottom of the bank inquiry page
to enter another payment.
Often a user will keep the bank inquiry page open
and continually add payments from there, rather than navigating
around through FA's pages of links.

## add total equities line to balance sheet report

The balance sheet report is missing a subtotal. It displays a Total Liabilities and Equities line at the bottom, which is a grand total of the Liabilities, Total Equity and Calculated Return.

This mod adds a Total Equities line to the report, which is equal to Total Equity and Calculated Return, which is the actual shareholder equity during the period.

With this mod, the Total Liabilities and Equities line then makes obvious sense.

## add missing spaces to rep102.php sql

report sql was broken because of missing spaces. Report works much better now.

## Update inst_lang.php

Fix for:
Illegal offset type in file: inst_lang.php at line 139

If you have error reporting enabled, you get this error when installing a language, at least manually. While the error can be ignored because the language successfully installs, it is disconcerting.

## Add frequently used dates to date picker

This mod updates the date picker to add frequently picked dates at the bottom of the date picker. Frequently, a person wants to review activity for the entire year or prior year. The current date picker does not offer these options, and requires so many clicks to to get these dates, that it is easier not to use the date picker and just enter the dates using the text interface.

This mod places these dates at the bottom of the date picker. It also removes the Back button, which is replaced by clicking on the date picker again, to hide or show it (i.e. toggle). Toggling is an expected behaviour by web users, as they have become accustomed to toggling the display of select menus.

## Sort the sales summary report by sales type

BF segregates the customers by sales type to make it easy to analyze
sales by sales channel.
For example, if customers are retail and wholesale, the report
will subtotal retail and wholesale sales separately.

## Add paging and scrolling to select pop-up

In FA, the select pop-up shows a (configurable) limited amount of items.
This can confuse the user into thinking that the search item does
no exist when in reality the search was limited.

BF adds scrolling and paging to the select pop-up
and no items are eliminated.
Internally,
the custom select display code was replaced with the built-in db_pager function.
This eliminates many lines of code and makes the user interface consistent
with other FA search functions.
