---
title: "🔍 Metodología Bug Bounty — Reconocimiento"
aliases:
  - Metodología Bug Bounty - Reconocimiento
  - Reconocimiento Bug Bounty
  - Recon Bug Bounty
  - Metodología de Reconocimiento
  - 02 - Reconocimiento y Herramientas Bug Bounty
tags:
  - ifct0109
  - hacking-etico
  - recon
  - bug-bounty
  - subfinder
  - httpx
  - nuclei
  - katana
  - dns
  - metodologias
---

# 🔍 Metodología Bug Bounty - Reconocimiento

> [!abstract] Información de la Guía
> **Fase:** Reconocimiento y Enumeración  
> **Descripción:** Guía completa de reconocimiento para programas de Bug Bounty, desde la identificación del scope hasta el análisis automatizado de vulnerabilidades.  
> **Herramientas:** Guía para la instalación y catálogo de las herramientas necesarias en el [[02 - MODULOS/Hacking Ético/Herramientas de Reconocimiento Bug Bounty|enlace]].  
> 
> 🔗 **Navegación:** [[02 - MODULOS/04 - Hacking Ético|⬅️ Hacking Ético]] &emsp;|&emsp; [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|🌐 Web Fuzzing]] &emsp;|&emsp; [[02 - MODULOS/Hacking Ético/Herramientas de Reconocimiento Bug Bounty|🛠️ Herramientas Recon]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## 1. Elegir programa BugBounty

Crear archivo `00_scope.txt` con scopes dentro.

Crear archivo `01_domains_scope.txt` con los dominios.

---

## 2. Buscar información de la empresa y sitio web

```bash
httpx -l 01_domains_scope.txt -status-code -cdn -content-length -title -web-server -location -tech-detect -follow-redirects -random-agent -threads 25 -silent -o 02_httpx.txt
```

```bash
whatweb -i 01_domains_scope.txt --user-agent='BugBounty' --aggression 1 --log-brief=02_whatweb_resumen.txt
```

```bash
whatweb -i 01_domains_scope.txt --user-agent='BugBounty' --aggression 1 -v --log-verbose=02_whatweb_verbose.txt
```

```bash
wafw00f -i 01_domains_scope.txt -o 02_wafw00f.txt
```

```bash
cdncheck -i 01_domains_scope.txt -resp -cdn -waf -silent -o 02_cdncheck.txt
```

---

## 3. Ver IPs asociadas al dominio

### Listado de IPs

```bash
dnsx -l 01_domains_scope.txt -a -resp-only -silent | sort | uniq > 03_domains_ips.txt && cat 03_domains_ips.txt
```

### Información de cada IP

```bash
cat 03_domains_ips.txt | while read ip; do echo "=== $ip ==="; curl -s https://ipinfo.io/$ip; echo ""; done > 03_domains_ipinfo.txt && cat 03_domains_ipinfo.txt
```

Cuántas webs hay alojadas en esa IP y otra información: [https://viewdns.info/](https://viewdns.info/)

---

## 4. Centrarnos en un dominio (Google Dorks)

```bash
cat 01_domains_scope.txt | while read domain; do dorks_hunter.py -d $domain ; done >> 04_dorks.txt && cat 04_dorks.txt
```

Recurso adicional: [https://karthikdude.github.io/bug-bounty-dorks/](https://karthikdude.github.io/bug-bounty-dorks/)

---

## 5. Enumerar subdominios

Recurso Online [Subdomain Finder](https://subdomainfinder.c99.nl/) y guardar los subdominios como `05_all_subdomains.txt`.

### Enumeración Pasiva (subfinder y crt)

```bash
subfinder -dL 01_domains_scope.txt -all -silent -o 05_subfinder_pasive.txt
```

```bash
cat 01_domains_scope.txt | while read domain; do crt -s -json -l 50 $domain | jq -r '.[].subdomain'; done > 05_crt_pasive.txt
```

### Enumeración Activa (shuffledns)

```bash
wget https://raw.githubusercontent.com/trickest/resolvers/refs/heads/main/resolvers.txt
```

```bash
cat 01_domains_scope.txt | while read domain; do shuffledns -d $domain -r resolvers.txt -w domains_dict/bitquark-subdomains-top100000.txt -mode bruteforce -silent; done >> 05_shuffledns_active.txt
```

### Generador de subdominios (alterx)

```bash
cat 01_domains_scope.txt | alterx | tee -a 05_alterx.txt
```

### Unir todos los subdominios

```bash
cat 05_subfinder_pasive.txt 05_crt_pasive.txt 05_shuffledns_active.txt 05_alterx.txt | sort | uniq > 05_all_subdomains.txt
```

---

## 6. Comprobar validez de subdominios

```bash
wget https://raw.githubusercontent.com/trickest/resolvers/refs/heads/main/resolvers.txt
```

```bash
wget https://raw.githubusercontent.com/trickest/resolvers/refs/heads/main/resolvers-trusted.txt
```

Comenzamos comprobando los que tenemos y creando una lista de válidos:

```bash
puredns resolve 05_all_subdomains.txt -r resolvers.txt --resolvers-trusted resolvers-trusted.txt -w 06_validos_01.txt
```

Y ahora por fuerza bruta, sacamos más válidos:

```bash
cat 01_domains_scope.txt | while read domain; do puredns bruteforce domains_dict/subdomains-top1million-110000.txt $domain -r resolvers.txt --resolvers-trusted resolvers-trusted.txt; done >> 06_validos_02_bf.txt
```

```bash
cat 06_validos_01.txt 06_validos_02_bf.txt | sort | uniq > 06_all_validos.txt
```

---

## 7. Protección de dominios

Extraemos todas las IPs:

```bash
cat 06_all_validos.txt | dnsx -recon -json -resp | jq -r 'try .a' | grep -oE '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' | sort -u > 07_hosts.txt
```

```bash
wafw00f -i 06_all_validos.txt -o 07_wafw00f.txt
```

```bash
wafw00f -i 07_hosts.txt -o 07_wafw00f_ips.txt
```

```bash
cdncheck -i 06_all_validos.txt -resp -cdn -waf -silent -o 07_cdncheck.txt
```

```bash
cdncheck -i 07_hosts.txt -resp -cdn -waf -silent -o 07_cdncheck_ips.txt
```

---

## 8. Escanear puertos

```bash
naabu -l 06_all_validos.txt -top-ports 100 -o 08_all_ports.txt
```

```bash
nmap -iL 06_all_validos.txt --top-ports 100 --open --max-retries 2 -oX 08_all_ports_nmap.xml
```

```bash
nmapurls -f 08_all_ports_nmap.xml > 08_all_webs.txt
```

Revisamos ahora los servicios:

```bash
cat 08_all_ports.txt | while read line; do host=$(echo $line | cut -d: -f1); port=$(echo $line | cut -d: -f2); echo ""; echo "=== Scanning $line ==="; echo "" >> 08_port_services.txt; echo "=== Scanning $line ===" >> 08_port_services.txt; nmap -sV -p $port $host | tee -a 08_port_services.txt; done
```

---

## 9. Análisis de la WEB

```bash
cat 06_all_validos.txt | httpx -status-code -cdn -content-length -title -web-server -location -tech-detect -follow-redirects -random-agent -threads 25 -silent -o 09_all_tech.txt
```

---

## 10. Pantallazos a las WEB

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

```bash
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

```bash
sudo apt --fix-broken install
```

```bash
gowitness scan file -f 08_all_webs.txt --write-db
```

Y las vemos creando un servidor WEB:

```bash
python3 -m http.server 8000
```

Acceso al sqlite3 creado:

```bash
gowitness report list --db-uri sqlite://gowitness.sqlite3
```

```bash
gowitness report list --db-uri sqlite://gowitness.sqlite3 > 10_witness.txt
```

---

## 11. Recolectar URLs

```bash
urlfinder -d 06_all_validos.txt -o 11_urls_pasive.txt
```

```bash
katana -list 06_all_validos.txt -jc -kf all -d 2 -fs rdn -o 11_urls_active.txt
```

Juntamos todas:

```bash
cat 11_urls_pasive.txt 11_urls_active.txt | sort | uniq > 11_all_urls.txt
```

Y tratamos de eliminar duplicados:

```bash
urless -i 11_all_urls.txt -o 11_all_end.txt
```

Miramos también con fuzzing:

```bash
cat 01_domains_scope.txt | while read domain; do ffuf -mc all -fc 404 -r -c -w Web-Content/burp-parameter-names.txt -u https://$domain/FUZZ; done > 11_urls_fuzzing.txt
```

Y los archivos:

```bash
cat 06_all_validos.txt | gau --o 11_urls_gau.txt
```

---

## 12. Análisis de URLs

### gf-patterns

```bash
gf -list
```

```bash
echo " " > 12_gf_results.txt
echo "=== sqli ===" >> 12_gf_results.txt && cat 11_all_end.txt | gf sqli >> 12_gf_results.txt
echo " " >> 12_gf_results.txt
echo "=== xss ===" >> 12_gf_results.txt && cat 11_all_end.txt | gf xss >> 12_gf_results.txt
echo " " >> 12_gf_results.txt
echo "=== rce ===" >> 12_gf_results.txt && cat 11_all_end.txt | gf rce >> 12_gf_results.txt
echo " " >> 12_gf_results.txt
echo "=== lfi ===" >> 12_gf_results.txt && cat 11_all_end.txt | gf lfi >> 12_gf_results.txt
echo " " >> 12_gf_results.txt
echo "=== idor ===" >> 12_gf_results.txt && cat 11_all_end.txt | gf idor >> 12_gf_results.txt
echo " " >> 12_gf_results.txt
echo "=== redirect ===" >> 12_gf_results.txt && cat 11_all_end.txt | gf redirect >> 12_gf_results.txt
echo " " >> 12_gf_results.txt
echo "=== ssrf ===" >> 12_gf_results.txt && cat 11_all_end.txt | gf ssrf >> 12_gf_results.txt
echo " " >> 12_gf_results.txt
echo "=== allparam ===" >> 12_gf_results.txt && cat 11_all_end.txt | gf allparam >> 12_gf_results.txt
echo " " >> 12_gf_results.txt
echo "=== secret-urls ===" >> 12_gf_results.txt && cat 11_all_end.txt | gf secret-urls >> 12_gf_results.txt
```

### Wayback

```bash
cat 01_domains_scope.txt | waybackurls | sort -u >> 12_waybackdata.txt | gf ssrf | tee -a 12_ssfrparams.txt
```

```bash
cat 12_waybackdata.txt | gf redirect | tee -a 12_redirect.txt
```

### Nuclei

```bash
cat 11_all_end.txt | nuclei -severity info
```

```bash
nuclei -list 11_all_end.txt -dast -retries 2 -silent -o 12_nuclei_results.txt
```

```bash
nuclei -list 12_gf_results.txt -dast -retries 2 -silent -o 12_nuclei_gf_results.txt
```

---

## 13. Scripts Automatizados

### Virustotal

```bash
virustotal.sh 07_hosts.txt
```

### Wayback

```bash
wayback.sh example.org
```

### Alienvault

```bash
alienvault.sh example.org
```

```bash
cat 06_all_validos.txt | while read domain; do alienvault.sh $domain; done | grep http | tee -a 13_alienvault.txt
```

```bash
cat 13_alienvault.txt | gf params | uro | nuclei -dast
```

### LostFuzzer

```bash
mkdir lostfuzzer
cd lostfuzzer
lostfuzzer.sh -d https://example.org -l ../06_all_validos.txt
find ./ -name "nuclei*.txt" -exec ls -al {} \;
```

### gf_script

```bash
gf_critical_script.sh
```

> [!warning] Patrones Críticos (Critical patterns)
> `"sqli"` `"sqli-error"` `"xss"` `"ssrf"` `"rce"` `"rce-2"` `"lfi"` `"xxe"` `"ssti"` `"idor"` `"redirect"` `"jwt"` `"secrets"` `"api-keys"` `"aws-keys"` `"aws-keys_secrets"` `"github"` `"github_secrets"` `"firebase"` `"firebase_secrets"` `"endpoints"` `"interestingparams"` `"debug-pages"` `"debug_logic"` `"js-sinks"` `"domxss"` `"s3-buckets"` `"cors"` `"takeovers"` `"upload-fields"`

```bash
gf_all_script.sh
```

---

> [!note] 📝 Resumen Oficial
> Esta metodología cubre el proceso completo de reconocimiento para Bug Bounty, desde la definición del scope hasta el análisis automatizado de vulnerabilidades. Cada paso genera archivos numerados (`00_` a `13_`) para mantener un flujo organizado y trazable del proceso de investigación.
