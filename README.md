#🚀 Multi-Tier Web Application Deployment Using Docker Compose (NGINX, PHP-FPM, MySQL)

This project demonstrates a multi-tier web application architecture using Docker Compose. The application includes:

    Nginx (as a web server and reverse proxy)
    
    PHP-FPM (for application logic)
    
    MySQL (for database storage)
    
The services are isolated in different Docker networks to follow the best practices of multi-tier architecture.


⚙️ Services Overview
1. myweb (NGINX)
   
  Exposes port 80.
  
  Forwards PHP requests to the myapp (PHP-FPM) service.
  
  Mounted default.conf for custom configuration.
  
3. myapp (PHP-FPM)
   
  Runs PHP 8.3 FPM.
  
  Shares code volume with myweb to serve dynamic content.
  
5. mydb (MySQL)
   
  Runs MySQL with root password and database creation.
  
  Stores persistent data in Docker volume mydata.
  

📦 docker-compose.yml

    services:
    
  mydb:
  
    image: mysql
    
    ports:
    
      - "3306:3306"
      
    environment:
    
      MYSQL_ROOT_PASSWORD: pass@123
      
      MYSQL_DATABASE: mydatabase
      
    networks:
    
      - backend
      
    volumes:
    
      - mydata:/var/lib/mysql
      

  myapp:
  
    image: php:8.3-fpm
    
    ports:
    
      - "9000:9000"
      
    networks:
    
      - frontend
      
      - backend
      
    volumes:
    
      - mydir:/var/www/html
      

  myweb:
  
    image: nginx
    
    ports:
    
      - "80:80"
      
    depends_on:
    
      - mydb
      
      - myapp
      
    networks:
    
      - frontend
      
    volumes:
    
      - mydir:/usr/share/nginx/html
      


networks:

  frontend:
  
  backend:

volumes:

  mydata:
  
  mydir:


# in nginx container change index.html file 
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>my first nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>


🔍 Docker Verification Commands

🕸️ 1. List All Docker Networks

      docker network ls
      
        Sample Output:
        
          NETWORK ID     NAME         DRIVER    SCOPE
f44c73d2583f   frontend     bridge    local
aa3b88d27849   backend      bridge    local
b2c3dfe46e91   bridge       bridge    local
9c8fcb86b041   host         host      local

✅ Description:

frontend: Connects NGINX and PHP-FPM (App) containers.
backend: Isolates PHP-FPM (App) and MySQL (DB) communication.

💾 2. List Docker Volumes

      docker volume ls

        Sample Output:
        
          DRIVER    VOLUME NAME
local     mydata
local     mydir
    local     mydir
✅ Description:

mydata: Stores MySQL database files persistently.

mydir: Shared between NGINX and PHP-FPM for serving application code.


🐳 3. List Running Docker Containers

      docker ps
      
        Sample Output:
        
          CONTAINER ID   IMAGE            NAME        STATUS        PORTS
d4fdb49f7715   nginx:latest     myweb       Up 10 mins    0.0.0.0:80->80/tcp
c1e98dbd273a   php:8.3-fpm      myapp       Up 10 mins
b9d2f8ab4e3a   mysql:latest     mydb        Up 10 mins    3306/tcp

✅ Description:

myweb: Exposes port 80 for public HTTP traffic.

myapp: Runs PHP-FPM to handle backend logic, linked to myweb.

mydb: Manages database storage with exposed port 3306 for internal use.



✅ Quick Health Checks

🔧 Task	🔗 Command

List networks	docker network ls

Inspect a network	docker network inspect frontend

List volumes	docker volume ls

Inspect a volume (optional)	docker volume inspect mydata

List running containers	docker ps

Inspect a container	docker inspect myweb

Enter a container (bash shell)	docker exec -it myweb bash

Ping app container from nginx	docker exec -it myweb ping myapp

