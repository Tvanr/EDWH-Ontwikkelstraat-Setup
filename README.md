# EDWH-Ontwikkelstraat-Setup
een tekst bestand die een beginnende gebruiker uit moet leggen hoe de ontwikkelstraat geïnstalleerd wordt.

## Stap 1: benodigdheding installeren
1. open de terminal (`CTRL+ALT+T`)
1. `sudo apt install python3.12 python3.12-venv pyton3-pip`
1. `sudo apt install git`
1. `pipx install uv`
2.  Als hier een melding komt die je vraagt `pipx ensurepath` uit te voeren doe dat dan.
1. `pipx install uvenv`
1. `uv tool install edwh`
1. `uv tool install virtualenv`
1. `edwh plugin.add all`
1. `edwh mp.install` om multipass te installeren. multipass is een VM programma

## Stap 2: github SSH key koppelen
ga naar github maak een ssh key en zet de public key in github zodat je straks in de private repository's kan downloaden
https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

## Stap 3: download de ontwikkelstraat en reverse_proxy bestanden via de SSH links
ga naar https://github.com/educationwarehouse/ontwikkelstraat,<br> 
klik op de groene knop "code" klik deze aan dan krijg je 3 opties https, SSH of Github CLI<br>
klik op SSH en kopieer de SSH link.<br>
ga naar de terminal en typ: `git clone <githubSSHlink>`<br>
doe dit ook voor https://github.com/educationwarehouse/reverse_proxy/ <br>
controlleer of dit gelukt is door in de home folder het commando `ls` uit te voeren. Dit laat alle bestanden zien. En je zou dan ook de net geinstalleerde mappen moeten zien.

## stap 4:VM maken
`multipass launch docker --name dockers` <br>
hier mee maak je een VM genaamd dockers. <br> 
deze VM heeft een speciale ubuntu versie.<br>
de VM heet dockers omdat hier de dockers draaien.

## Stap 5:VM aanpassen
de VM moet aangepast worden om de omgeving te kunnen draaien. <br>
`multipass stop dockers` om de VM te stoppen<br>
pas de VM  aan zodat deze meer cpu cores, disk en RAM geheugen heeft:<br>
`multipass set local.dockers.cpus=4`<br>
`multipass set local.dockers.disk=100G`<br>
`multipass set local.dockers.memory=4G`<br>
`multipass start dockers` om de VM weer te starten

## Stap 6:VM benaderbaar maken via SSH
`edwh mp.prepare dockers`

## Stap 7:DNS-fix en EDWH tool installeren op VM
`multipass list` om te kijken of de VM draait.<br>
`edwh mp.fix-dns dockers -h delen.dockers.local -h py4web.dockers.local -h web2py.dockers.local -h`om de bereikbare links van de VM aan te geven<br>
`edwh -H ubuntu@delen.dockers.local remote.prepare-generic-server` om de EDWH tool te installeren op de VM<br>
werkt dit niet gebruik dan:`edwh -H ubuntu@<ipadres> -i ~/.ssh/multipass.key remote.prepare-generic-server`<br>
werkt dit wel voeg dan vanaf nu `-i ~/.ssh/multipass.key` toe aan elk commando.

## Stap 8: werken vanaf de VM
vanaf nu gaan we verbinden met de multipass VM met 
`multipass shell dockers`<br>
`multipass exit dockers` om hier later weer uit te komen.<br>
**de shell met de naam ubuntu@dockers is de VM en zal vanaf nu zo genoemd worden.<br>
open een nieuwe shell dit is de host.**


## stap 9:Mappen vanaf de host zichtbaar maken voor de dockersVM
vanaf de host in de home/~ map.<br>
`edwh mp.mount ontwikkelstraat -m dockers`<br>
`edwh mp.mount reverse_proxy -m dockers`<br>
ga naar de VM en en controlleer of de 2 mappen ontwikkelstraat en reverse_proxy er in staan met ll.

## Stap 10 :Omgeving configureren.
vanaf de VM:<br>
`cd ontwikkelstraat/`
zo kom je in de ontwikkelstraat map<br>
`ew setup` nu komt een setup(.env word nu gemaakt)<br>
verander de:<br>
HOSTDOMAIN = dockers.local<br>
APLICATION_NAME = delen<br>
CERTRESOLVER = default<br>
voor de rest is default/enter goed 

## Stap 11 :migrate
ga naar /ontwikkelstraat in de VM<br>
`ew up` om de ontwikkelstraat omgeving te starten.<br>
`ew migrate` om de database klaar te zetten

## Stap 12: reverse proxy setup
ga naar de /reverse_proxy map<br>
`ew setup`<br>
`ew up`

## Stap 13: check
ga in de VM naar de map ontwikkelstraat/<br>
`ew ps`
controlleer of de docker instanties running zijn.<br>
ga in de VM naar de reverse_proxy/ map<br>
`ew ps`
als het goed is is er 1 docker running

## Stap 14: verbinden 
ga naar http://delen.dockers.local in je internetbrowser om te bekijken of je een website hebt.<br>
hier krijg je een foutmelding: Sorry, we konden niks vinden.<br>
dit is omdat de database van je omgeving nog leeg is.

## Stap 15: database vullen
ga naar https://educationwarehouse.org/, ga naar collectives en in de kring eddy's staat Database recovery URL's daar staat een devdb, <br>volg de stappen.
check op http://delen.dockers.local of de bestanden op het scherm zichtbaar zijn

## De omgeving is succesvol geinstalleerd.
