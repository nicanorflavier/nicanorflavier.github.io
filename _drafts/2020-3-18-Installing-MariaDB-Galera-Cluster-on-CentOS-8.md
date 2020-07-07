---
layout: post
title: Installing MariaDB Galera Cluster on CentOS 8
permalink: /blog/:title

---
Installing MariaDB Galera Cluster on CentOS 8


<div class="alert alert-warning" role="alert">
WARNING: Unable to resolve package source ‘https://www.powershellgallery.com/api/v2’
</div>

Found out that the proxy server is not allowing to install the module,. looks like a TLS problem.

<div>Solution: Run the PowerShell command below as Administrator</div>
```bash
[Net.ServicePointManager]::SecurityProtocol = "tls12"
```
<div class="alert alert-info" role="alert">
If you like the article, please comment and or share the article, remember sharing is caring.
</div>
