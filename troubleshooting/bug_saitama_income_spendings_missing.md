# Spendings and incomes tied to Saitama account were missing
When saving spendings and incomes as of 2025-12-31, spendings and incomes tied to Saitama Resona account were missing.

# Root cause
After looking at django_debug.log file within family_spending repo,
I found out it was due to missing ```AssetAccount``` as of 2025-12-31.
After doing that from business balances page, I had to reenter incomes and spendings tied to Saitama account one by one