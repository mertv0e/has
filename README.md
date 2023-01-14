# Home Assistant install

# Установка докер
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo bash get-docker.sh
```
# Установка Docker Compose
```
sudo apt install python3-pip -y
```
# Установка ХА
Создать файл compose.yml со следующим создержимым
```yaml
version: '3'
services:
  homeassistant:
    container_name: homeassistant
    image: "ghcr.io/home-assistant/home-assistant:stable"
    volumes:
      - /PATH_TO_YOUR_CONFIG:/config
      - /etc/localtime:/etc/localtime:ro
    restart: unless-stopped
    privileged: true
    network_mode: host
```
После запустить контейнер
```
docker compose up -d
```
После установки ХА, подкелобчения ЗигБи корнтроллера надо устанвоить ZigBee2mqtt

В моем случае дангл определился как /dev/ttyACM0
```
[213856.991755] usb 1-1.4.4: new full-speed USB device number 11 using xhci_hcd
[213857.107820] usb 1-1.4.4: New USB device found, idVendor=1a86, idProduct=55d4, bcdDevice= 4.42
[213857.107851] usb 1-1.4.4: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[213857.107868] usb 1-1.4.4: Product: SONOFF Zigbee 3.0 USB Dongle Plus V2
[213857.107883] usb 1-1.4.4: Manufacturer: ITEAD
[213857.107897] usb 1-1.4.4: SerialNumber: 20221029154812
[213857.115415] cdc_acm 1-1.4.4:1.0: ttyACM0: USB ACM device
```
https://www.zigbee2mqtt.io/guide/getting-started/#installation

Активировать ХА интеграцию
https://www.zigbee2mqtt.io/guide/usage/integrations/home_assistant.html#mqtt-discovery
