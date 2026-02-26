[TOC]

# UD2.AA1. Servei DHCP

<img src="img/AA1-TeoriaDHCP_0.tiff" width=250px />

**0227 Serveis de xarxa**

CFGM SMX
Carlos Alonso Martínez

`carlos.martinez@mataro.epiaedu.cat`

v20250805

## Continguts

Servei DHCP

Característiques funcionament DHCP

Configuració client DHCP

Adreces automàtiques (zero conf)

Servidor DHCP

DHCP relay

Atacs protocol DHCP

Contramesures

## Servei DHCP (RFC 2131)

* __D__ ynamic  __H__ ost  __C__ onfiguration  __P__ rotocol.
* Model client/servidor.
* Assignació als clients dels paràmetres de xarxa:
  * adreça
  * màscara de subxarxa
  * porta d’enllaç
  * servidor de noms

## DHCP vs configuració manual

* Avantatges:
  * __Zero configuracions__  per part dels usuaris.
  * __Minimització errors __ a la configuració de xarxa dels equips.
  * __Facilitat de manteniment__ : un canvi d’adreça, màscara o DNS es realitza directament al servidor.
  * Permet  __reutilitzar__  adreces (exemple WiFi d’un bar).
* Inconvenient:
  * Cal assegurar el funcionament del servei DHCP (alta disponibilitat).

## Tipus d’assignació

__Estàtica o manual__ : l’adreça IP es reserva a un equip concret usant l’adreça MAC del client.

__Dinàmica__ : el servidor ofereix una adreça al client per un temps limitat (lease time).

__Permanent__ : l’adreça assignada al client queda permanentment associada ( lease time il·limitat). És una solució que permet el protocol però és molt poc utilitzada.

## Detalls del protocol

* Ports:
  * Servidor 67 UDP
  * <span style="color:#1c8703"> __Client 68 UDP__ </span>
