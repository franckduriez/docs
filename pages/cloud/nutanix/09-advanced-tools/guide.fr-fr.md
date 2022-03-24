---
title: Outils avancés
slug: advanced-tools
excerpt: "Présentation de divers outils avancés"
section: Premiers pas
order: 09
---

**Dernière mise à jour le 17/03/2022**

## Objectif

Présenter l'ensemble des outils d'administrations autre que les interfaces WEB  de **Prism Central** et **Prim Element** qui sont :

* ncli accessible sur un poste local, en SSH sur **Prism Central** et **Prism Element**.
* acli utilisable en ssh sur les **CVM**.
* Cmdlets pour **Powershell**.
* Commande **restapi** à partir de l'URL de **Prism Central** ou **Prism Element**


> [!warning]
> OVHcloud vous met à disposition des services dont la configuration, la gestion et la responsabilité vous incombent. Il vous appartient donc de ce fait d’en assurer le bon fonctionnement.
>
> Ce guide a pour but de vous accompagner au mieux sur des tâches courantes. Néanmoins, nous vous recommandons de faire appel à un prestataire spécialisé si vous éprouvez des difficultés ou des doutes concernant l’administration, l’utilisation ou la mise en place d’un service sur un serveur.
>

## Prérequis

- Disposer d'un cluster Nutanix dans votre compte OVHcloud
- Être connecté à votre [espace client OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.com/fr/&ovhSubsidiary=fr)
- Certaines commandes ne sont utilisables que si vous avez accès à **Prism Central** et **Prism Element** en ssh



## Présentation des outils

### Présentation de **ncli**

**ncli** se trouve sur **Prism Central** et sur toutes les **CVM** au travers d'une connexion **ssh**, il est aussi possible de l'installer en local sur un poste Windows ou Linux à partir de l'interface Web de **Prism Central**.

**ncli** permet de gérer le cluster Nutanix comme le stockage, les tâches et certaines actions sur les machines virtuelles à l'interieur du CLUSTER.

### Informations sur **acli**

La commmande acli est uniquement disponible sur les **CVM** elle est orientée sur la gestion des hotes des machines virtuelles, des résaux et des snapshots. 

### Extensions **Powershell** 

Powershell est un langage de script qui a été développé par Microsoft et qui se base sur **Net.Framework**. 

Il fonctionne dans les environnements **Microsoft**, **Linux** et **MacOS** mais il est principalement utilisé dans un environnement **Microsoft** et tous les exemples cités ont été testé uniquement sur cet environnement.

Nutanix a rajouté des extensions qui permettent l'administration du cluster et des VM en utilisant des scripts POWERSHELL.


### Interface d'administration **REST API**

Au travers de l'URL de **Prism central** ou **Prism Element** il est possible d'utiliser un api nommée **REST API** que l'on peut soit utiliser en ligne de commande avec l'outil **curl** ou au travers d'un autre langage de programmation comme **python** ou **php** et même avec les commandes standards de **Powershell**.

