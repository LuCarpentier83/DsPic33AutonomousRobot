# Robot autonome — dsPIC33EP512GM306

Projet de programmation embarquée réalisé à SeaTech : un petit robot mobile à deux moteurs
qui évite les obstacles grâce à des télémètres infrarouges, piloté par un microcontrôleur
**dsPIC33EP512GM306**, avec une interface PC (WPF / C#) qui communique avec lui par liaison série.

## Contenu

```
firmware/       Code C du microcontrôleur (MPLAB X, compilateur XC16)
pc-interface/   Application Windows WPF (.NET 6) de supervision par port série
```

### Firmware

| Fichier | Rôle |
|---|---|
| `main.c` / `main.h` | Boucle principale et machine à états du robot (attente, avance, tourne, recule…) |
| `ChipConfig.c` | Bits de configuration et horloge du dsPIC |
| `IO.c` | Affectation des broches (LED, moteurs, UART) |
| `timer.c` | Timers 1, 3 et 4 (rafraîchissement PWM, horodatage, déclenchement ADC) |
| `PWM.c` | Commande des moteurs avec rampe de vitesse |
| `ADC.c` | Acquisition des 5 télémètres infrarouges |
| `UART.c` | UART1 à 115200 bauds |
| `CB_TX1.c` / `CB_RX1.c` | Buffers circulaires d'émission / réception UART |
| `UART_Protocol.c` | Encodage des trames du protocole |
| `ToolBox.c` | Fonctions utilitaires |

### Interface PC

Application WPF qui ouvre le port série (`COM21`, 115200 bauds), décode les trames reçues et
affiche les mesures des télémètres, l'état des LED et les vitesses des moteurs.

Elle dépend de la bibliothèque `ExtendedSerialPort` fournie pendant le cours (non incluse ici) :
le `.csproj` l'attend dans `..\C#\ExtendedSerialPort .NET6\`.

## Protocole série

```
0xFE | Fonction (2 octets) | Longueur payload (2 octets) | Payload | Checksum
```

Le checksum est le XOR de `0xFE`, de la fonction, de la longueur et de chaque octet du payload.

| Fonction | Contenu |
|---|---|
| `0x0080` | Texte |
| `0x0020` | État des LED |
| `0x0030` | Distances des télémètres IR (gauche, centre, droit) |
| `0x0040` | Vitesses des moteurs (droit, gauche) |

## Compilation

- **Firmware** : créer un projet MPLAB X pour le dsPIC33EP512GM306 (XC16), y ajouter les
  fichiers de `firmware/` puis compiler et flasher avec un PICkit / ICD.
- **Interface PC** : ouvrir `pc-interface/Courtois_Carpentier_WPF.sln` dans Visual Studio
  (Windows, .NET 6).

## Auteurs

Courtois & Carpentier — SeaTech.
