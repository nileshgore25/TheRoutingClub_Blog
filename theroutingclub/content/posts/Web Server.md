---
title: "Web Server"
date: 2022-05-19
draft: false
tags: [Web Server, Nginx]
# series: [MPLS VPN]
# categories: [technical]
toc: true
sidebar: false
singleColumn : true
# description: abc
# introDescription: efg
# abstract: xyz
summary: "In this post, we will see what is web server, how a web server works and then study Nginx web server and its functions."
# AuthorName: "Nilesh Gore"
---

In this post, we will see what is web server, how a web server works and then study Nginx web servers and its functions.

# What is Web Server ?
A web server stores and delivers the content for a website – such as text, images, video, and application data – to clients that request it. The most common type of client is a web browser program, which requests data from your website when a user clicks on a link or downloads a document on a page displayed in the browser.

The main job of a web server is to display website content through storing, processing and delivering webpages to users. Besides HTTP, web servers also support SMTP (Simple Mail Transfer Protocol) and FTP (File Transfer Protocol), used for email, file transfer and storage.

Web server hardware is connected to the internet and allows data to be exchanged with other connected devices, while web server software controls how a user accesses hosted files. The web server process is an example of the client/server model. All computers that host websites must have web server software.

A web server communicates with a web browser using the Hypertext Transfer Protocol (HTTP). The content of most web pages is encoded in Hypertext Markup Language (HTML). The content can be static (for example, text and images) or dynamic (for example, a computed price or the list of items a customer has marked for purchase). To deliver dynamic content, most web servers support server‑side scripting languages to encode business logic into the communication. Commonly supported languages include Active Server Pages (ASP), Javascript, PHP, Python, and Ruby.

A web server might also cache content to speed delivery of commonly requested content. This process is also known as **web acceleration**.

A web server can host a single website or multiple websites using the same software and hardware resources, which is known as virtual hosting. Web servers can also limit the speed of response to different clients so as to prevent a single client from dominating resources that are better used to satisfy requests from a large number of clients.

# How does a Web Server work?
Web server software is accessed through the domain names of websites and ensures the delivery of the site's content to the requesting user. The software side is also comprised of several components, with at least an HTTP server. The HTTP server is able to understand HTTP and URLs. As hardware, a web server is a computer that stores web server software and other files related to a website, such as HTML documents, images and JavaScript files.

When a web browser, like Google Chrome or Firefox, needs a file that's hosted on a web server, the browser will request the file by HTTP. When the request is received by the web server, the HTTP server will accept the request, find the content and send it back to the browser through HTTP.

More specifically, when a browser requests a page from a web server, the process will follow a series of steps. First, a person will specify a URL in a web browser's address bar. The web browser will then obtain the IP address of the domain name -- either translating the URL through DNS (Domain Name System) or by searching in its cache. This will bring the browser to a web server. The browser will then request the specific file from the web server by an HTTP request. The web server will respond, sending the browser the requested page, again, through HTTP. If the requested page does not exist or if something goes wrong, the web server will respond with an error message. The browser will then be able to display the webpage.

# Popular Web Server Softwares
There are a number of common web servers available, some including:

* **Apache HTTP Server:** Developed by Apache Software Foundation, it is a free and open source web server for Windows, Mac OS X, Unix, Linux, Solaris and other operating systems; it needs the Apache license.
* **Microsoft Internet Information Services (IIS):** Developed by Microsoft for Microsoft platforms; it is not open sourced, but widely used.
* **Nginx:** A popular open source web server for administrators because of its light resource utilization and scalability. It can handle many concurrent sessions due to its event-driven architecture. Nginx also can be used as a proxy server and load balancer.
* **Lighttpd:** A free web server that comes with the FreeBSD operating system. It is seen as fast and secure, while consuming less CPU power.
* **Sun Java System Web Server:** A free web server from Sun Microsystems that can run on Windows, Linux and Unix. It is well-equipped to handle medium to large websites.

# What’s a Proxy Server ?
A forward proxy, often called a proxy, proxy server, or web proxy, is a server that sits in front of a group of client machines. When those computers make requests to sites and services on the Internet, the proxy server intercepts those requests and then communicates with web servers on behalf of those clients, like a middleman.

For example, let’s name 3 computers involved in a typical forward proxy communication:
* A: This is a user’s home computer
* B: This is a forward proxy server
* C: This is a website’s origin server (where the website data is stored)

![Forward Proxy](/images/webserver/forwardproxy.JPG)

There are a few reasons one might want to use a forward proxy:
* To avoid state or institutional browsing restrictions
* To block access to certain content
* To protect their identity online

# What’s a Reverse Proxy Server ?

A reverse proxy is a server that sits in front of one or more web servers, intercepting requests from clients. This is different from a forward proxy, where the proxy sits in front of the clients. With a reverse proxy, when clients send requests to the origin server of a website, those requests are intercepted at the network edge by the reverse proxy server. The reverse proxy server will then send requests to and receive responses from the origin server.

