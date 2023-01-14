# Home Assistant install

## Установка докер
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo bash get-docker.sh
```
## Установка Docker Compose
```
sudo apt install python3-pip -y
```
## Установка ХА
Создать файл ```compose.yml``` со следующим создержимым
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
## Установка ZigBee2mqtt
Для настройки ZigBee2MQTT надо узнать на какой файл ссылается контроллер. В моем случае дангл определился как /dev/ttyACM0
```
$ dmesg
...
[213856.991755] usb 1-1.4.4: new full-speed USB device number 11 using xhci_hcd
[213857.107820] usb 1-1.4.4: New USB device found, idVendor=1a86, idProduct=55d4, bcdDevice= 4.42
[213857.107851] usb 1-1.4.4: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[213857.107868] usb 1-1.4.4: Product: SONOFF Zigbee 3.0 USB Dongle Plus V2
[213857.107883] usb 1-1.4.4: Manufacturer: ITEAD
[213857.107897] usb 1-1.4.4: SerialNumber: 20221029154812
[213857.115415] cdc_acm 1-1.4.4:1.0: ttyACM0: USB ACM device
...
```
Далее создаем copmpose.yml файл
```yaml
version: '3.8'
services:
  mqtt:
    image: eclipse-mosquitto:2.0
    restart: unless-stopped
    volumes:
      - "./mosquitto-data:/mosquitto"
    ports:
      - "1883:1883"
      - "9001:9001"
    command: "mosquitto -c /mosquitto-no-auth.conf"

  zigbee2mqtt:
    container_name: zigbee2mqtt
    restart: unless-stopped
    image: koenkk/zigbee2mqtt
    volumes:
      - ./zigbee2mqtt-data:/app/data
      - /run/udev:/run/udev:ro
    ports:
      - 8080:8080
    environment:
      - TZ=Europe/Berlin
    devices:
      - <ID USB контроллера>:<ID USB контроллера>
```
Далее создаем конифгурационный файл ```zigbee2mqtt-data/configuration.yaml```
```yaml
# Let new devices join our zigbee network
permit_join: true
# Docker-Compose makes the MQTT-Server available using "mqtt" hostname
mqtt:
  base_topic: zigbee2mqtt
  server: mqtt://mqtt
# Zigbee Adapter path
serial:
  port: <ID USB контроллера>
# Enable the Zigbee2MQTT frontend
frontend:
  port: 8080
# Let Zigbee2MQTT generate a new network key on first start
advanced:
  network_key: GENERATE
 ```   
Поднимаем контейнер
```
$ docker-compose up -d
```
## Активировация интеграции в HA

- В ```configuration.yaml``` Zigbee2MQTT выставить homeassistant: true
- Установить интеграцию MQTT в Home Assistant

