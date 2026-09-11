# El switch: funcionament, VLAN i STP

## Objectius

En acabar aquesta unitat, hauràs de ser capaç de:

- Explicar la funció d'un switch en una xarxa local (LAN).
- Diferenciar els principals tipus de switch.
- Configurar VLAN bàsiques i ports d'accés.
- Entendre per què cal el protocol STP (*Spanning Tree Protocol*).

## 1. Què és un switch?

Un **switch** és un dispositiu de xarxa que connecta equips dins d'una LAN. Treballa principalment a la **capa 2, Enllaç de dades**, del model OSI i reenvia trames (*frames*) segons l'adreça MAC de destinació.

A diferència d'un hub, un switch no envia totes les trames a tots els ports. Decideix a quin port les ha d'enviar, fet que redueix el trànsit innecessari i millora el rendiment de la xarxa.

### Taula d'adreces MAC

El switch manté una **taula MAC** (també anomenada taula CAM) amb la relació entre les adreces MAC apreses i els seus ports.

| Acció | Com actua el switch |
| --- | --- |
| Rep una trama | Llegeix la MAC origen i l'associa al port d'entrada. |
| Coneix la MAC destinació | Reenvia la trama només pel port corresponent. |
| No coneix la MAC destinació | Envia la trama per tots els ports de la mateixa VLAN, excepte el d'entrada. Aquest procés s'anomena *flooding*. |
| La MAC destinació és al mateix port | No reenvia la trama. |

> La taula MAC és dinàmica: les entrades caduquen després d'un temps si no es tornen a utilitzar.

### Dominis de col·lisió i de difusió

Cada port d'un switch és un **domini de col·lisió** independent. Per tant, dos equips connectats a ports diferents poden comunicar-se simultàniament.

En canvi, sense VLAN, tots els ports formen part del mateix **domini de difusió** (*broadcast domain*). Les trames de difusió, com les peticions ARP, arriben a tots els equips connectats al switch.

## 2. Tipus de switch

| Tipus | Característiques | Ús habitual |
| --- | --- | --- |
| No gestionable (*unmanaged*) | No permet configuració. Funciona en connectar-lo. | Xarxes domèstiques o molt petites. |
| Gestionable (*managed*) | Permet configurar VLAN, STP, seguretat, monitoratge i altres funcions. | Empreses, centres educatius i xarxes professionals. |
| Intel·ligent (*smart*) | Ofereix configuració limitada, normalment des d'una interfície web. | Petites empreses. |
| Capa 2 | Commutació basada en MAC. | Accés d'equips finals. |
| Capa 3 | També pot fer encaminament entre VLAN mitjançant IP. | Xarxes grans o nuclis de xarxa. |
| PoE (*Power over Ethernet*) | Alimenta dispositius a través del cable Ethernet. | Punts d'accés, telèfons IP i càmeres IP. |

## 3. VLAN

Una **VLAN** (*Virtual Local Area Network*) divideix lògicament un switch físic en diverses xarxes independents. Cada VLAN és un domini de difusió diferent.

Per exemple, es poden separar els ordinadors de l'alumnat, el professorat i l'administració encara que estiguin connectats al mateix switch.

| VLAN | Nom | Equips |
| --- | --- | --- |
| 10 | ALUMNAT | Ordinadors de l'alumnat |
| 20 | PROFESSORAT | Ordinadors del professorat |
| 30 | ADMINISTRACIO | Equips de gestió |

### Ports d'accés i ports troncals

- **Port d'accés** (*access port*): connecta un equip final i pertany a una única VLAN.
- **Port troncal** (*trunk port*): connecta switches, routers o punts d'accés i transporta trànsit de diverses VLAN. Utilitza habitualment l'estàndard IEEE 802.1Q per identificar cada trama amb l'etiqueta de VLAN.

Els equips de VLAN diferents no es comuniquen directament. Per comunicar VLANs cal un dispositiu de capa 3, com ara un router o un switch de capa 3. Aquest procés s'anomena **encaminament inter-VLAN**.

### Exemple de configuració bàsica Cisco IOS

L'exemple crea les VLAN 10 i 20, assigna dos ports a cadascuna i configura un enllaç troncal cap a un altre switch.

```cisco
enable
configure terminal

vlan 10
 name ALUMNAT
vlan 20
 name PROFESSORAT

interface range fastEthernet 0/1-2
 switchport mode access
 switchport access vlan 10

interface range fastEthernet 0/3-4
 switchport mode access
 switchport access vlan 20

interface gigabitEthernet 0/1
 switchport mode trunk

end
show vlan brief
```

> Comprova sempre que els dos extrems d'un enllaç troncal transporten les mateixes VLAN necessàries.

## 4. STP: per què és necessari?

L'**STP** (*Spanning Tree Protocol*, IEEE 802.1D) evita els bucles de capa 2. Un bucle apareix, per exemple, quan dos switches estan units per més d'un cable per tenir redundància.

La redundància és útil si un cable falla, però sense STP les trames de difusió poden circular indefinidament. Això provoca una **tempesta de difusió** (*broadcast storm*), duplica trames i fa que la taula MAC sigui inestable. El resultat pot ser la caiguda de tota la LAN.

STP selecciona un switch com a **pont arrel** (*root bridge*) i bloqueja alguns ports redundants. Així queda un únic camí actiu entre dos punts de la xarxa. Si l'enllaç actiu falla, STP pot activar un camí alternatiu.

| Concepte | Funció |
| --- | --- |
| Root bridge | Switch de referència escollit per STP. |
| Port arrel (*root port*) | Millor camí des d'un switch cap al root bridge. |
| Port designat | Port que reenvia trames en un segment. |
| Port bloquejat | Port redundant que no reenvia trames per evitar bucles. |

Les variants habituals són **RSTP** (*Rapid STP*, IEEE 802.1w), que convergeix més ràpidament, i **MSTP** (*Multiple STP*, IEEE 802.1s), que pot gestionar grups de VLAN amb arbres diferents.

## 5. Exercici pràctic: separar dos departaments amb VLAN

**Supòsit:** tens un switch gestionable amb quatre ordinadors. Els ports `Fa0/1` i `Fa0/2` han de ser de l'alumnat, i `Fa0/3` i `Fa0/4`, del professorat.

1. Crea la VLAN 10 amb el nom `ALUMNAT` i la VLAN 20 amb el nom `PROFESSORAT`.
2. Assigna `Fa0/1` i `Fa0/2` a la VLAN 10.
3. Assigna `Fa0/3` i `Fa0/4` a la VLAN 20.
4. Verifica la configuració amb la comanda següent:

```cisco
show vlan brief
```

**Resultat esperat:** els equips de la mateixa VLAN es poden comunicar entre ells. Els equips de VLAN diferents necessiten encaminament inter-VLAN per comunicar-se.

## 6. Resum

- El switch aprèn adreces MAC i reenvia trames només quan és necessari.
- Els switches gestionables permeten configurar VLAN, STP i mesures de seguretat.
- Les VLAN segmenten la xarxa en dominis de difusió independents.
- STP manté la redundància física sense crear bucles de capa 2.
