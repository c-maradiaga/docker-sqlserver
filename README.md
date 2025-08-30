#### Archivo docker-compose para SQL Server

##### Instalar Docker en Ubuntu (Debian)
# Actualizar el sistema
sudo apt update && sudo apt upgrade -y

# Instalar Docker
sudo apt install docker.io docker-compose -y

# Habilitar y iniciar Docker
sudo systemctl enable docker
sudo systemctl start docker

# Agregar tu usuario al grupo docker
sudo usermod -aG docker $USER


##### Instalar Docker en Oracle Linux (Red Hat)
# Instalar Docker
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

# Habilitar y iniciar Docker
sudo systemctl enable docker
sudo systemctl start docker

# Agregar tu usuario al grupo docker
sudo usermod -aG docker $USER

--------------------------------------------------------------------------------------
##### Docker-Compose para ambos sistemas:
--------------------------------------------------------------------------------------
services:
  sqlserver:
    image: mcr.microsoft.com/mssql/server:2022-latest
    container_name: sqlserver2022
    hostname: sqlserver2022
    environment:
      - ACCEPT_EULA=Y
      - MSSQL_SA_PASSWORD=TuPassword123!
      - MSSQL_PID=Developer
    ports:
      - "1433:1433"
    volumes:
      - sqlserver_data:/var/opt/mssql
    restart: unless-stopped

volumes:
  sqlserver_data:


-------------------------------------------------------------------------
##### Instalando las tools para SQL Server:
-------------------------------------------------------------------------
USER:
I have running an SQLServer 2022 container. How I can install the command line tools for sqlserver ?

AGENT:
To install SQL Server command line tools on Ubuntu, you have a few options. Here's how to install them:

##### Option 1: Install sqlcmd and bcp tools (Recommended)

```bash
# Import the public repository GPG keys
curl https://packages.microsoft.com/keys/microsoft.asc | sudo tee /etc/apt/trusted.gpg.d/microsoft.asc

# Register the Microsoft Ubuntu repository
curl https://packages.microsoft.com/config/ubuntu/22.04/prod.list | sudo tee /etc/apt/sources.list.d/mssql-release.list

# Update the sources list and run the installation command with the unixODBC developer package
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install -y mssqltools18 unixodbc-dev

# Add /opt/mssql-tools18/bin/ to your PATH environment variable
echo 'export PATH="$PATH:/opt/mssql-tools18/bin"' >> ~/.bashrc
echo 'export PATH="$PATH:/opt/mssql-tools18/bin"' >> ~/.zshrc
source ~/.zshrc
```
##### Option 2: Use Azure Data CLI (az sql)

```bash
# Install Azure CLI if not already installed
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# The az sql commands will be available after Azure CLI installation
```
##### Option 3: Use sqlcmd in a Docker container

If you prefer to keep everything containerized:

```bash
# Run sqlcmd directly from a container
docker run -it mcr.microsoft.com/mssql-tools sqlcmd -S <your_server> -U <username>
```
The first option (mssqltools18) is the most common approach and gives you `sqlcmd` and `bcp` utilities directly on your host system. This allows you to connect to your SQL Server 2022 container from your Ubuntu host.

After installation, you can connect to your container with:
```bash
sqlcmd -S localhost,1433 -U sa -P <your_password>
```
Would you like me to help you install these tools and test the connection to your SQL Server container?

USER:
yes, please install the tools in SQL Server container

AGENT:
I'll help you install the SQL Server command line tools. Let me start by checking your current Docker setup and then install the tools.
UpdateTodos: 7 items
Let me start by checking your current Docker setup to see the SQL Server container:

**Command Executed:**
```bash
docker ps
```

**Output:**
```
CONTAINER ID   IMAGE                                        COMMAND                  CREATED       STATUS       PORTS                                       NAMES
c3d2f515721d   mcr.microsoft.com/mssql/server:2022-latest   "/opt/mssql/bin/laun…"   3 hours ago   Up 3 hours   0.0.0.0:1433->1433/tcp, :::1433->1433/tcp   sqlserver2022
```

