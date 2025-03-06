Yes! You can achieve dynamic routes using *NGINX* without needing a backend like Node.js. 🚀  

---

## ✅ *Solution: Use NGINX for Dynamic Routing*
NGINX can serve different HTML files based on the requested URL using *rewrite rules*.

---

### *1️⃣ Example Project Structure*

/project
│── public/
│   ├── index.html      # Home Page
│   ├── login.html      # Login Page
│   ├── signup.html     # Signup Page
│   ├── username.html   # Generic User Profile Page
│── nginx.conf          # NGINX Configuration File
│── docker-compose.yml  # (Optional) Run NGINX with Docker


---

### **2️⃣ Configure NGINX (nginx.conf)**
nginx
server {
    listen 80;
    server_name localhost;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Serve specific pages
    location = /login {
        rewrite ^ /login.html break;
    }

    location = /signup {
        rewrite ^ /signup.html break;
    }

    # Dynamic route for usernames
    location / {
        if ($request_uri ~* "^/([a-zA-Z0-9_-]+)$") {
            rewrite ^ /username.html break;
        }
    }

    error_page 404 /index.html;
}


---

### *3️⃣ Explanation*
✔ / → Serves index.html  
✔ /login → Serves login.html  
✔ /signup → Serves signup.html  
✔ /carlos, /john → Serves username.html (Dynamic Profile)  

---

### **4️⃣ Modify username.html to Display the Username**
Since username.html is loaded dynamically, we need *JavaScript* to extract the username:
html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>User Profile</title>
</head>
<body>
    <h1>Profile Page</h1>
    <p id="username">Loading...</p>

    <script>
        const username = window.location.pathname.substring(1); // Get the username from the URL
        document.getElementById("username").innerText = `Welcome, ${username}!`;
    </script>
</body>
</html>


---

### *5️⃣ Start NGINX Locally*
#### 🏗 *Option 1: Run NGINX in Docker*
Create a *Docker container* for NGINX:
yaml
# docker-compose.yml
version: '3'
services:
  nginx:
    image: nginx
    ports:
      - "8080:80"
    volumes:
      - ./public:/usr/share/nginx/html
      - ./nginx.conf:/etc/nginx/conf.d/default.conf

Run:
sh
docker-compose up -d

Now visit [http://localhost:8080/carlos](http://localhost:8080/carlos)

#### 🏗 *Option 2: Install and Run NGINX Manually*
sh
sudo apt install nginx  # Linux
brew install nginx      # macOS

Copy nginx.conf to /etc/nginx/nginx.conf, then restart:
sh
sudo systemctl restart nginx


---

## 🎯 *Final Thoughts*
✔ *No backend needed*  
✔ Works *without JavaScript frameworks*  
✔ URLs are **clean (/carlos instead of /#/carlos)**  
✔ Uses *NGINX rewrite rules* for dynamic routing  

🚀 Now you have a *dynamic routing setup with NGINX and separate HTML files!* Want to deploy this on a real server? 🔥
