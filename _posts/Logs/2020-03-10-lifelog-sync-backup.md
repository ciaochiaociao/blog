---
title: "[lifelog] Sync/Backup"
---

- Backup
	- normal
	- port 22 (ssh)
		- scp
		- sftp
	- ftp/ftps

- SyncBack
	- Free version does not support port 22 (SSH) but only port 21 (FTP)
  - workaround: https://dmihalik.com/articles/2006/03/22/using-ftp-applications-over-sftp/
		- Used Tunnelier’s software to create a service that bridges FTP to SFTP (needs the priviledge in server)
- Alternatives
	- Backup4all (not free)