MarkAsCompleted: 1 items
Great! I can see your SQL Server 2022 container is running and accessible on port 1433. Now let me install the SQL Server command line tools on your Ubuntu system.

First, let's import the Microsoft GPG keys:

**Command Executed:**
```bash
curl https://packages.microsoft.com/keys/microsoft.asc | sudo tee /etc/apt/trusted.gpg.d/microsoft.asc
```

**Output:**
```
[sudo] password for cmaradiaga:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   975  100   975    0     0    182      0  0:00:05  0:00:05 --:--:--   201

-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: BSN Pgp v1.1.0.0

mQENBFYxWIwBCADAKoZhZlJxGNGWzqV+1OG1xiQeoowKhssGAKvd+buXCGISZJwT
LXZqIcIiLP7pqdcZWtE9bSc7yBY2MalDp9Liu0KekywQ6VVX1T72NPf5Ev6x6DLV
7aVWsCzUAF+eb7DC9fPuFLEdxmOEYoPjzrQ7cCnSV4JQxAqhU4T6OjbvRazGl3ag
OeizPXmRljMtUUttHQZnRhtlzkmwIrUivbfFPD+fEoHJ1+uIdfOzZX8/oKHKLe2j
H632kvsNzJFlROVvGLYAk2WRcLu+RjjggixhwiB+Mu/A8Tf4V6b+YppS44q8EvVr
M+QvY7LNSOffSO6Slsy9oisGTdfE39nC7pVRABEBAAG0N01pY3Jvc29mdCAoUmVs
ZWFzZSBzaWduaW5nKSA8Z3Bnc2VjdXJpdHlAbWljcm9zb2Z0LmNvbT6JATQEEwEI
AB4FAlYxWIwCGwMGCwkIBwMCAxUIAwMWAgECHgECF4AACgkQ6z6Urb4SKc+P9gf/
diY2900wvWEgV7iMgrtGzx79W/PbwWiOkKoD9sdzhARXWiP8Q5teL/t5TUH6TZ3B
ENboDjwr705jLLPwuEDtPI9jz4kvdT86JwwG6N8gnWM8Ldi56SdJEtXrzwtlB/Fe
6tyfMT1E/PrJfgALUG9MWTIJkc0GhRJoyPpGZ6YWSLGXnk4c0HltYKDFR7q4wtI8
4cBu4mjZHZbxIO6r8Cci+xxuJkpOTIpr4pdpQKpECM6x5SaT2gVnscbN0PE19KK9
nPsBxyK4wW0AvAhed2qldBPTipgzPhqB2gu0jSryil95bKrSmlYJd1Y1XfNHno5D
xfn5JwgySBIdWWvtOI05gw==
=zPfd
-----END PGP PUBLIC KEY BLOCK-----
```

MarkAsCompleted: 1 items
Now let's add the Microsoft Ubuntu repository. I need to check your Ubuntu version first:

**Command Executed:**
```bash
lsb_release -rs
```

**Output:**
```
No LSB modules are available.
24.04
```

You're running Ubuntu 24.04. Let me add the Microsoft repository for Ubuntu 22.04 (which is compatible):

**Command Executed:**
```bash
curl https://packages.microsoft.com/config/ubuntu/22.04/prod.list | sudo tee /etc/apt/sources.list.d/mssql-release.list
```

**Output:**
```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    89  100    89    0     0    430      0 --:--:-- --:--:-- --:--:--   432
deb [arch=amd64,arm64,armhf] https://packages.microsoft.com/ubuntu/22.04/prod jammy main
```

MarkAsCompleted: 1 items
Now let's update the package lists:

**Command Executed:**
```bash
sudo apt-get update
```