The difference between a forward and reverse proxy is subtle but important. A simplified way to sum it up would be to say that a forward proxy sits in front of a client and ensures that no origin server ever communicates directly with that specific client. On the other hand, a reverse proxy sits in front of an origin server and ensures that no client ever communicates directly with that origin server.

Once again, let’s illustrate by naming the computers involved:
* D: Any number of users’ home computers
* E: This is a reverse proxy server
* F: One or more origin servers 

![Reverse Proxy](/images/webserver/reverseproxy.JPG)

Below we outline some of the benefits of a reverse proxy:
* Load balancing
* Protection from attacks
* Global Server Load Balancing (GSLB)
* Caching and Web acceleration
* SSL encryption


# Reverse Proxy versus Load Balancer

Reverse proxy servers and load balancers are components in a client-server computing architecture. Both act as intermediaries in the communication between the clients and servers, performing functions that improve efficiency. They can be implemented as dedicated, purpose-built devices, but increasingly in modern web architectures they are software applications that run on commodity hardware.

The basic definitions are simple:

* A **Reverse Proxy** accepts a request from a client, forwards it to a server that can fulfill it, and returns the server’s response to the client.
* A **Load Balancer** distributes incoming client requests among a group of servers, in each case returning the response from the selected server to the appropriate client.

## Load Balancer

Load balancers are most commonly deployed when a site needs multiple servers because the volume of requests is too much for a single server to handle efficiently. Deploying multiple servers also eliminates a single point of failure, making the website more reliable. Most commonly, the servers all host the same content, and the load balancer’s job is to distribute the workload in a way that makes the best use of each server’s capacity, prevents overload on any server, and results in the fastest possible response to the client.

A load balancer can also enhance the user experience by reducing the number of error responses the client sees. It does this by detecting when servers go down, and diverting requests away from them to the other servers in the group. In the simplest implementation, the load balancer detects server health by intercepting error responses to regular requests. Application health checks are a more flexible and sophisticated method in which the load balancer sends separate health-check requests and requires a specified type of response to consider the server healthy.

Another useful function provided by some load balancers is session persistence, which means sending all requests from a particular client to the same server. Even though HTTP is stateless in theory, many applications must store state information just to provide their core functionality – think of the shopping basket on an e-commerce site. Such applications underperform or can even fail in a load-balanced environment, if the load balancer distributes requests in a user session to different servers instead of directing them all to the server that responded to the initial request.

## Reverse Proxy

Whereas deploying a load balancer makes sense only when you have multiple servers, it often makes sense to deploy a reverse proxy even with just one web server or application server. You can think of the reverse proxy as a website’s “public face.” Its address is the one advertised for the website, and it sits at the edge of the site’s network to accept requests from web browsers and mobile apps for the content hosted at the website. The benefits are two-fold:

* **Increased security** – No information about your backend servers is visible outside your internal network, so malicious clients cannot access them directly to exploit any vulnerabilities. Many reverse proxy servers include features that help protect backend servers from distributed denial-of-service (DDoS) attacks, for example by rejecting traffic from particular client IP addresses (blacklisting), or limiting the number of connections accepted from each client.
* **Increased scalability and flexibility** – Because clients see only the reverse proxy’s IP address, you are free to change the configuration of your backend infrastructure. This is particularly useful In a load-balanced environment, where you can scale the number of servers up and down to match fluctuations in traffic volume.

# What is Nginx ?

NGINX is an open source software for web serving, reverse proxying, caching, load balancing, media streaming, and more. It started out as a web server designed for maximum performance and stability. In addition to its HTTP server capabilities, NGINX can also function as a proxy server for email (IMAP, POP3, and SMTP) and a reverse proxy and load balancer for HTTP, TCP, and UDP servers.

## Nginx as a Web Server
The goal behind NGINX was to create the fastest web server around, and maintaining that excellence is still a central goal of the project. NGINX consistently beats Apache and other servers in benchmarks measuring web server performance. Since the original release of NGINX, however, websites have expanded from simple HTML pages to dynamic, multifaceted content. NGINX has grown along with it and now supports all the components of the modern Web, including WebSocket, HTTP/2, gRPC, and streaming of multiple video formats (HDS, HLS, RTMP, and others).

## Nginx Beyond Web Serving
Though NGINX became famous as the fastest web server, the scalable underlying architecture has proved ideal for many web tasks beyond serving content. Because it can handle a high volume of connections, NGINX is commonly used as a reverse proxy and load balancer to manage incoming traffic and distribute it to slower upstream servers – anything from legacy database servers to microservices.

NGINX also is frequently placed between clients and a second web server, to serve as an SSL/TLS terminator or web accelerator. Acting as an intermediary, NGINX efficiently handles tasks that might slow down your web server, such as negotiating SSL/TLS or compressing and caching content to improve performance. Dynamic sites, built using anything from Node.js to PHP, commonly deploy NGINX as a content cache and reverse proxy to reduce load on application servers and make the most effective use of the underlying hardware.


# What is HAProxy ?

HAProxy, which stands for High Availability Proxy, is a popular open source software TCP/HTTP Load Balancer and proxying solution which can be run on Linux, macOS, and FreeBSD. Its most common use is to improve the performance and reliability of a server environment by distributing the workload across multiple servers (e.g. web, application, database)

