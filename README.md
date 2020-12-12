# PhonieboxSetup
Dies ist eine kurze Anleitung meines [Phoniebox](https://github.com/MiczFlor/RPi-Jukebox-RFID) Setups.

## Einkaufsliste
* [Adata PowerPack PT100 10000mAh](https://www.galaxus.ch/de/s1/product/adata-powerpack-pt100-10000mah-powerbank-6337123)
* [Trust Leto 2.0 USB Lautsprecher](https://www.amazon.de/dp/B00JRW0M32/ref=pe_3044161_185740101_TE_item)
* [American Style Standard Arcade Tasten](https://www.amazon.de/dp/B07GBSJX2H/ref=pe_3044161_185740101_TE_item)
* [KKmoon IC Kartenlesegerät](https://www.amazon.de/dp/B011XI2DE8/ref=pe_3044161_185740101_TE_item)
* [Uonlytech 2m glow Glasfaserkabel](https://www.amazon.de/dp/B081JY4ZMJ/ref=pe_3044161_185740101_TE_item)
* [Pimoroni OnOff SHIM](https://www.pi-shop.ch/pimoroni-onoff-shim)
* 2 x [Brass M2.5 Standoffs 16mm](https://www.pi-shop.ch/brass-m2-5-standoffs-16mm-tall-black-plated-2er-pack)
* [KABEL USB TYPE-C 2.0 - USB 2.0 MICRO-B](https://www.conrad.ch/de/p/delock-usb-2-0-anschlusskabel-1x-usb-2-0-stecker-micro-b-1x-usb-c-stecker-1-00-m-schwarz-1371594.html)
* [FT USB-C F/F PLAIN HOLE](https://www.conrad.ch/de/p/xlr-adapter-usb-c-buchse-auf-usb-c-buchse-adapter-cp30201x-cliff-inhalt-1-st-2239986.html)
* [AUDAC TR2070](https://www.brack.ch/audac-entstoerfilter-tr2070-520216) (Störgeräusche [Problem](https://github.com/MiczFlor/RPi-Jukebox-RFID/issues/341))

## Installation
* Auf der `boot` Partition wurde die Datei `ssh` und `wpa_supplicant.conf` erstellt wie im [Wiki](https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/INSTALL-stretch#installation-and-configuration-via-ssh--headless-installation) beschrieben.
* Die Installation wurde wie im Wiki beschrieben mit dem [One line install command](https://github.com/MiczFlor/RPi-Jukebox-RFID) durchgeführt. Nach der `One line install command` Installation sind keine weiteren Installationen nötig.
* Der `MAC Adresse` des Raspi wurde im Router eine IP zugewiesen.

## Konfiguration

### RFID Kkmoon
Für den verwendeten RFID Reader muss folgendes gemacht werden:

* In der Datei `/scripts/daemon_rfid_reader.py` muss eine Zeile [angepasst](https://github.com/MiczFlor/RPi-Jukebox-RFID/issues/551#issuecomment-517492094) werden.
* Der Reader muss mit der Konfigurationskarte richtig [eingestellt](https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/RFID_Reader_KKMOON_Info) werden.
* Die Datei `/scripts/Reader.py` muss durch die Datei `/scripts/Reader.py.kkmoonRFIDreader` ersetzt werden wie im [Wiki](https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/RFID-Reader-Special#alternative-scripts) beschrieben.
* Der Reader muss noch [registriert](https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/CONFIGURE-stretch#register-your-usb-device-for-the-phoniebox) werden.
* Danach muss der Pi neu gestartet werden und im WebUI kann nun der Reader aktiviert werden.

### Startup Sound
Die Lautstärke für den Startup Sound ist standardmässig auf `0`. Kann im WebUI eingestellt werden.

### Ausschalten mit OnOff SHIM
Wenn der Pi mit dem `OnOff SHIM` ausgeschaltet wird, wird beim erneuten Starten die Wiedergabe automatisch fortgesetzt.
Das [Problem](https://github.com/MiczFlor/RPi-Jukebox-RFID/issues/1189#issuecomment-743426526) wurde gelöst, indem dann die Datei `/usr/bin/cleanshutd` angepasst hat. Ich habe den original Befehl auskommentiert und mit dem Befehl der Phoniebox ersetzt.

```bash
    while [ "$daemon" = "on" ]; do
        if shutdown_trigger; then
            msg="BCM $trigger_pin held low, system shutdown in $shutdown_delay minutes"
            echo $msg
            wall $msg
            daemon="off"
            #shutdown -h +$shutdown_delay
            /home/pi/RPi-Jukebox-RFID/scripts/playout_controls.sh -c=shutdown
            break
        fi
        sleep $polling_rate
    done
```
