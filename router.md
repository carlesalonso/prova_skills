# Apunts de Xarxes: Router (Encaminador)

## 1. Funcionament bàsic d’un router

Un **router** (encaminador) és un dispositiu de **Capa 3 (Xarxa)** que connecta xarxes diferents i decideix per on han de passar els paquets IP.

Funcions principals:

- Llegir la IP de destí del paquet.
- Consultar la **taula d’enrutament**.
- Triar la millor ruta segons prefix, mètrica i protocols.
- Reencapsular la trama de Capa 2 segons la interfície de sortida.
- Separar dominis de *broadcast*.

Flux simplificat:

1. Rep una trama Ethernet.
2. Desencapsula fins al paquet IP.
3. Busca la millor coincidència (*longest prefix match*).
4. Envia el paquet per la interfície adequada.

---

## 2. Enrutament estàtic i dinàmic

### 2.1 Enrutament estàtic

Rutes configurades manualment per l’administrador.

**Avantatges:**

- Control total de la ruta.
- Menys consum de CPU i ample de banda (no intercanvia actualitzacions).
- Més simple en xarxes petites o molt estables.

**Inconvenients:**

- Escala malament en xarxes grans.
- Si canvia la topologia, cal reconfiguració manual.
- Més risc d’errors humans.

### 2.2 Enrutament dinàmic

Rutes apreses automàticament amb protocols (RIP, OSPF, etc.).

**Avantatges:**

- S’adapta automàticament a canvis i caigudes d’enllaços.
- Millor escalabilitat.
- Menys manteniment manual en entorns grans.

**Inconvenients:**

- Més complexitat de configuració.
- Consumeix recursos i tràfic de control.
- Pot tenir convergència lenta segons el protocol.

### 2.3 Comparativa ràpida

| Tipus | Configuració | Escalabilitat | Reacció a canvis | Recursos |
| :--- | :--- | :--- | :--- | :--- |
| Estàtic | Manual | Baixa | Manual | Baix |
| Dinàmic | Automàtica | Alta | Automàtica | Mitjà/Alt |

---

## 3. RIP, OSPF i concepte de mètrica

La **mètrica** és el valor que usa un protocol per decidir quina ruta és millor.

- Mètrica baixa = ruta preferida (habitualment).
- Pot representar salts, cost, amplada de banda, retard, etc.

### RIP (Routing Information Protocol)

- Protocol dinàmic de tipus **distance-vector**.
- Mètrica: **nombre de salts (hop count)**.
- Màxim 15 salts (16 = inassolible).
- Simple, però limitat per a xarxes grans.

### OSPF (Open Shortest Path First)

- Protocol dinàmic de tipus **link-state**.
- Calcula rutes amb l’algorisme SPF (Dijkstra).
- Mètrica: **cost** (normalment derivat de l’amplada de banda).
- Convergència ràpida i bona escalabilitat.

> **Consell:** En entorns petits RIP pot ser suficient, però en xarxes mitjanes o grans OSPF és habitualment més eficient i robust.

---

## 4. Accés a Internet

### 4.1 Breu introducció històrica

- **Mòdem analògic (dial-up):** connexió per línia telefònica RTC, velocitats molt baixes i ocupació de la línia de veu.
- **ADSL:** usa el parell de coure telefònic amb més velocitat i separació de veu/dades.
- **Cable (HFC):** accés per xarxa de televisió per cable, bones velocitats i gran implantació urbana.

### 4.2 Actualitat: FTTH (Fiber To The Home)

La fibra arriba directament fins a casa de l’usuari.

Característiques bàsiques:

- Velocitats molt altes (simètriques o quasi simètriques).
- Baixa latència.
- Alta estabilitat i menor sensibilitat a interferències elèctriques.
- Millor preparació per serveis actuals (streaming 4K, teletreball, jocs en línia, núvol).

---

## 5. Accés sense fils: WiMAX, satèl·lit i dades mòbils

### WiMAX

- Tecnologia d’accés sense fils d’àrea metropolitana.
- Bona cobertura en zones àmplies.
- Menys habitual avui en entorns domèstics que la fibra o 4G/5G.

### Satèl·lit

- Útil en zones rurals o remotes sense infraestructura terrestre.
- Cobertura molt extensa.
- Inconvenients: latència més alta (especialment GEO) i cost superior.

### Dades mòbils (4G/5G)

- Accés a Internet via xarxa cel·lular.
- Molt flexible i mòbil (telèfons, routers 4G/5G, IoT).
- El 5G millora velocitat, capacitat i latència.
- Pot dependre de cobertura, congestió i límits de dades.

---

## 6. Exemple de configuració bàsica de ruta estàtica (Cisco)

```cisco
Router> enable
Router# configure terminal
! Ruta cap a la xarxa 192.168.20.0/24 passant pel següent salt 10.0.0.2
Router(config)# ip route 192.168.20.0 255.255.255.0 10.0.0.2
Router(config)# end
Router# write memory
```

> `ip route` defineix: xarxa destí + màscara + *next hop* (o interfície de sortida).