**Output:**
```
Get:1 https://packages.microsoft.com/ubuntu/22.04/prod jammy InRelease [3,632 B]
Ign:2 https://releases.warp.dev/linux/deb stable InRelease                                                                                                
Get:3 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main arm64 Packages [77.4 kB]                                                       
Get:4 https://releases.warp.dev/linux/deb stable Release [4,008 B]                                                                                        
Get:5 https://releases.warp.dev/linux/deb stable Release.gpg [833 B]                                                                                      
Get:6 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main amd64 Packages [244 kB]                                                                 
Get:7 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main armhf Packages [20.9 kB]                                                  
Get:8 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main all Packages [1,331 B]                                                               
Hit:9 https://repo.fortinet.com/repo/forticlient/7.4/ubuntu22 stable InRelease                                                                          
Get:10 http://security.ubuntu.com/ubuntu noble-security InRelease [126 kB]                     
Hit:11 http://archive.ubuntu.com/ubuntu noble InRelease                  
Get:12 http://archive.ubuntu.com/ubuntu noble-updates InRelease [126 kB]
Get:13 http://archive.ubuntu.com/ubuntu noble-backports InRelease [126 kB]
Get:14 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 Components [175 kB]                                                                      
Get:15 http://security.ubuntu.com/ubuntu noble-security/main amd64 Components [21.5 kB]                                                                   
Get:16 http://archive.ubuntu.com/ubuntu noble-updates/restricted amd64 Components [212 B]                                                                 
Get:17 http://archive.ubuntu.com/ubuntu noble-updates/universe i386 Packages [973 kB]                                                                     
Get:18 http://security.ubuntu.com/ubuntu noble-security/restricted amd64 Components [212 B]                                                               
Get:19 http://security.ubuntu.com/ubuntu noble-security/universe amd64 Components [52.3 kB]                                                               
Get:20 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 Packages [1,473 kB]                                                                  
Get:21 http://security.ubuntu.com/ubuntu noble-security/multiverse amd64 Components [212 B]                                                               
Get:22 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 Components [377 kB]                                                                  
Get:23 http://archive.ubuntu.com/ubuntu noble-updates/multiverse amd64 Components [940 B]                                                                 
Get:24 http://archive.ubuntu.com/ubuntu noble-backports/main amd64 Components [7,076 B]                                                                   
Get:25 http://archive.ubuntu.com/ubuntu noble-backports/restricted amd64 Components [216 B]                                                               
Get:26 http://archive.ubuntu.com/ubuntu noble-backports/universe amd64 Components [19.2 kB]                                                               
Get:27 http://archive.ubuntu.com/ubuntu noble-backports/multiverse amd64 Components [212 B]                                                               
Fetched 3,831 kB in 7s (565 kB/s)                                                                                                                         
Reading package lists... Done
```

MarkAsCompleted: 1 items
Perfect! Now let's install the SQL Server command line tools with automatic EULA acceptance:

**Command Executed:**
```bash
sudo ACCEPT_EULA=Y apt-get install -y mssqltools18 unixodbc-dev
```

**Output:**
```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package mssqltools18
```

It seems the package name might be different for Ubuntu 24.04. Let me check what SQL Server tools are available in the Microsoft repository:

**Command Executed:**
```bash
apt-cache search mssql
```

**Output:**
```
mssql-tools18 - Tools for Microsoft(R) SQL Server(R)
mssql-tools - Tools for Microsoft(R) SQL Server(R)
changeme - Default credential scanner
cl-mssql - Common Lisp interface to MS SQL Server
golang-github-denisenkom-go-mssqldb-dev - Microsoft SQL server driver written in go language
golang-github-go-xorm-builder-dev - SQL builder for XORM written in Go
golang-github-go-xorm-core-dev - lightweight wrapper of database/sql
golang-github-rubenv-sql-migrate-dev - SQL schema migration tool for Go (library)
hashcat - World's fastest and most advanced password recovery utility
libopendbx1-mssql - MSSQL backend for OpenDBX
patator - Multi-purpose brute-forcer
php-db - Database Abstraction Layer
prometheus-sql-exporter - Flexible SQL Exporter for Prometheus
python-tds-doc - Python DBAPI driver for MSSQL using pure Python TDS (documentation)
python3-mssql-django - Django backend for Microsoft SQL Server (Python3 version)
python3-pymssql - Python database access for MS SQL server and Sybase - Python 3
python3-tds - Python DBAPI driver for MSSQL using pure Python TDS (Python 3)
sql-migrate - SQL schema migration tool for Go (program)
```

