# Installation

Avant de pouvoir exécuter des scripts sur l'ESP32, nous devons d'abord nous assurer que le pilote de périphérique permet de communiquer avec la carte, flasher le firmware **MicroPython** sur l'ESP32, puis installer et configurer l'IDE **Thonny**.

## Installation du driver CH340 (Windows uniquement)

Pour transférer notre code, il faut installer le driver CH340 sur notre ordinateur.

Commencez par connecter l'ESP32 à l'ordinateur à l'aide du câble USB.

Ensuite, téléchargez le driver à cette adresse : [lien](https://learn.sparkfun.com/tutorials/how-to-install-ch340-drivers/all#windows-710){target=_blank}. Le fichier à télécharger pour les portables sous Windows est **CH341SER.exe**. Exécutez le fichier une fois téléchargé.

Pour vérifier que le driver est bien installé, ouvrez l'explorateur de fichiers et cliquez avec le bouton droit sur « Ce PC ». Sélectionnez ensuite « Gérer » et, dans le panneau de droite, « Gestionnaire de périphériques ».

![setup_01](\assets\images\esp32_setup_01.png){.center .shadow}

Dans la section « Ports (COM et LPT) », vous devriez avoir une entrée « USB-SERIAL CH340 (COM4) ». Le numéro du port peut changer (COM4). **Notez ce numéro** : il servira plus tard.

## Sur Linux (en cas de problème seulement)

Sur Linux, le driver `ch341` est déjà intégré donc pas besoin de le télécharger. Il reste juste a vérifier les permissions d'accès qui pourraient verrouiller le port.

### Vérifier la détection de la carte

Branchez l'ESP32, puis vérifiez que le système a bien créé le port série :

```bash
sudo dmesg | tail -20
```

Vous devriez voir apparaître `ch341-uart` et la création d'un port comme `/dev/ttyUSB0`. Pour lister les ports disponibles à tout moment :

```bash
ls /dev/ttyUSB*
```

### Autoriser l'accès au port série

Sous Linux, les ports série nécessitent des permissions particulières. Sur la plupart des distributions le groupe à utiliser est `dialout`. 

Vérifiez si votre utilisateur appartient déjà au bon groupe :

```bash
groups $USER
```

Si `uucp` (ou `dialout`) n'apparaît pas, ajoutez-vous au groupe :
```bash
sudo usermod -a -G dialout $USER
```


### Bug lié au port bloquer

Si vous rencontrez des erreurs de type `Permission denied` ou `read_until timed out`, arrêtez ces services.

```bash
sudo systemctl stop ModemManager
sudo systemctl stop brltty.service
```


## Flasher l'ESP32 (firmware MicroPython)

Avant que Thonny puisse communiquer avec la carte, il faut installer le **firmware MicroPython**. C'est le logiciel qui s'exécute au démarrage de l'ESP32 et qui contient l'interpréteur Python. Une carte vierge ne peut pas répondre à Thonny. Votre carte est équipée d'un module **WROVER**, qui possède de la **PSRAM** (SPIRAM). Il faut donc choisir la variante **« Support for SPIRAM / WROVER »** sur la page de téléchargement officielle.

1. Rendez-vous sur [https://micropython.org/download/ESP32_GENERIC/](https://micropython.org/download/ESP32_GENERIC/){target=_blank}.
2. Dans la section **« Support for SPIRAM / WROVER »**, téléchargez le fichier `.bin` le plus récent (par exemple `ESP32_GENERIC-SPIRAM-2024xxxx-v1.xx.x.bin`).
3. Placez ce fichier dans un dossier simple (par exemple `~/firmware/` sous Linux, ou `C:\firmware\` sous Windows).


## Flashage en ligne de commande avec `esptool`

Si vous préférez comprendre ce qui se passe sous le capot, ou si l'interface graphique ne fonctionne pas, vous pouvez utiliser `esptool` directement.

**Installer `esptool`** :


- **Ubuntu** :
  ```bash
  sudo apt install esptool
  ```
- **Windows** (via `pip`, après avoir installé Python) :
  ```powershell
  pip install esptool
  ```

**Effacer la flash** (recommandé pour une première installation) :

- **Linux** :
  ```bash
  esptool.py --chip esp32 --port /dev/ttyUSB0 erase_flash
  ```
- **Windows** :
  ```powershell
  esptool --chip esp32 --port COM4 erase_flash
  ```

**Écrire le firmware** à l'adresse `0x1000` :

- **Linux** :
  ```bash
  esptool.py --chip esp32 --port /dev/ttyUSB0 --baud 460800 write_flash -z 0x1000 ~/firmware/NOM_DU_FICHIER.bin
  ```
- **Windows** :
  ```powershell
  esptool --chip esp32 --port COM4 --baud 460800 write_flash -z 0x1000 C:\firmware\NOM_DU_FICHIER.bin
  ```

### Si la carte n'est pas détectée en mode bootloader

Il peut arriver que la carte ne bascule pas automatiquement en mode bootloader. Dans ce cas, utilisez la séquence manuelle :

1. Maintenez le bouton **`BOOT`** enfoncé.
2. Appuyez brièvement sur **`EN` (ou `RST`)** , puis relâchez-le, tout en gardant `BOOT` enfoncé.
3. Relâchez **`BOOT`**.

La carte attend alors les commandes de flashage.




## Installation de Thonny

Passons maintenant à l'installation de notre IDE, **Thonny**. Vérifiez d'abord s'il est déjà installé sur votre ordinateur (il était normalement inclus dans le SenseHAT). Sinon, allez à l'adresse suivante pour télécharger la dernière version disponible pour votre système d'exploitation :

- [https://thonny.org/](https://thonny.org/){target=_blank}

Démarrez l'installation une fois le programme téléchargé, puis lancez l'application.



## Configuration de Thonny

Une fois Thonny lancé et le firmware flashé, il reste à indiquer à l'IDE quel interpréteur utiliser et sur quel port.

1. Dans Thonny, allez dans **`Run`**.
2. Dans le menu déroulant **Interpreter**, choisissez **`MicroPython (ESP32)`**.
3. Dans le champ **Port**, sélectionnez le port correspondant à votre carte :
   - **Linux** : `/dev/ttyUSB0` (ou `/dev/ttyUSB1`, etc.).
   - **Windows** : `COM4` (ou le numéro que vous avez noté).
4. Cliquez sur **OK**.

La barre d'état en bas à droite de Thonny doit maintenant afficher quelque chose comme `MicroPython (ESP32) · /dev/ttyUSB0`. Pour vérifier que la connexion est établie, cliquez sur le bouton **Stop/Restart** (carré rouge) : le Shell en bas doit afficher le prompt `>>>` de MicroPython.

Vous pouvez alors tester une commande simple :

```python
print("Hello ESP32")
```

Si le message s'affiche, votre carte est prête et vous pouvez passer aux exercices.
