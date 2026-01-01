Alternative way to bind Noolite devices to Home Assistant with old PRF-64 and without MRTF

(RU)
Есть способ добавить кнопки/реле Noolite в Home Assistant, если у вас "старый" блок управления PRF-64, нету USB-адаптера MRTF, но хочется подвязать свои девайсы, то:
1. Узнаём ID кнопки/реле - его можно посмотреть в мобильном приложении, это набор букв и цифр (Hex-число), обычно три нуля и 5 букв/цифр за ним; в приложении это находится в свойствах устройства, в поле "Идентификатор"; например, в моём случае у реле идентификатор равен 0001859F
2. На ПК заходим по адресу http://ip_адрес_контроллера/fdev.xml и находим своё устройство в открывшемся текстовом файле, например:
```
<id1>0001859F</id1>
<st1>1:1:0:0</st1>
```
3. В конфигурации Home Assistant (configuration.yaml) добавляем для включения/выключения кусок конфига, заменив последние 5 букв/цифр на свои:
```
rest_command:
  noolite_my_knopka:
    url: "http://ip_адрес_контроллера/send.htm?sd=00020900000400000000000001859F"
```
4. Для опроса состояния кнопки/реле (если это устройство поддерживает обратную связь) добавляем там же в конфиг, заменив в строке с "st0" цифру на ту, которую запомнили в п.2
```
rest:
  - resource: "http://ip_адрес_контроллера/fdev.xml"
    scan_interval: 1
    binary_sensor:
        - name: noolite_sensor_my_knopka
          value_template: "{{ value_json['response']['st0'][6:] }}"
```

(EN)
To bind Noolite relay to Home Assistant without MRTF and with old-ish controller PRF-64, do following:
1. Find ID of your relay - it can be found in mobile app, press and hold relay button to open Settings and remember Hex value in "Identifier" field - it's usually a combo of digits and letters (Hex number); for example, my relay has ID equal to 0001859F
2. On your PC in web browser navigate to your PRF-64 controller by URL http://ip_address_of_controller/fdev.xml and find your device in the file you see in browser, for example:
```
<id1>0001859F</id1>
<st1>1:1:0:0</st1>
```
3. To add your relay to Home Assistant, edit configuration file (configuration.yaml) and add this (replace last 5 digits/letters with your device's ID):
```
rest_command:
  noolite_my_relay:
    url: "http://ip_address_of_controller/send.htm?sd=00020900000400000000000001859F"
```
4. If your device reports its on/off state to PRF-64 controller, you also can add it as a binary sensor in Home Assistant, replace 0 in 'st0' with the number you've seen previously in p.2
```
rest:
  - resource: "http://ip_address_of_controller/fdev.xml"
    scan_interval: 1
    binary_sensor:
        - name: noolite_sensor_my_relay
          value_template: "{{ value_json['response']['st0'][6:] }}"
```