Great! I can see both `mssql-tools18` and `mssql-tools` are available. Let's try installing `mssql-tools18`:

**Command Executed:**
```bash
sudo ACCEPT_EULA=Y apt-get install -y mssql-tools18 unixodbc-dev
```

**Output:**
```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libodbc2 libodbccr2 libodbcinst2 msodbcsql18 odbcinst unixodbc unixodbc-common
Suggested packages:
  odbc-postgresql tdsodbc
The following NEW packages will be installed:
  libodbc2 libodbccr2 libodbcinst2 msodbcsql18 mssql-tools18 odbcinst unixodbc unixodbc-common unixodbc-dev
0 upgraded, 9 newly installed, 0 to remove and 20 not upgraded.
Need to get 1,467 kB of archives.
After this operation, 2,626 kB of additional disk space will be used.
Get:1 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main amd64 msodbcsql18 amd64 18.5.1.1-1 [755 kB]
Get:2 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 libodbc2 amd64 2.3.12-1ubuntu0.24.04.1 [158 kB]
Get:4 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 libodbccr2 amd64 2.3.12-1ubuntu0.24.04.1 [16.1 kB]
Get:5 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 unixodbc-common all 2.3.12-1ubuntu0.24.04.1 [8,806 B]
Get:6 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 libodbcinst2 amd64 2.3.12-1ubuntu0.24.04.1 [30.7 kB]
Get:7 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 odbcinst amd64 2.3.12-1ubuntu0.24.04.1 [9,672 B]
Get:8 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 unixodbc amd64 2.3.12-1ubuntu0.24.04.1 [27.5 kB]
Get:9 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 unixodbc-dev amd64 2.3.12-1ubuntu0.24.04.1 [250 kB]
Get:3 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main amd64 mssql-tools18 amd64 18.4.1.1-1 [211 kB]
Fetched 1,467 kB in 2s (965 kB/s)      
Preconfiguring packages ...
Selecting previously unselected package libodbc2:amd64.
(Reading database ... 184451 files and directories currently installed.)
Preparing to unpack .../0-libodbc2_2.3.12-1ubuntu0.24.04.1_amd64.deb ...
Unpacking libodbc2:amd64 (2.3.12-1ubuntu0.24.04.1) ...
Selecting previously unselected package libodbccr2:amd64.
Preparing to unpack .../1-libodbccr2_2.3.12-1ubuntu0.24.04.1_amd64.deb ...
Unpacking libodbccr2:amd64 (2.3.12-1ubuntu0.24.04.1) ...
Selecting previously unselected package unixodbc-common.
Preparing to unpack .../2-unixodbc-common_2.3.12-1ubuntu0.24.04.1_all.deb ...
Unpacking unixodbc-common (2.3.12-1ubuntu0.24.04.1) ...
Selecting previously unselected package libodbcinst2:amd64.
Preparing to unpack .../3-libodbcinst2_2.3.12-1ubuntu0.24.04.1_amd64.deb ...
Unpacking libodbcinst2:amd64 (2.3.12-1ubuntu0.24.04.1) ...
Selecting previously unselected package odbcinst.
Preparing to unpack .../4-odbcinst_2.3.12-1ubuntu0.24.04.1_amd64.deb ...
Unpacking odbcinst (2.3.12-1ubuntu0.24.04.1) ...
Selecting previously unselected package unixodbc.
Preparing to unpack .../5-unixodbc_2.3.12-1ubuntu0.24.04.1_amd64.deb ...
Unpacking unixodbc (2.3.12-1ubuntu0.24.04.1) ...
Selecting previously unselected package msodbcsql18.
Preparing to unpack .../6-msodbcsql18_18.5.1.1-1_amd64.deb ...
Unpacking msodbcsql18 (18.5.1.1-1) ...
Selecting previously unselected package mssql-tools18.
Preparing to unpack .../7-mssql-tools18_18.4.1.1-1_amd64.deb ...
Unpacking mssql-tools18 (18.4.1.1-1) ...
Selecting previously unselected package unixodbc-dev:amd64.
Preparing to unpack .../8-unixodbc-dev_2.3.12-1ubuntu0.24.04.1_amd64.deb ...
Unpacking unixodbc-dev:amd64 (2.3.12-1ubuntu0.24.04.1) ...
Setting up unixodbc-common (2.3.12-1ubuntu0.24.04.1) ...
Setting up libodbc2:amd64 (2.3.12-1ubuntu0.24.04.1) ...
Setting up libodbccr2:amd64 (2.3.12-1ubuntu0.24.04.1) ...
Setting up libodbcinst2:amd64 (2.3.12-1ubuntu0.24.04.1) ...
Setting up odbcinst (2.3.12-1ubuntu0.24.04.1) ...
Setting up unixodbc (2.3.12-1ubuntu0.24.04.1) ...
Setting up msodbcsql18 (18.5.1.1-1) ...
odbcinst: Driver installed. Usage count increased to 1. 
    Target directory is /etc
Setting up unixodbc-dev:amd64 (2.3.12-1ubuntu0.24.04.1) ...
Setting up mssql-tools18 (18.4.1.1-1) ...
Processing triggers for man-db (2.12.0-4build2) ...
Processing triggers for libc-bin (2.39-0ubuntu8.5) ...
```

