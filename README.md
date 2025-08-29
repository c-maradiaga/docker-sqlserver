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
version: '3.8'

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


  


