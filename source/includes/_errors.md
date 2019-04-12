# Errors

## Order execution error code

When something go wrong with the signed order message was sent to the API server, the API uses the following error codes:

Error Code | Meaning
---------- | -------
1001 | TransactionType is missing.
1002 | Unsupported transactionType.
1003 | TransactionId cannot be empty.
1004 | Missing signature. Signature cannot be empty.
1005 | Duplicated transaction.
1006 | Missing account identifier. Either of following fields is required: accountId, accountName.
1007 | Missing account name.
1008 | Account name not found.
1009 | MinTickSize violation. MinTickSize is violated in price.
1010 | Minimum quantity violation. Quantity is less than minimum quantity.
1011 | Asset is not found.
1012 | Unsupported asset pair.
1014 | Database is not available. Please re-try in a few minutes.
1015 | Invalid input is found.

## HTTP error code

Error Code | Meaning
---------- | -------
400 | Parameter Error -- Your request is invalid.
401 | Unauthorized -- Your API key is wrong.
404 | Not Found -- The specified item could not be found.
500 | Internal Server Error -- We had a problem with our server. Try again later.
502 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.
