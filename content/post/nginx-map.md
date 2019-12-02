---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Nginx Map"
subtitle: ""
summary: ""
authors: []
tags: []
categories: []
date: 2019-12-02T10:56:09+08:00
lastmod: 2019-12-02T10:56:09+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
# How to Use Nginx's map Module.

Sometimes we need to redirect old URI of our web service to new URI, without changing our legacy frontend. I will walk you through Nginx's map Module that can be used to achieve our goal.

## Step 1 – Creating map file

Create file called uri_map at root folder (_/etc/nginx/uri_map_) of Nginx and fill it in with:

```
/old/uri1	/new/uri1;
…
/old/uriX	/new/uriX;
```

where two pars represent old and new URI, separated with TAB.

## Step 2 – Edit configuration file

Include following text at the top of your nginx.conf configuration file:
	
```
map $uri $new_uri {
      include /etc/nginx/uri_map;
  }
```

and following text to server block:
	
```
server {
    listen 80;
    server_name XXX;
    ...
    # Old static views redirect
    if ($new_uri) {
        rewrite ^ $new_uri permanent;
    }
    ...
}
```

## Step 3 - Reload nginx

Use follwoing command to reload nginx configurations:

```sh
$ sudo systemctl reload nginx.service
```    

Check if redirection works with:

```
$ curl -I domain.name.com/old/uri
HTTP/1.1 301 Moved Permanently
Server: nginx/1.10.3
Date: Sat, 15 Jul 2017 05:18:55 GMT
Content-Type: text/html
Content-Length: 185
Connection: keep-alive
Location: https://domain.name.com/old/uri1
```