![High Availability Setup](/images/webserver/ha-diagram-animated.gif)

Check existing container images on host.
```bash
[nilesh@localhost nginx-basic-playbook]$ sudo podman images
REPOSITORY               TAG         IMAGE ID      CREATED        SIZE
localhost/centosst8_ssh  latest      3037d4f05e3f  4 weeks ago    645 MB
localhost/centos8_ssh    latest      450951eb12aa  7 weeks ago    575 MB
quay.io/centos/centos    stream8     c5c90f59f34b  2 months ago   403 MB
quay.io/centos/centos    centos8     300e315adb2f  17 months ago  217 MB
```

Check existing containers
```bash
[nilesh@localhost haproxy-project]$ sudo podman ps -a
CONTAINER ID  IMAGE       COMMAND     CREATED     STATUS      PORTS       NAMES
```

Create containers for our lab.
3 x Nginx containers for backend servers
1 x HAProxy container
1 x Client container 

```bash
[nilesh@localhost ansibleplaybooks]$ sudo podman run --privileged --name Nginx-1 -d -p 8081:8080 localhost/centosst8_ssh
ada20c4d9de4c8db32b1a1a61191858113aa229958ffc1791663b1485071cc99
[nilesh@localhost ansibleplaybooks]$ sudo podman run --privileged --name Nginx-2 -d -p 8082:8080 localhost/centosst8_ssh
31c3bd3d3e3f124a1718dbd4f8fd1d1c5cb99a67ac6bbb1c8ad24cff37af507d
[nilesh@localhost ansibleplaybooks]$ sudo podman run --privileged --name Nginx-3 -d -p 8083:8080 localhost/centosst8_ssh
d22e75c41df9d0e7f16b4822aa2e43bc0d4634ce9caf99df671a815dd192cc4f
[nilesh@localhost ansibleplaybooks]$ sudo podman run --privileged --name HAProxy -d -p 8084:80 localhost/centosst8_ssh
5d8a58bf07b1e84309592138550caa9f025c1d562c2f98387d0719e49906dca5
[nilesh@localhost ansibleplaybooks]$ sudo podman run --privileged --name Client -d localhost/centosst8_ssh
22ca01f5f47767a610b8fb58a3f5f970ed088670ac3db20a9ffc1519f171ae06
```

Check running conatiners.

```bash
[nilesh@localhost ansibleplaybooks]$ sudo podman ps
CONTAINER ID  IMAGE                           COMMAND         CREATED        STATUS            PORTS                   NAMES
ada20c4d9de4  localhost/centosst8_ssh:latest  /usr/sbin/init  4 minutes ago  Up 4 minutes ago  0.0.0.0:8081->8080/tcp  Nginx-1
31c3bd3d3e3f  localhost/centosst8_ssh:latest  /usr/sbin/init  3 minutes ago  Up 3 minutes ago  0.0.0.0:8082->8080/tcp  Nginx-2
d22e75c41df9  localhost/centosst8_ssh:latest  /usr/sbin/init  3 minutes ago  Up 3 minutes ago  0.0.0.0:8083->8080/tcp  Nginx-3
5d8a58bf07b1  localhost/centosst8_ssh:latest  /usr/sbin/init  3 minutes ago  Up 2 minutes ago  0.0.0.0:8084->80/tcp    HAProxy
22ca01f5f477  localhost/centosst8_ssh:latest  /usr/sbin/init  2 minutes ago  Up 2 minutes ago                          Client
```

Checking the IP addresses of the conatiners.
```bash
[nilesh@localhost ansibleplaybooks]$ sudo podman inspect --format='{{.NetworkSettings.IPAddress}}' Nginx-1
10.88.0.2
[nilesh@localhost ansibleplaybooks]$ sudo podman inspect --format='{{.NetworkSettings.IPAddress}}' Nginx-2
10.88.0.3
[nilesh@localhost ansibleplaybooks]$ sudo podman inspect --format='{{.NetworkSettings.IPAddress}}' Nginx-3
10.88.0.4
[nilesh@localhost ansibleplaybooks]$ sudo podman inspect --format='{{.NetworkSettings.IPAddress}}' HAProxy
10.88.0.5
[nilesh@localhost ansibleplaybooks]$ sudo podman inspect --format='{{.NetworkSettings.IPAddress}}' Client
10.88.0.6
```

Adding entry for the containers ip addresses to the hosts file

```bash
[nilesh@localhost ~]$ sudo vi /etc/hosts
[nilesh@localhost ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.23.101 R1
192.168.23.102 R2
192.168.23.103 R3
10.88.0.2 Nginx-1
10.88.0.3 Nginx-2
10.88.0.4 Nginx-3
10.88.0.5 HAProxy
10.88.0.6 Client
```




# References
1. https://www.nginx.com/resources/glossary/nginx/
1. https://www.techtarget.com/whatis/definition/Web-server
1. https://www.cloudflare.com/en-in/learning/cdn/glossary/reverse-proxy/
1. https://www.digitalocean.com/community/tutorials/an-introduction-to-haproxy-and-load-balancing-concepts
