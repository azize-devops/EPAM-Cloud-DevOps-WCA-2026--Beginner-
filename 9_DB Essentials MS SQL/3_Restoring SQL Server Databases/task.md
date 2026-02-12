# RESTORING SQL SERVER DATABASES - TASK

GOALS:
------
- Understand SQL Server backup types (Full, Differential, Transaction Log)
- Learn to configure recovery models and backup compression
- Master point-in-time recovery using transaction log backups
- Understand tail-log backup importance in disaster recovery
- Practice restoring databases from multiple backup files

PREREQUISITES:
--------------
- 1 VM with installed SQL Server 2019
- AdventureWorks database backup file
- SQL Server Management Studio (SSMS) on host machine
- Remote connection configured from host to VM

TASKS:
------
1. Restore AdventureWorks DB on VM SQL Server instance

2. Make FULL backup (without compression)

3. Change recovery model to FULL

4. Enable backup compression (server-wide setting)

5. Make FULL backup (with compression enabled)

6. Compare backup sizes:
   - Full backup without compression
   - Full backup with compression
   - Calculate compression ratio

7. Perform data changes and make Differential backup:
   - Edit rows in Person.PersonPhone table
   - Create differential backup

8. Make additional changes and perform Transaction Log backup

9. Repeat step 8 two or three more times:
   - Make different changes each time
   - Take transaction log backup after each change

10. Note the exact time when you finished all changes

11. Wait 2-3 minutes, then DELETE the table you worked with (Person.PersonPhone)

12. Perform Point-in-Time Restore:
    - Restore AdventureWorks to the time noted in step 10
    - Ensure tail-log backup is taken before restore
    - Use STOPAT option for point-in-time recovery

13. Verify restoration:
    - Check that Person.PersonPhone table exists
    - Verify all changes made before the noted time are present

14. Simulate disaster recovery:
    - Put database in OFFLINE mode
    - Delete the MDF file physically

15. Restore database from backup chain:
    - Restore from Full backup
    - Apply Differential backup
    - Apply all Transaction Log backups
    - Bring database online

REQUIREMENTS:
-------------
1. ALL actions must be performed from HOST machine (remote connection)
2. Backup and edit operations must be performed using single T-SQL scripts
3. Recovery model must be set to FULL for transaction log backups
4. Backup compression must be enabled and size difference documented
5. Point-in-time restore must recover data to the exact noted time
6. Tail-log backup must be taken before point-in-time restore
7. Final restore must use the complete backup chain (Full + Diff + Logs)

SELF-REVIEW:
------------
- AdventureWorks is restored successfully
- Full backup created without compression
- Recovery model changed to FULL
- Backup compression enabled server-wide
- Full backup created with compression (smaller size)
- Compression ratio calculated and documented
- Data changes made to Person.PersonPhone
- Differential backup created after changes
- Multiple transaction log backups created
- Time noted before deleting table
- Table deleted after waiting 2-3 minutes
- Point-in-time restore completed with tail-log backup
- Table exists after restore with all changes intact
- Database taken offline and MDF deleted
- Database restored from backup chain successfully
- All operations performed via T-SQL from host machine

HELPFUL MATERIALS:
------------------
https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server
https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/differential-backups-sql-server
https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/transaction-log-backups-sql-server
https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/restore-a-database-to-a-point-in-time-sql-server
https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/tail-log-backups-sql-server
https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/backup-compression-sql-server
https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server

