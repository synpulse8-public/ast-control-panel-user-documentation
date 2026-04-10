# Best practices

### Have oracle flashback enabled for the database.
We strongly recommend to have flashback enabled for the database to be able to rollback in case of any issues during the upgrade. 
This will allow you to quickly restore the database to its previous state without needing to perform a full restore from backup, which can be time-consuming.
After the rollback you can revert to the previous version of AST and investigate the issue before attempting the upgrade again.

!!! warning
    Do not revert to the previous version of AST without rolling back the database first, as this can lead to data inconsistencies and potential loss of data.
    Always ensure that the database is rolled back to the state before the upgrade before reverting to the previous version of AST.