Pour plus de détails sur ces commandes reportez-vous à la section « [Aller plus loin](#gofurther) » de ce guide.

## En pratique

### Exemples d'utilisation de **ncli**

Se connecter en **ssh** soit avec la commande **ssh** sous linux ou un outil sous Windows permettant une connexion SSH sur une des **CVM**

```bash
ssh nutanix@oneofcvm
```

ncli est utilisable en mode intéractif en lançant **ncli** sans option mais il est possible d'éxecuter **ncli** suivi des instructions sur la même ligne comme avec cette syntaxe ```ncli entitée action option1="valeur" option2="valeur2``` 

#### Création d'un **Storage Container** nommé newcontainer en mote interactif

```bash
ncli
<ncli> 
<ncli> ctr create name="NewContainer" sp-name="default-storage-pool-10728992352041"

    Id                        : 0005ce15-3f3c-a8ce-3802-043f72bf18a6::2900908
    Uuid                      : c3db4020-2a98-4636-b9eb-8ebf10a1b351
    Name                      : Moncontainer
    Storage Pool Id           : 0005ce15-3f3c-a8ce-3802-043f72bf18a6::9
    Storage Pool Uuid         : 111d4e91-10df-4f6c-a774-b79b53077131
    Free Space (Logical)      : 37.69 TiB (41,445,056,716,652 bytes)
    Used Space (Logical)      : 0 bytes
    Allowed Max Capacity      : 37.69 TiB (41,445,056,716,652 bytes)
    Used by other Containers  : 260.86 GiB (280,096,141,312 bytes)
    Explicit Reservation      : 0 bytes
    Thick Provisioned         : 0 bytes
    Replication Factor        : 2
    Oplog Replication Factor  : 2
    NFS Whitelist Inherited   : true
    Container NFS Whitelist   : 192.168.0.77/255.255.255.255
    VStore Name(s)            : Moncontainer
    Random I/O Pri Order      : SSD-PCIe, SSD-SATA, DAS-SATA
    Sequential I/O Pri Order  : SSD-PCIe, SSD-SATA, DAS-SATA
    Compression               : off
    Fingerprint On Write      : off
    On-Disk Dedup             : off
    Erasure Code              : off
    Software Encryption       : off
<ncli> 
```

#### Suppression du **Storage Container sans directement à partir le la **CVM**

Lancez la commande ci-dessous pour supprimer sans confirmation le **Storage Container**. 

```bash
ncli ctr remove name="Newcontainer"
```

### Exemples concernant **acli**

Se connecter en ssh soit avec la commande ssh sous linux ou un outil sous Windows permettant une connexion SSH sur une des **CVM**

```ssh nutanix@oneofcvmipaddress```

La commande **acli** est utilisable de manière interactive ou suivi des commandes à executer avec la syntaxe ci-dessous: 

```bash
acli entité.action pourqui option1="valeur" option2="valeur2
``` 

#### Création d'un **Snapshot** en mode interactif

```bash
acli 
<acropolis> vm.snapshot_create VM-TEST snapshot_name_list="Example"
<acropolis> exit
```
#### Affichage et suppression d'un **Snapshot** avec une commande complète sur la même ligne**

En mode console par défaut une confirmation est demandée lors d'un choix mais il est possible de lancer la commande **acli** avec une confirmation automatique du choix par défaut en ajoutant l'option -y après acli.

Saisissez ces commandes pour afficher et supprimer un snapshot avec une demande confirmation.

```bash
acli acli snapshot.list
Snapshot Name  Snapshot ID                           Creation Time                         VM Name
Example2       fce5483f-5a9d-4b76-9ad7-48685fb4a638  Wednesday March 23 2022, 09:27:16 AM  VM-TEST
acli snapshot.delete Example2
Delete 1 snapshots? (yes/no)
Delete 1 snapshots? (yes/no) yes
Example2: pending
Example2: complete
```

Utilisez cette syntaxe pour ne pas avoir de demandes de confirmation lors de la suppression du snapshot.

```bash
acli -y snapshot.delete Example2
Example2: pending
Example2: complete
```

### Configuration de **Powershell** sous Windows et utilisation pratique

#### Installation de **Powershell** 7 et des modules pour Nutanix

Il est nécessaire d'installer la dernière version de **Powershell** à partir ce lien [Lien vers installation Powershell](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.2#msi)

Téléchargez le programme d'installation qui correspond à votre version de Windows en 32 bits ou plus généralement en 64 bits. Installez le.

Lancez l'invite de Commande Powershell 7
.
```powershell
PS C:\Users\Administrator> Install-Module Nutanix.Cli

Untrusted repository
You are installing the modules from an untrusted repository. If you trust this repository, change its
InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
'PSGallery'?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):A
PS C:\Users\Administrator> Install-module Nutanix.Prism.PS.Cmds
PS C:\Users\Administrator> Install-module Nutanix.Prism.Common
```

#### Exemples de commandes avec Powershell pour Nutanix

Importez les modules pour Powershell dans la console **PowerShell 7**

```powershell
PS C:\Users\Administrator> Import-Module Nutanix.Cli -Prefix NTNX
PS C:\Users\Administrator> Import-Module Nutanix.Prism.Common -Prefix NTNX
PS C:\Users\Administrator> Import-Module Nutanix.Prism.PS.Cmds -Prefix NTNX
```

Connectez-vous à Prism Central.

```powershell
PS C:\Users\Administrator> Connect-NTNXPrismCentral ipprismcentral -UserName nomutilisateur -AcceptInvalidSSLCerts
Password: *****************
[Warning]: This Prism Central version[pc.2022.1] might not be compatible.This might cause some cmdlets to not function correctly.Please consider upgrading to pc.2020.7 or later Do you still want to continue [Y/N]?:Y

Server                : ipprismcentral
Version               : pc.2022.1
UserName              : nomutilisateur
AcceptInvalidSSLCerts : True
ForcedConnection      : False
```

Executez la commande ci-dessous pour afficher la liste de toutes les machines virtuelles

```powershell
PS C:\Users\Administrator> Get-NTNXvm

pcHostName                    : 192.168.0.222
vmId                          : 0005ce15-3f3c-a8ce-3802-043f72bf18a6::c8ef83db-a095-49f3-9bb8-cbcdc85c3ebf
uuid                          : c8ef83db-a095-49f3-9bb8-cbcdc85c3ebf
powerState                    : off
vmName                        : OVHgateway
guestOperatingSystem          :
ipAddresses                   : {}
hypervisorType                : kKvm
hostName                      :
hostId                        :
hostUuid                      :
containerIds                  : {0005ce15-3f3c-a8ce-3802-043f72bf18a6::728, 0005ce15-3f3c-a8ce-3802-043f72bf18a6::728}
containerUuids                : {98e67c38-77c4-4e05-bc20-b38bab5b28b2, 98e67c38-77c4-4e05-bc20-b38bab5b28b2}
nutanixVirtualDisks           : {/SelfServiceContainer/.acropolis/vmdisk/a7da0957-9f90-4c07-8d12-65f851350453,
                                /SelfServiceContainer/.acropolis/vmdisk/b308778d-594f-4903-bc30-c2a2fd45ed5d}
nutanixVirtualDiskIds         : {0005ce15-3f3c-a8ce-3802-043f72bf18a6::a7da0957-9f90-4c07-8d12-65f851350453,
                                0005ce15-3f3c-a8ce-3802-043f72bf18a6::b308778d-594f-4903-bc30-c2a2fd45ed5d}
nutanixVirtualDiskUuids       : {a7da0957-9f90-4c07-8d12-65f851350453, b308778d-594f-4903-bc30-c2a2fd45ed5d}
virtualNicIds                 : {0005ce15-3f3c-a8ce-3802-043f72bf18a6::c8ef83db-a095-49f3-9bb8-cbcdc85c3ebf:50:6b:8d:c5
                                :d4:8a, 0005ce15-3f3c-a8ce-3802-043f72bf18a6::c8ef83db-a095-49f3-9bb8-cbcdc85c3ebf:50:6
                                b:8d:e2:16:4e}
virtualNicUuids               : {1e8defc6-ea18-4d67-ba41-d3ae21047940, 537632dc-4db3-483e-a7bb-500d2c1e56ba}
clusterUuid                   : 0005ce15-3f3c-a8ce-3802-043f72bf18a6
virtualGpuUuids               :
memoryCapacityInBytes         : 1073741824
memoryReservedCapacityInBytes : 0
numVCpus                      : 1
cpuReservedInHz               :
numNetworkAdapters            : 2
controllerVm                  : False
controlDomain                 :
vdiskNames                    : {}
vdiskFilePaths                : {/SelfServiceContainer/.acropolis/vmdisk/a7da0957-9f90-4c07-8d12-65f851350453,
                                /SelfServiceContainer/.acropolis/vmdisk/b308778d-594f-4903-bc30-c2a2fd45ed5d}
diskCapacityInBytes           : 11811543040
displayable                   : False
acropolisVm                   : True
protectionDomainName          :
protectionType                :
consistencyGroupName          :
description                   :
runningOnNdfs                 : True
nonNdfsDetails                :
fingerPrintOnWrite            :
onDiskDedup                   :
gpusInUse                     : False
vmType                        :
```

Réutilisez la même commande suivie de d'un commande suplémentaire séparé par un |

```powershell
PS C:\Users\Administrator> Get-NTNXvm | ft vmname

vmName
------
vmfromrestapimajhttpd3
WS2022TEMPLATE
vmfromrestapiws
NTNX-221060034-A-CVM
VM-WS2019
gateway368
PFSENSE-POUR-MOVE
VM-TESTFG
WS2022b
prism-central
NutaDemo1
NTNX-520001155-A-CVM
NTNX-520001158-A-CVM
MOVE
NTNX-520000892-A-CVM
VM-WS2022
OVHgateway
```

Utilisez cet enchainement de commandes pour rechercher une machine virtuelle nommée VW-WS2022 et la supprimer

```powershell
PS C:\Users\Administrator> Get-NTNXVM | where-object {$_.Source.vmname -like "VM-WS2022" } | Remove-NTNXVM
```

### Utilisation des commandes **REST API**

Tous les exemples qui seront montrés ci-dessous seront fait à partir d'une console sous Linux avec l'outils **curl**, certaines commandes nécessiterons d'autres outils supplémentaires disponible dans l'environnement Linux.

#### Affichage de toutes le commandes à partir de **Prism central**

Il est possible d'avoir la liste des commandes **REST Api** à partir de **Prism Central**

Dans **Prism Central** en haut à droite cliquez sur `Username`{.action} et dans le menu cliquez sur `REST API Explorer`{.action}

![Display RESTAPI commands 01](images/Displayrestapicmds-01.png){.thumbnail}

La liste des commandes est affichée à gauche.

Cliquez sur `List operations`{.action} à droite d'une des familles de commandes pour faire apparaitre toutes les commandes possibles.  

![Display RESTAPI commands 02](images/Displayrestapicmds-02.png){.thumbnail}

Cliquez sur `Expand operations`{.action} pour faire lister la syntaxe d'une commande en particulier.

![Display RESTAPI commands 03](images/Displayrestapicmds-03.png){.thumbnail}

![Display RESTAPI commands 04](images/Displayrestapicmds-04.png){.thumbnail}

#### Divers exemples simples

Se connecter en SSH sur une VM linux du cluster qui a **curl** et **jq** d'installé

Pour que ces exemples fonctionnent remplacez prismcentralip par l'adresse IP ou le nom FQDN de **Prism central**, primcentraluser par l'utilisateur de **Prism central** et prismcentralpassword par le mot de passe de l'utilisateur de **Prism central**

##### Afficher la liste des images disponibles pour les déploiements de machines virtuelles.

Exécutez cette commande après avoir remplacé "prismcenraluser:password" par le nom d'utilisateur et le mot de passe 

```bash
curl -k -X POST --header "Content-Type: application/json" --header "Accept: application/json" -u "prismcentraluser:prismcentralpassword" -d {} "https://prismcentralip:9440/api/nutanix/v3/images/list" | jq
```

Cette commande a généré un fichier au format json lisible grace à la commande **jq** qui contient la liste des images et de quelques informations utiles que l'on pourrait avoir besoin.

```json
{
  "api_version": "3.1",
  "metadata": {
    "total_matches": 14,
    "kind": "image",
    "length": 14,
    "offset": 0
  },
  "entities": [
    {
      "status": {
        "state": "COMPLETE",
        "execution_context": {
          "task_uuid": [
            "3a4315c8-0f9e-4182-be89-9083371ef40e"
          ]
        },
        "name": "ubuntu-20.04.3-live-server-amd64.iso",
        "resources": {
          "retrieval_uri_list": [
            "https://127.0.0.1:9440/api/nutanix/v3/images/4a48f395-80f3-4afe-a3e4-91700db88901/file"
          ],
          "current_cluster_reference_list": [
            {
              "kind": "cluster",
              "uuid": "0005ce15-3f3c-a8ce-3802-043f72bf18a6"
            }
          ],
          "architecture": "X86_64",
          "size_bytes": 1261371392,
          "image_type": "ISO_IMAGE",
          "source_uri": "file://4a48f395-80f3-4afe-a3e4-91700db88901"
        },
        "description": "Ubuntu Server 20.04"
      },
      "spec": {
        "name": "ubuntu-20.04.3-live-server-amd64.iso",
        "resources": {
          "image_type": "ISO_IMAGE",
          "source_uri": "file://4a48f395-80f3-4afe-a3e4-91700db88901",
          "architecture": "X86_64"
        },
        "description": "Ubuntu Server 20.04"
      },
      "metadata": {
        "last_update_time": "2021-10-12T13:38:55Z",
        "kind": "image",
        "uuid": "4a48f395-80f3-4afe-a3e4-91700db88901",
        "creation_time": "2021-10-12T13:38:49Z",
        "spec_version": 2,
        "spec_hash": "00000000000000000000000000000000000000000000000000",
        "categories_mapping": {},
        "owner_reference": {
          "kind": "user",
          "uuid": "00000000-0000-0000-0000-000000000000",
          "name": "admin"
        },
        "categories": {}
      }
    },
```

##### Affichage de la liste des machines virtuelles

Executez cette commande pour afficher la liste des machines virtuelles

```bash
curl -k -X POST --header "Content-Type: application/json" --header "Accept: application/json" -u "prismcentraluser:prismcentralpassword" -d {} "https://prismcentralip:9440/api/nutanix/v3/vms/list" | jq 
```
Le résultat est toujours au format JSON

Ci-dessous une partie du fichier généré contenant l'UUID de la machine virtuelle qui est en fait son identifiant unique pour Nutanix, il existe un UUID pour toutes les éléments de Nutanix (VM, Vdisks, images, etc....)

"kind:" "vm" correspond au type de l'objet et uuid est son uuid

```json
        "kind": "vm",
        "uuid": "46574b90-333b-4cd9-a737-3af0f8e242b7",
```

##### Affichage des informations d'une machine virtuelle à partir de son uuid

Lancez cette commande en réutilisant l'UUID de l'exemple précedent

```bash
curl -k -X GET --header "Accept: application/json" - u "prismcentraluser:prismcentralpassword" "https://prismcentralip:9440/api/nutanix/v3/vms/46574b90-333b-4cd9-a737-3af0f8e242b7" | jq
```

##### Affichage la liste des réseaux

Lancez cette commande pour afficher la listes de réseaux dans Nutanix :

```bash
 curl -k -X POST --header "Content-Type: application/json" --header "Accept: application/json" -u "prismcentraluser:prismcentralpassword" -d {} "https://prismcentralip:9440/api/nutanix/v3/subnets/list" | jq
```
 
#### Affichage des informations sur les clusters administrées la console **Prism central**

Pour afficher les informations sur le cluster lancez cette commande.

```bash
curl -k --request POST --url "https://prismcentralip:9440/api/nutanix/v3/clusters/list" -u "prismcentraluser:prismcentralpassword" --header 'Content-Type: application/json' --data '{ }' | jq
```

#### Exemples avancées

En plus de pouvoir afficher des informations il est possible d'agir sur Nutanix pour créer ou modifier des éléments du cluster. 
 
Nous allons voir comment créer deux machines virtuelles de manière automatisé l'une sous Linux, l'autre sous Windows.

##### Création d'une machine virtuelle sous Linux

Il est possible d'installer Linux à partir d'images préinstallées et de personnaliser la configuration avec cloud-init qui utilise le format de fichiers yaml. 

Suivez ces instructions pour créer une VM à partir d'une image personnalisable avec cloud-init :

Sous Linux créer une mot passe au format SHA-512 avec la commande ```mkpasswd```

```bash
mkpasswd --method=SHA-512 -s
Password:
$6$q0hSUaxUNIzgF$4R6hbeVF7Nqz3JMUSI47vINSmwt3XufAIC1lvu15twR/8HMkuRIGd7ZNNLMDGYYGyrgZXwgI7q2BP2rCAv9BU1
```

Editer le fichier ```cloud-config.yaml```, remplacez < RSAKEYFORLOGING > par un clé RSA qui permettra de se connecter avec le compte userlinux en ssh au travers de cette clé. Remplacez < PASSWORDGENERATEDBYMKPASSWORD > par le mot de passe au format SHA-512 créé
  

```yaml
#cloud-config
hostname: vm-fromcloudinit
fqdn: vm-fromcloudinit.localdomain
users:
  - name: userlinux
    ssh-authorized-keys:
      - ssh-rsa < RSAKEYFORLOGIN >
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    groups: sudo
    shell: /bin/bash
    lock_passwd: false
    passwd: < PASSWORDGENERATEDBYMKPASSWD >

package_upgrade: true

packages:
  - nginx

runcmd:
  - sleep 60 & reboot
```

Le fichier au format **yaml** est créé il permettra l'installation d'une machine virtuelle sous LINUX avec le serveur WEB **NGINX** 

Transformez le fichier **yaml** au format **mime64** et stockez le dans une variable pour pouvoir l'intégrer dans un autre fichier.

```bash
USERDATA=$(base64 -w 0 cloud-config.yaml)
echo $USERDATA
```

Editez le fichier *vmlinux.json* ci-dessous en modifiant ces éléments pour l'adapter à votre environnement.

* **< VMNAME >** par le nom de la VM que vous voulez donner 
* **< UUID-IMAGE-LINUX-CLOUD-INIT >** par l'UUID de l'image LINUX compatible avec cloud-init
* **< UUID-NETWORK >** nom du réseau sur laquelle la machine virtuelle va fonctionner. 
* **< MIME64FORMATEDYAMLFILE >** doit être remplacé par le contenu de la variable ```$USERDATA```
* **< CLUSTER-NAME >** par le nom du cluster
* **< CLUSTER-UUID >** avec l'UUID du cluster  



Toutes les informations nécessaires sont soit accessibles depuis l'interface Web de **Prism central** ou au travers de commandes **REST API** 

```json
{
	"spec":{
		"name":"< VMNAME >",
		"resources":{
			"power_state":"ON",
			"num_vcpus_per_socket":1,
			"num_sockets":1,
			"memory_size_mib":1024,
			"disk_list":[{
				"device_properties":{
					"device_type":"DISK",
					"disk_address": {
						"device_index": 0,
						"adapter_type": "SCSI"
					}
				},
				"data_source_reference": {
					"kind": "image",
					"uuid": "< UUID-IMAGE-LINUX-CLOUD-INIT >"
				}
			},
			{
				"device_properties":{
					"device_type":"CDROM",
					"disk_address": {
                                                "device_index": 0,
                                                "adapter_type": "IDE"
					}
				}
			}],
			"nic_list":[{
				"nic_type":"NORMAL_NIC",
				"is_connected":true,
				"ip_endpoint_list":[{
					"ip_type":"DHCP"
				}],
				"subnet_reference":{
					"kind":"subnet",
					"name":"base",
					"uuid":"< UUID-NETWORK >"
				}
			}],
			"guest_tools":{
				"nutanix_guest_tools":{
					"state":"ENABLED",
					"iso_mount_state":"MOUNTED"
				}
			},
			"guest_customization": {
				"cloud_init": {
					"user_data": "< MIME64FORMATEDYAMLFILE >"
				},
				"is_overridable": false
			}
		},
		"cluster_reference":{
			"kind":"cluster",
			"name":"Cluster_368",
			"uuid":"0005ce15-3f3c-a8ce-3802-043f72bf18a6"
		}
	},
	"api_version":"3.1.0",
	"metadata":{
		"kind":"vm"
	}
}
```

Lancez cette commande :

```bash
curl -k -H Accept:application/json -H Content-Type:application/json -u "prismcentraluser:prismcentralpassword"  -X POST "https://prismcentralip:9440/api/nutanix/v3/vms" -d @vmlinux.json | jq .
```

La nouvelle VM va apparaitre dans **Prism Central** elle aura été mise à jours et NGINX sera installé

##### Création d'une machine virtuelle sous Windows

Il est possible d'installer des ordinateurs virtuels sous Windows à partir d'une image qui a été préparé pour pouvoir être dupliqué à l'aide la commande **sysprep** intégrée à Windows, et comme pour linux d'y appliquer un fichier de configuration.

Le fichier de configuration est un fichier au format **XML**, il est possible de créer ce fichier au travers des outils **Windows ADK** téléchargeables sur le site de **Microsoft** [Lien de téléchargement Windows ADK](https://docs.microsoft.com/en-us/windows-hardware/get-started/adk-install#download-the-adk-for-windows-11) et notamment **Windows system image manager**

Ci-dessous un exemple de fichier **XML** généré par l'outil de Microsoft qui crée un utilisateur admin avec un mot de passe et modifie le mot de passe administrateur.

```xml
<?xml version="1.0" encoding="utf-8"?>
<unattend xmlns="urn:schemas-microsoft-com:unattend">
    <settings pass="specialize">
        <component name="Microsoft-Windows-Shell-Setup" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <CopyProfile>true</CopyProfile>
        </component>
    </settings>
    <settings pass="windowsPE">
        <component name="Microsoft-Windows-International-Core-WinPE" processorArchitecture="wow64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <InputLocale>en-US</InputLocale>
            <SystemLocale>en-US</SystemLocale>
            <UILanguage>en-US</UILanguage>
            <UserLocale>en-US</UserLocale>
        </component>
        <component name="Microsoft-Windows-Setup" processorArchitecture="wow64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <UserData>
                <AcceptEula>true</AcceptEula>
                <FullName>Nutanix Doc</FullName>
                <Organization>Nutanix Doc</Organization>
            </UserData>
        </component>
    </settings>
    <settings pass="oobeSystem">
        <component name="Microsoft-Windows-Shell-Setup" processorArchitecture="wow64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <OOBE>
                <HideOnlineAccountScreens>true</HideOnlineAccountScreens>
                <ProtectYourPC>3</ProtectYourPC>
                <HideEULAPage>true</HideEULAPage>
                <HideLocalAccountScreen>true</HideLocalAccountScreen>
                <HideOEMRegistrationScreen>true</HideOEMRegistrationScreen>
                <HideWirelessSetupInOOBE>true</HideWirelessSetupInOOBE>
            </OOBE>
            <UserAccounts>
                <LocalAccounts>
                    <LocalAccount wcm:action="add">
                        <Password>
                            <Value>< CRYPTED PASSWORD ></Value>
                            <PlainText>false</PlainText>
                        </Password>
                        <Name>admin</Name>
                        <Group>Administrators</Group>
                    </LocalAccount>
                </LocalAccounts>
            </UserAccounts>
        </component>
    </settings>
    <cpi:offlineImage cpi:source="wim:c:/source/sources/install.wim#Windows Server 2022 SERVERSTANDARD" xmlns:cpi="urn:schemas-microsoft-com:cpi" />
</unattend>
```

Copiez le fichier XML sur une VM linux du cluster et transformez le au format MIME64 comme ceci pour l'intégrer dans un autre fichier


```bash
USERDATA=$(base64 -w 0 answerfile.xml)
echo $USERDATA
```
Editez le fichier *vmwindows.json* ci-dessous en modifiant ces éléments pour l'adapter à votre environnement

* **< VMNAME >** par le nom de la VM que vous voulez donner 
* **< UUID-IMAGE-WINDOWS2022SYSPREPED >** par l'UUID de l'image WINDOWS SYSPREPED utilisable avec un fichier XML
* **< UUID-NETWORK >**  nom du réseau sur laquelle la machine virtuelle va fonctionner. 
* **< MIME64FORMATEDYAMLFILE >** doit être remplacé par le contenu de la variable ```$USERDATA```
* **< CLUSTER-NAME >** par le nom du cluster
* **< CLUSTER-UUID >** avec l'UUID du cluster  

Toutes les informations nécessaires sont soit accessibles depuis l'interface Web de **Prism central** ou au travers de commandes **REST API** 

```json
{
	"spec":{
		"name":"< VMNAME >",
		"resources":{
			"power_state":"ON",
			"num_vcpus_per_socket":2,
			"num_sockets":2,
			"memory_size_mib":4096,
			"disk_list":[{
				"device_properties":{
					"device_type":"DISK",
					"disk_address": {
						"device_index": 0,
						"adapter_type": "SCSI"
					}
				},
				"data_source_reference": {
					"kind": "image",
					"uuid": "< UUID-IMAGE-WINDOWS2022SYSPREPED >"
				}
			},
			{
				"device_properties":{
					"device_type":"CDROM",
					"disk_address": {
                                                "device_index": 0,
                                                "adapter_type": "IDE"
					}
				}
			}],
			"nic_list":[{
				"nic_type":"NORMAL_NIC",
				"is_connected":true,
				"ip_endpoint_list":[{
					"ip_type":"DHCP"
				}],
				"subnet_reference":{
					"kind":"subnet",
					"name":"base",
					"uuid":"< UUID-NETWORK >"
				}
			}],
			"guest_tools":{
				"nutanix_guest_tools":{
					"state":"ENABLED",
					"iso_mount_state":"MOUNTED"
				}
			},
			"guest_customization": {
				"sysprep": {
					"unattend_xml": "< MIME64FORMATEDYAMLFILE >"



},
				"is_overridable": false
			}
		},
		"cluster_reference":{
			"kind":"cluster",
			"name":"< CLUSTER-NAME >",
			"uuid":"< CLUSTER-UUID >"
		}
	},
	"api_version":"3.1.0",
	"metadata":{
		"kind":"vm"
	}
}
```

```bash
curl -k -H Accept:application/json -H Content-Type:application/json -u "prismcentraluser:prismcentralpassword"  -X POST "https://prismcentralip:9440/api/nutanix/v3/vms" -d @vmwindows.json | jq .
```

La nouvelle VM va apparaitre dans **Prism Central** elle sera démarrée et prendra les options du fichier de réponse.

## Aller plus loin <a name="gofurther"></a>

[Références NCLI de Nutanix](https://portal.nutanix.com/page/documents/details?targetId=Command-Ref-AOS-v5_20:man-ncli-c.html)

[Références ACLI de NUTANIX](https://portal.nutanix.com/page/documents/details?targetId=Command-Ref-AOS-v5_20:man-acli-c.html)

[Installation des CmdLets Nutanix](https://portal.nutanix.com/page/documents/details?targetId=PS-Cmdlets-AOS-v6_0:ps-ps-cmdlets-install-r.html#:~:text=Sign%20in%20to%20the%20Nutanix,desktop%20shortcut%20NutanixCmdlets%20is%20created.) 

[Réferences sur les outils de développements autour de Nutanix](https://www.nutanix.dev)

,
Échangez avec notre communauté d'utilisateurs sur <https://community.ovh.com/>.

Lancez cette commande 