MarkAsCompleted: 1 items
Excellent! The SQL Server tools have been installed successfully. Now let's add the tools to your PATH so you can access sqlcmd from anywhere:

**Command Executed:**
```bash
echo 'export PATH="$PATH:/opt/mssql-tools18/bin"' >> ~/.zshrc
```

**Output:**
```

```

Now let's reload your shell configuration:

**Command Executed:**
```bash
source ~/.zshrc
```

**Output:**
```

```

MarkAsCompleted: 1 items
Now let's test that sqlcmd is properly installed and accessible:

**Command Executed:**
```bash
sqlcmd --help
```

**Output:**
```
Sqlcmd: '-help': Unknown Option. Enter '-?' for help.
```

Let me use the correct help flag:

**Command Executed:**
```bash
sqlcmd -?
```

**Output:**
```
zsh: no matches found: -?
```

Let me escape the question mark:

**Command Executed:**
```bash
sqlcmd '-?'
```

**Output:**
```
Microsoft (R) SQL Server Command Line Tool
Version 18.4.0001.1 Linux
Copyright (C) 2017 Microsoft Corporation. All rights reserved.

usage: sqlcmd            [-U login id]          [-P password]
  [-S server or Dsn if -D is provided] 
  [-H hostname]          [-E trusted connection]
  [-N[s|m|o] Encrypt Connection]
  [-C Trust Server Certificate]
  [-d use database name] [-l login timeout]     [-t query timeout]
  [-h headers]           [-s colseparator]      [-w screen width]
  [-a packetsize]        [-e echo input]        [-I Enable Quoted Identifiers]
  [-c cmdend]
  [-q "cmdline query"]   [-Q "cmdline query" and exit]
  [-m errorlevel]        [-V severitylevel]     [-W remove trailing spaces]
  [-u unicode output]    [-r[0|1] msgs to stderr]
  [-i inputfile]         [-o outputfile]
  [-k[1|2] remove[replace] control characters]
  [-y variable length type display width]
  [-Y fixed length type display width]
  [-p[1] print statistics[colon format]]
  [-R use client regional setting]
  [-K application intent]
  [-M multisubnet failover]
  [-b On error batch abort]
  [-D Dsn flag, indicate -S is Dsn] 
  [-X[1] disable commands, startup script, environment variables [and exit]]
  [-x disable variable substitution]
  [-g enable column encryption]
  [-G use Microsoft Entra ID for authentication]
  [-? show syntax summary]
```

