# MSSQL SERVER INSTALLATION - TASK

GOALS:
------
- Learn to install SQL Server 2019 using GUI and command line methods
- Understand SQL Server instance concepts (default vs named instances)
- Configure SQL Server authentication modes
- Set up custom file locations for databases, logs, and backups
- Install and configure SQL Server Management Studio (SSMS)
- Establish remote connections to SQL Server instances

PREREQUISITES:
--------------
- 1 VM running Windows Server 2016 or Windows Server 2019
- ISO file of "SQL Server 2019 Developer Edition" installation disk
- Sufficient disk space (minimum 6 GB for SQL Server)
- .NET Framework 4.6 or later

TASKS:
------
1. Install SQL Server 2019 Default Instance (GUI Installation):
   - Mount SQL Server 2019 ISO
   - Run setup and install default instance (MSSQLSERVER)
   - Install only Database Engine component
   - Configure Mixed Authentication Mode
   - Set custom locations for:
     * Data files
     * Log files
     * Backup files

2. Install SQL Server 2019 Named Instance (Command Line Installation):
   - Use command line (setup.exe with parameters) to install
   - Create a named instance (e.g., YOURNAME or YOURINSTANCE)
   - Install only Database Engine component
   - Configure Mixed Authentication Mode
   - Set custom file locations via command line parameters

3. Install SQL Server Management Studio (SSMS):
   - Download and install SSMS on the VM
   - Download and install SSMS on the host machine

4. Configure Remote Connections:
   - Enable TCP/IP protocol for both instances
   - Configure SQL Server Browser service
   - Open required firewall ports (1433, 1434)
   - Configure named instance port (if using static port)

5. Test Remote Connections from Host Machine:
   - Connect to default instance using SSMS with SQL Authentication
   - Connect to named instance using SSMS with SQL Authentication
   - Connect to both instances using PowerShell

REQUIREMENTS:
-------------
1. Only Database Engine component should be installed
2. Named instance MUST be installed using command line
3. Both instances must use Mixed Authentication Mode
4. Custom locations must be configured for database files, logs, and backups
5. SSMS must be installed on both VM and host machine
6. Remote connections must work using SQL Authentication via SSMS and PowerShell

SELF-REVIEW:
------------
- Default instance is installed and running (service: MSSQLSERVER)
- Named instance is installed and running (service: MSSQL$INSTANCENAME)
- Both instances use Mixed Authentication Mode
- Database files are stored in custom locations
- Log files are stored in custom locations
- Backup files default to custom location
- SSMS connects locally to both instances
- SSMS on host machine connects remotely to both instances
- PowerShell can connect remotely to both instances using SQL Authentication
- SQL Server Browser service is running
- Firewall rules allow SQL Server traffic

HELPFUL MATERIALS:
------------------
https://docs.microsoft.com/en-us/sql/database-engine/install-windows/install-sql-server-from-the-command-prompt
https://docs.microsoft.com/en-us/sql/database-engine/install-windows/install-sql-server
https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms
https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port
https://docs.microsoft.com/en-us/sql/powershell/sql-server-powershell
https://docs.microsoft.com/en-us/sql/tools/configuration-manager/sql-server-configuration-manager

