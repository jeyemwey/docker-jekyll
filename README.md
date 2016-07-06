# Docker Jekyll

With the generated Docker container is possible to compile a Jekyll based
website and move the resulting HTML files to a location mounted through a
volume.

# Build

```
docker build -t mikroways/jekyll .
```

# Run

```
docker run -e "REPO_URL=https://github.com/Mikroways/mikroways.net.git" --rm -v
/path/to/webroot:/data mikroways/jekyll
```

# Automation with Nginx and Github

We need Nginx with support for LUA commands.

```
apt-get install nginx-extras
```

Following, there is a virtual host configuration example:

```
server {
  listen 80;
  root /var/www/html;
  index index.html;

  server_name _;

  location / {
    try_files $uri $uri/ =404;
  }

  location /deploy {
    auth_basic "Restricted Content";
    auth_basic_user_file /etc/nginx/.htpasswd;
    content_by_lua 'os.execute("docker run -e
REPO_URL=https://github.com/Mikroways/mikroways.net.git --rm -v
/var/www/html:/data mikroways/jekyll")';
  }
}
```