* El client utilitza un  _well_  _ _  _kwown_  _ port _ herència del protocol de [bootstrap](https://es.wikipedia.org/wiki/Protocolo_de_arranque). Es necessita un port conegut perquè el client encara no té configuració i necessita saber a on enviar i a on rebrà els missatges.
* A una mateixa xarxa poden conviure més d’un servidor DHCP.

<span style="color:#002060">Els </span>  <span style="color:#002060">well</span>  <span style="color:#002060"> </span>  <span style="color:#002060">known</span>  <span style="color:#002060"> ports són els ports originalment compresos entre el 0 i el 1023 i que l’IANA va reservar per serveis específics, tot i que avui la llista ha anat creixent. Es troben definits a la </span>  <span style="color:#002060">[RFC 1060](https://datatracker.ietf.org/doc/html/rfc1060)</span>

## Negociació DHCP

<img src="img/AA1-TeoriaDHCP_1.tiff" width=250px />

<span style="color:#002060">El trànsit de client s’envia per broadcast local per tal d’arribar a tots els possibles servidors.</span>

<span style="color:#002060">Les respostes del server s’envien per broadcast perquè el client no té IP encara.</span>

El client utilitza com  __adreça inicial __ 0.0.0.0.

__El DHCP __  __offer__  __ __ s’envia com broadcast.

Per renovar el temps de leasing, el client envia un  __DHCP __  __request__  __ __ per fer el renew de la concessió.

Normalment els clients, envien el renew bastant abans d’acabar el lease time (per exemple, els clients Windows renoven la concessió al 50% del lease time).

## Negociació DHCP i II

Per alliberar l’adreça de la que disposa, el client envia un  __DHCP release__ .

El  __DHCP __  __nack__  __ __ l’envia el servidor per indicar que no li queden adreces lliures o que s’ha produït un error.

El  __DHCP __  __decline__  __ __ avisa al server que el client ha detectat que l’adreça que se li ha assignat ja està en ús.

## Configuració client

Configurar el client perquè funcioni per DHCP, és molt senzill (per defecte els SO ja ho disposen així).

<img src="img/AA1-TeoriaDHCP_2.png" width=250px />

<img src="img/AA1-TeoriaDHCP_3.png" width=250px />

## Configuració client automàtica

* Anem per una situació força habitual:
  * Què passa si el client està configurat per obtenir una adreça de forma automàtica, però no hi ha server DHCP disponible?

<img src="img/AA1-TeoriaDHCP_4.tiff" width=250px />

<span style="color:#002060">És la situació que us podeu trobar si canvieu una VM de NAT a adaptador pont abans d’editar la configuració de xarxa.</span>

## Autoconfiguració: zero conf

La IETF va definir un mètode d’assignació d’adreces privades automàtiques.

APIPA (Automatic Private IP Addressing) és la implementació per Windows o  _Avahi_  en sistemes Linux.

Especifica una adreça aleatòria dins la subxarxa 169.254.0.0/16.

<span style="color:#1c8703"> __Només permet connectivitat local!!__ </span>

Les IP duplicades es detecten al moment de la publicació (Gratuitous ARP)

<img src="img/AA1-TeoriaDHCP_5.png" width=250px />

<span style="color:#002060">És per això que a una xarxa Windows (grup de treball) el equips es poden comunicar entre sí encara que no s’hagi configurat adreça i no existeixi cap servidor DHCP.</span>

## Servidors DHCP

* Per disposar d’un servidor DHCP a la nostra xarxa disposem de diverses opcions:
  * __Routers__ : els routers d’accés a Internet disposen d’un servidor DHCP integrat.
  * __Switch L3__ : els switches més avançats també disposen d’aquest servei.
  * __Servidors__ : Windows Server i Linux poden oferir també el servei DHCP.


* En un servidor DHCP tindrem les següents opcions:
  * <span style="color:#1c8703"> __Marge d’adreces (__ </span>  <span style="color:#1c8703"> __range__ </span>  <span style="color:#1c8703"> __ o pool)__ </span> : conjunt d’adreces que s’oferiran.
  * Configuració porta enllaç i servei de noms.
  * <span style="color:#1c8703"> __Temps de concessió __ </span> per les assignacions dinàmiques.
  * Llistes per fer assignacions estàtiques.

* Funcions addicionals:
  * Creació d’àmbits per gestionar diverses xarxes.
  * Interfícies per les que accepta peticions DHCP.
  * Configuracions avançades:
    * Configuració de servei horari, proxy, servidors correu...
  * Configuració DHCP per IPv6.

<img src="img/AA1-TeoriaDHCP_6.png" width=250px />

<img src="img/AA1-TeoriaDHCP_7.png" width=250px />

<img src="img/AA1-TeoriaDHCP_8.png" width=250px />

## DHCP relay

És un intermediari que rep les peticions DHCP dels clients i les redirigeix cap el servidor.

Poder usar un sol servidor DHCP per diverses xarxes.

Reducció del trànsit en el segment de xarxa del servidor (les peticions seran unicast enlloc de broadcast).

<img src="img/AA1-TeoriaDHCP_9.png" width=250px />

## Atacs protocol DHCP

* El protocol DHCP és sensible a una sèrie d’atacs:
  * DHCP starvation
  * DHCP spoofing
    * DHCP rogue
    * DHCP ACK injection

<img src="img/AA1-TeoriaDHCP_10.tiff" width=250px />

## DHCP starvation

Inundar de peticions DHCP falses el servidor, de manera que no quedin adreces lliures a la xarxa.

Sol ser el primer pas per fer un atac de DHCP Rogue.

<img src="img/AA1-TeoriaDHCP_11.png" width=250px />

<span style="color:#002060">L’atacant llença moltes peticions amb </span>  <span style="color:#002060">MACs</span>  <span style="color:#002060"> diferents</span>

<span style="color:#1c8703">imatge de </span>  <span style="color:#1c8703">http</span>  <span style="color:#1c8703">://</span>  <span style="color:#1c8703">www.lijyyh.com</span>  <span style="color:#1c8703">/ </span>

## DHCP rogue

Servidor il·legítim de DHCP que fem servir per configurar els equips de la xarxa.

L’atacant usa aquesta tècnica per modificar la configuració dels clients: MitM, DNS spoofing.

<img src="img/AA1-TeoriaDHCP_12.tiff" width=250px />

<span style="color:#1c8703">imatge de </span>  <span style="color:#1c8703">hebryantadvantage.com</span>

## DHCP ACK injection

Interceptem els paquets request dels clients i enviem el ACK amb la configuració modificada.

El client fa cas al primer ACK rebut.

Funciona si es respon més ràpid que el server.

<img src="img/AA1-TeoriaDHCP_13.tiff" width=250px />

<span style="color:#002060">Font: un </span>  <span style="color:#002060">inform</span>  <span style="color:#002060">ático en el lado del mal.</span>

## Contramesures

Eines de monitorització i detecció de servidors de DHCP no autoritzats utilitzant eines d’anàlisi del trànsit com snort, Suricata o similars.

<span style="color:#1c8703"> __DHCP __ </span>  <span style="color:#1c8703"> __Snooping__ </span> : <span style="color:#ff6600"> __ __ </span> el switch bloqueja els missatges de servidor en els ports marcats con untrusted (tots menys els dels servidor DHCP).

## DHCP Snooping

<img src="img/AA1-TeoriaDHCP_14.tiff" width=250px />

<span style="color:#1c8703">imatge de </span>  <span style="color:#1c8703">Wiki</span>  <span style="color:#1c8703"> IES </span>  <span style="color:#1c8703">Har</span>  <span style="color:#1c8703">ía</span>  <span style="color:#1c8703">.</span>

## Autoria i llicenciament

* Aquests materials han estat elaborats per:
  * Carlos Alonso Martínez

<img src="img/AA1-TeoriaDHCP_15.tiff" width=250px />

<span style="color:#002060"> __(CC BY-NC-ND 4.0)__ </span>
