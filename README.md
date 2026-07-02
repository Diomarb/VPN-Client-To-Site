# VPN-Client-To-Site# README
Link de Youtube: https://youtu.be/vi-UODJGL14 

## Descripción 

Este laboratorio implementa dos VPN de acceso remoto (Client-to-Site) sobre un firewall FortiGate: una **SSL-VPN en modo túnel** y una **IPsec VPN en modalidad Dialup**. Ambas permiten que un usuario remoto, mediante el cliente FortiClient, se conecte de forma cifrada a la red LAN interna (`10.11.85.0/26`) donde reside el host PC1.

## Topología resumida

- **R1 (router):** enlaza el cliente Windows/SW1 con el FortiGate.
- **FortiGate:** `port1` = WAN (hacia R1) · `port2` = Admin WebTerm · `port3` = LAN (hacia PC1).
- **Admin WebTerm:** usado para administrar el FortiGate vía HTTPS.
- **PC1 (VPCS):** host destino dentro de la LAN interna.
- **Cliente Windows:** ejecuta FortiClient para las conexiones SSL-VPN e IPsec.
  
<img width="370" height="248" alt="Captura de pantalla 2026-07-02 174949" src="https://github.com/user-attachments/assets/57861d02-5d8b-4bb3-b7c8-38b1495b43b7" />

<img width="355" height="231" alt="Captura de pantalla 2026-07-02 181345" src="https://github.com/user-attachments/assets/f2cd809e-74d1-4c0b-99f8-7a782c9b8099" />


## Direccionamiento rápido

| Campo | Valor |
|---|---|
| FortiGate port1 (WAN) | `10.11.85.65 /30` |
| FortiGate port2 (Admin) | `10.11.85.129 /26` |
| FortiGate port3 (LAN) | `10.11.85.1 /26` |
| R1 Eth0/1 / Eth0/0 | `10.11.85.66 /30` · `10.11.85.69 /30` |
| PC1 | `10.11.85.2 /26` |
| Pool SSL-VPN | `10.11.85.192 – 10.11.85.206 /28` |
| Pool IPsec Dialup | `172.16.14.10 – 172.16.14.50 /24` |

## Requisitos previos

- Escenario montado en GNS3/EVE-NG con R1, FortiGate, Admin WebTerm y PC1 (VPCS).
- Cliente Windows con acceso a Internet para descargar FortiClient VPN (versión free): https://www.fortinet.com/support/product-downloads
- Navegador (Firefox/Chrome) en la Admin WebTerm para acceder a la GUI del FortiGate.

1. Aplicar direccionamiento IP en el orden: **R1 → FortiGate → PC1 → Admin WebTerm** (ver configuraciones CLI en la documentación técnica).
2. Entrar a la GUI del FortiGate desde la Admin WebTerm: `https://10.11.85.129` (usuario `admin`).
3. Crear el usuario local `euni` y el grupo `GRP-VPN-SSL` en *User & Authentication*.
4. Configurar *SSL-VPN Settings* (puerto `10443`, certificado `Fortinet_Factory`, pool `10.11.85.192–206`).
5. Habilitar *Tunnel Mode* y *Split Tunneling* en el portal `full-access`, con ruta `10.11.85.0/26`.
6. Crear la política de firewall `SSL-VPN-to-LAN` (`ssl.root` → `port3`, NAT desactivado).
7. Instalar FortiClient en Windows y crear el perfil `VPN-ITLA` apuntando a `10.11.85.65:10443`.
8. Conectar con usuario `eunice` / contraseña `ITLA2026`.
9. Para IPsec: correr el *IPsec Wizard* (`vpn_client_site`, tipo *Remote Access*, *FortiClient*), ajustar Fase 1 (IKEv2, Aggressive, DES, SHA1, DH14, Local ID `VPN_ITLA`) y configurar el mismo perfil en FortiClient.

## Credenciales utilizadas en el laboratorio

| Campo | Valor |
|---|---|
| Usuario GUI FortiGate | `admin` |
| Usuario SSL-VPN | `eunice` / `ITLA2026` |
| Usuario local creado | `euni` / `ITLA2024` |
| Grupo VPN | `GRP-VPN-SSL` / `grp-vpn-ssl` |
| Local ID IPsec | `VPN_ITLA` |

## Comandos clave

**Admin WebTerm**
```bash
ip addr add 10.11.85.130/26 dev eth0
ip link set eth0 up
ip route add default via 10.11.85.129
```

**Cliente Windows**
```cmd
netsh interface ip set address name="Ethernet" static 10.11.85.70 255.255.255.252 10.11.85.69
```

## Verificación

- Ping entre R1 y FortiGate (`10.11.85.65` ↔ `10.11.85.66`).
- Conectar FortiClient (SSL-VPN o IPsec) y confirmar la IP virtual asignada.
- Hacer ping desde el cliente remoto hacia PC1 (`10.11.85.2`).
- Revisar *VPN → Monitor* (SSL-VPN / IPsec Monitor) en el FortiGate para confirmar el túnel activo.

<img width="338" height="257" alt="Captura de pantalla 2026-07-02 172956" src="https://github.com/user-attachments/assets/58decb93-b231-4dd5-b599-485918c670cc" />

<img width="227" height="152" alt="Captura de pantalla 2026-07-02 172929" src="https://github.com/user-attachments/assets/ce44e6a6-d19b-4248-b539-f3bb11078e93" />

<img width="500" height="332" alt="Captura de pantalla 2026-07-02 173052" src="https://github.com/user-attachments/assets/229ee558-cdcb-4f8e-b24f-90a44ec2e01f" />

<img width="233" height="140" alt="Captura de pantalla 2026-07-02 174235" src="https://github.com/user-attachments/assets/1645cb76-1a1b-4958-90a3-6c4ed026d743" />

<img width="294" height="268" alt="Captura de pantalla 2026-07-02 173414" src="https://github.com/user-attachments/assets/6090c2bc-3811-4ce5-9d50-8eb7a9ab4560" />

<img width="358" height="38" alt="Captura de pantalla 2026-07-02 173505" src="https://github.com/user-attachments/assets/05fccfcc-c9c4-47ce-90b6-be28d741af4a" />

<img width="448" height="295" alt="Captura de pantalla 2026-07-02 173530" src="https://github.com/user-attachments/assets/0659464c-3ce8-4117-994b-ba044b942768" />

