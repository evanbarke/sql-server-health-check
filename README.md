# SQL Server health check (read-only collector)

A single T-SQL script that collects the configuration and health metadata of a SQL Server instance in a few seconds, without reading any business data and without changing anything. It is the collector behind the free [SQL Server health score](https://samix-technology.com/en/sql-server-health-score) by Samix Technology, and it is useful on its own as a quick, safe audit snapshot.

## What it checks

- Version, patch level and end-of-support status (SQL Server and Windows Server)
- Instance settings: max server memory, MAXDOP, cost threshold for parallelism, xp_cmdshell, optimize for ad hoc workloads and more
- Every database: recovery model, compatibility level, page verify, auto close / auto shrink, TRUSTWORTHY, size, log reuse wait
- Backups: age of the last full, differential and log backup, backup checksums
- Integrity: age of the last good DBCC CHECKDB
- Security (counts only): sysadmin members, `sa` disabled or renamed, SQL logins without password policy, blank or name-equal passwords
- Top 10 waits (benign waits excluded)
- Missing and unused index counts
- tempdb layout, file growth settings, free disk space on volumes holding database files
- SQL Agent job failures over 7 days and Agent service status
- High availability features in use (Availability Groups, mirroring, log shipping)

## What it never collects

Table rows, query text, execution plans, login names, password hashes, connection strings, server or host name. It creates nothing except `#temp` tables that disappear with the session, and it sends nothing anywhere: the output stays on your machine unless you choose to upload it.

Set `@MaskNames = 1` at the top of the script to replace database and SQL Agent job names with `db<id>` and `job<N>`.

## Requirements

SQL Server 2008 R2 or later. `VIEW SERVER STATE`, `VIEW ANY DEFINITION` and read access to `msdb`; `sysadmin` gives the most complete result.

## Run it

```
sqlcmd -S YOURSERVER -E -i samix-sql-health-check.sql -o samix-health.txt -W -h -1
```

Or open the file in SSMS and press F5.

The output is one line per record (`SHC1|section|key=value|...`), easy to read or diff between two runs.

## Get a score and a report

Upload the output file at **[samix-technology.com/en/sql-server-health-score](https://samix-technology.com/en/sql-server-health-score)** for a free score on 7 areas with the findings explained.

To check whether your build is current or out of support, see the [SQL Server builds and end-of-support dates](https://samix-technology.com/en/sql-server-builds) list.

## En français

Script T-SQL en **lecture seule** : il lit uniquement des métadonnées de configuration et de performance (vues système, DMV, historique msdb). Il ne lit aucune ligne de vos tables, ne crée ni ne modifie aucun objet et n'envoie rien : le fichier produit reste chez vous, sauf si vous choisissez de le déposer.

- Score de santé gratuit : [samix-technology.com/score-sante-sql-server](https://samix-technology.com/score-sante-sql-server)
- Versions de SQL Server et fins de support : [samix-technology.com/versions-sql-server](https://samix-technology.com/versions-sql-server)

## Licence

MIT. Made by [Samix Technology](https://samix-technology.com/), SQL Server DBA and performance consulting, Perpignan, France.
