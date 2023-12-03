# How to achieve running with subdomain urls in local
Usually when you launch your webserver on your PC, you access your web application with localhost in url.
In this application's case I want to access each sub-application with different urls. 
I want to replicate how I can use nginx to serve two separate applications
via separate subdomains.

Basically you map ```127.0.0.1``` to subdomains in ```C:\Windows\System32\drivers\etc\hosts``` file. In the case of macOS or Linux the file is ```/etc/hosts```

For instance I added lines like below

```bash
127.0.0.1 jspanda.local
127.0.0.1 family-spending.local
```

After editing the hosts file, to flush and reflect changes on Windows

```bash
ipconfig /flushdns
```

