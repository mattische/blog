---
title: "Ssh Tunneling"
date: 2017-11-20T13:57:59+01:00
---

<pre>Sorry, this is in swedish only</pre>

# Reverse SSH tunneling /Remote port forwarding

## Problem:
du kan inte komma åt en dator utifrån (den befinner sig förmodligen bakom ett NAT (Network Address Translator).

## Lösning:
skapa en ssh tunnel FRÅN oåtkomlig maskin via en åtkomlig maskin.

## Krävs:
SSH deamon (sshd) och SSH klient på alla maskinerna.

## Konfiguration:
På bägge maskinerna krävs en SSH-server (ssh-deamon).
Lägg till 2 attribut sist i sshd_config:

<code>$ sudo nano /etc/ssh/sshd_config</code>

<pre><code class="bash">
GatewayPorts yes 
ClientAliveInterval 60

</code></pre>

<code>GatewayPorts</code> - för att man ska kunna tunnla.
<code>ClientAliveInterval</code> - för att undvika att kopplingen/tunnlingen stängs ned/bryts efter inaktivitet lägg till följande i samma 
där värdet 60 anger interval i sekunder som dummy-paket skickas för att upprätthålla kopplingen.


Man kan dessutom lägga till motsvarande i SSH klienten för att upprätthålla kopplingen till en server.
Följande gäller för alla användare:

<code>$ sudo nano /etc/ssh/ssh_config</code>

Följande gäller endast för berörd användare:

<code>$ sudo nano ~/.ssh/config</code>

lägg till följande:

<code>ServerAliveInterval 60</code>


Logga in på den maskin som du inte kommer åt och skapa en reverse tunnel till den åtkomliga maskinen.
På detta sätt kan du komma åt den oåtkomliga maskinen via den åtkomliga.

Exempel:
Du vill komma åt maskinen <code>194.47.151.45</code> utanför det lokala nätet (t ex hemifrån) och logga in via SSH på den.
Du har tillgång till en server med IP <code>91.123.200.138</code>.
Eller så kanske du sitter och utvecklar en webb-app på din lokala maskin och vill låta andra kolla på vad du gjort.

På bägge dessa maskiner har du alltså ssh-server och klient installerade (och konfigurerade enligt ovan).
Logga in på 194.47.151.45 och kör:

<kbd>$ ssh -nNT -R 2222:localhost:22 user@91.123.200.138</kbd>

-nNT gör att en tty inte skapas (man skapar inte ett shell) samt att ett kommando inte körs.
-R skapar en reverse tunnel
2222 är porten på 91.123.200.138 som tunnlas till port:
22 på 194.47.151.45, dvs SSH.
localhost är alltså ur serverns (91.123.200.138) perspektiv, inte 194.47.151.45

På en annan maskin kan du nu:

<kbd>$ ssh user@91.123.200.138 -p2222</kbd>

och du ansluts alltså till 194.47.151.45.
(tänk på att user ovan ska vara en user på 194.47.151.45)


Local SSH tunneling / Local port forwarding

Konfigurera SSH-server och klient enligt ovan.
Du har åtkomst till en server 91.123.200.138.

Problemet kanske är att vissa portar är spärrade på din maskin. Eller så vill du kanske bara förenkla saker.
Du kanske har en databas som du vill komma åt via en annan maskin.

Scenario 1 - du vill komma åt den spärrade siten imgur.com:

<code>$ ssh -nNT -L 8080:imgur.com:80 user@91.123.200.138</code>

i en webbläsare: http://localhost:8080
-L talar om att vi gör en lokal tunnel (local port forwarding)
Vår lokala port 8080 på localhost, tunnlas till imgur.com och port 80.

Scenario 2 - exponera vår lokala databas (med shell-access eller för att kunna ansluta via en app). Vi har t ex mysql-server lokalt som då har port 3306.

<code>$ ssh -nNT -L 9999:localhost:3306 user@91.123.200.138</code>

Vi kör alltså kommandot på maskinen där databasen finns - den maskinens lokala port 3306 är nu åtkomlig via servern (91.123.200.138) på port 9999.

<code>$ mysql -h 91.123.200.138 -u databaseUser -p databasePass --port 9999</code>


Övrigt
För att begränsa ssh-åtkomst till vissa användare på en maskin, lägg till tillåtna användare i /etc/ssh/sshd_config:
<code>
#Only accept connections from specified users
AllowUsers user1 user2 user3
</code>