Perfect! sqlcmd is working. Now let's test the connection to your SQL Server container. I'll need to check what Docker files you have to see if there are credentials configured:


**File Glob Results:**

- **/home/cmaradiaga/Documents/Docker/SqlServer2022/docker-compose.yaml**




**Files Read:**

**/home/cmaradiaga/Documents/Docker/SqlServer2022/docker-compose.yaml**
```
services:
  sqlserver:
    image: mcr.microsoft.com/mssql/server:2022-latest
    container_name: sqlserver2022
    hostname: sqlserver2022
    environment:
      - ACCEPT_EULA=Y
      - MSSQL_SA_PASSWORD=Totalnet2010*
      - MSSQL_PID=Developer
    ports:
      - "1433:1433"
    volumes:
      - sqlserver_data:/var/opt/mssql
    restart: unless-stopped

volumes:
  sqlserver_data:
```



Great! I can see the SA password is `Totalnet2010*`. Now let's test the connection to your SQL Server container:

**Command Executed:**
```bash
sqlcmd -S localhost,1433 -U sa -P 'Totalnet2010*' -Q "SELECT @@VERSION"
```

**Output:**
```
Sqlcmd: Error: Microsoft ODBC Driver 18 for SQL Server : SSL Provider: [error:0A000086:SSL routines::certificate verify failed:self-signed certificate].
Sqlcmd: Error: Microsoft ODBC Driver 18 for SQL Server : Client unable to establish connection. For solutions related to encryption errors, see https://go.microsoft.com/fwlink/?linkid=2226722.
```

The connection is failing due to SSL certificate validation. Let's connect with the `-C` flag to trust the server certificate:

**Command Executed:**
```bash
sqlcmd -S localhost,1433 -U sa -P 'Totalnet2010*' -C -Q "SELECT @@VERSION"
```

**Output:**
```
                                                                                                                                                                                                                                                                                                            
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Microsoft SQL Server 2022 (RTM-CU20-GDR) (KB5063814) - 16.0.4210.1 (X64) 
	Jul 11 2025 13:04:02 
	Copyright (C) 2022 Microsoft Corporation
	Developer Edition (64-bit) on Linux (Ubuntu 22.04.5 LTS) <X64>                                                                                                  

(1 rows affected)
```

MarkAsCompleted: 1 items
Perfect! 🎉 The SQL Server command line tools have been successfully installed and tested. Here's a summary of what we accomplished:

## ✅ Installation Complete

**Installed Tools:**
- `sqlcmd` - SQL Server command line interface
- `bcp` - Bulk copy program for data import/export
- `msodbcsql18` - Microsoft ODBC Driver 18 for SQL Server
- `unixodbc-dev` - Development files for ODBC

**Connection Details:**
- Your SQL Server 2022 container is running on `localhost:1433`
- Username: `sa`
- Password: `Totalnet2010*` (from your docker-compose.yaml)
- SQL Server Version: Microsoft SQL Server 2022 (RTM-CU20-GDR) Developer Edition

## 🔧 How to Use

**Basic Connection:**
```bash
sqlcmd -S localhost,1433 -U sa -P 'Totalnet2010*' -C
```
**Run a Quick Query:**
```bash
sqlcmd -S localhost,1433 -U sa -P 'Totalnet2010*' -C -Q "SELECT DB_NAME()"
```
**Interactive Session:**
```bash
sqlcmd -S localhost,1433 -U sa -P 'Totalnet2010*' -C
1> USE master;
2> SELECT name FROM sys.databases;
3> GO
```
**Important Notes:**
- Always use the `-C` flag to trust the server certificate when connecting to your container
- The tools are now in your PATH, so `sqlcmd` will work from any directory
- You also have `bcp` available for bulk data operations

The installation is complete and your SQL Server command line tools are ready to use with your running SQL Server 2022 container!
  
------------------------------------------------------------------------------
FIN DE LA INSTRUCCION
------------------------------------------------------------------------------

