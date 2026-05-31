# **P02 – Guia tècnica**

## **Creació d’una Màquina Virtual Ubuntu Server (LTS)**

***

## **1. Objectiu de l’activitat**

L’objectiu d’aquesta activitat és crear una **màquina virtual Ubuntu Server LTS** que servirà com a **base estàndard** per a la resta del curs, garantint:

* Homogeneïtat de configuració
* Rapidesa en futurs desplegaments
* Reproductibilitat mitjançant una OVA
* Bones pràctiques de documentació en entorns de sistemes

***

## **2. Entorn de treball**

* **Hipervisor**: VirtualBox (o equivalent)
* **Sistema operatiu convidat**: Ubuntu Server **LTS més recent**
* **Format final de lliurament**: Fitxer **OVA**

***

## **3. Requisits de la màquina virtual**

| Component      | Configuració requerida |
| -------------- | ---------------------- |
| Memòria RAM    | 4 GB                   |
| Disc dur       | 20 GB                  |
| CPU            | Valor per defecte      |
| Xarxa          | 2 adaptadors           |
| Usuari inicial | `usuari`               |
| Contrasenya    | `usuari`               |
| Servei         | SSH instal·lat         |

### **Configuració de xarxa**

* **Adaptador 1**: NAT
* **Adaptador 2**: Pont (Bridge)
  * IP estàtica: `192.168.c.x/24`
  * `c = 2` → 2n A
  * `c = 4` → 2n B
  * `x` = número de llista

***

## **4. Creació de la màquina virtual**

1. Obrir l’hipervisor.
2. Crear una nova màquina virtual:
   * Nom: `UbuntuServer_P02`
   * Tipus: Linux
   * Versió: Ubuntu (64-bit)
3. Assignar recursos:
   * **RAM**: 4096 MB
   * **Disc**: 20 GB (VDI, dinàmic)
4. Afegir els adaptadors de xarxa:
   * Adaptador 1 → NAT
   * Adaptador 2 → Pont (interfície de xarxa física)

***

## **5. Instal·lació d’Ubuntu Server**

1. Arrencar la VM amb la ISO d’Ubuntu Server LTS.
2. Seleccionar:
   * Idioma
   * Zona horària
   * Teclat adequat
3. Configuració de xarxa:
   * DHCP temporal durant la instal·lació
4. Emmagatzematge:
   * Ús del disc complet
   * Particionat automàtic
5. Crear l’usuari:
   * Usuari: `usuari`
   * Contrasenya: `usuari`
   * ⚠️ **No utilitzar cap altre nom**
6. Paquets addicionals:
   * **Seleccionar “Install OpenSSH Server”**
7. Finalitzar instal·lació i reiniciar.

***

## **6. Configuració de xarxa estàtica**

Un cop iniciat el sistema:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

### Exemple de configuració:

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: no
      addresses:
        - 192.168.c.x/24
      gateway4: 192.168.c.1
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
```

Aplicar canvis:

```bash
sudo netplan apply
```

Comprovar:

```bash
ip a
```

***

## **7. Verificació del servei SSH**

1. Comprovar estat:

```bash
sudo systemctl status ssh
```

2. Si no està actiu:

```bash
sudo systemctl enable --now ssh
```

3. Prova de connexió (des d’un altre equip):

```bash
ssh usuari@192.168.c.x
```

***

## **8. Validacions finals**

Abans de generar la OVA, verificar:

* RAM = 4 GB
* Disc = 20 GB
* 2 adaptadors de xarxa
* IP correcta configurada
* Usuari i password correctes
* Servei SSH funcional

***

## **9. Exportació a OVA**

1. Apagar la màquina virtual.
2. Menú → Exporta appliance.
3. Seleccionar la VM.
4. Format: **OVA**
5. Nom del fitxer:
   ```
   UbuntuServer_P02_usuari.ova
   ```
6. Exportar i verificar que la OVA arrenca correctament.

***

## **10. Conclusions**

Aquesta màquina virtual constitueix una **plantilla base reutilitzable** per al desplegament ràpid de servidors Linux durant el curs. La correcta documentació del procés garanteix:

* Reducció d’errors futurs
* Facilitat de manteniment
* Compliment de criteris d’avaluació

