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

[Vaccine](https://app.hackthebox.com/starting-point/#vaccine)

<div class="mermaid">
graph LR
    A[nmap] --> B[FTP]
    A --> C[http]
    A --> D[ssh]
    B --> E[get zip] --> F[zip2john] --> G[john] --> H([admin/pass]) 
    C --> I[login <br/> admin/pass] --> J["sqlmap --os-shell <br/> dashboard?search"] --> K[bash reverse shell] --> L["look in <br/> /var/www/html"] --> M([postgres/pass])
    D --> N[postgres/pass] --> P["sudo -l"] --> Q["sudo vi"] --> R[GTFOBins] --> S(["root ⬛"])
</div>


