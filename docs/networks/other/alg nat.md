**ALG NAT (Application Layer Gateway in Network Address Translation)**
ALG NAT (Application Layer Gateway в рамках NAT) — это функция сетевого устройства, которая помогает корректно обрабатывать трафик приложений, использующих сложные или динамические протоколы. Она решает проблемы с трансляцией адресов в ситуациях, где приложения требуют изменения сетевой информации (IP-адресов и портов) внутри самих данных приложения.

# Контекст
NAT (Network Address Translation) изменяет IP-адреса и порты в заголовках пакетов, позволяя устройствам в частной сети взаимодействовать с внешним миром через общий публичный IP-адрес. Однако многие приложения встраивают сетевые адреса или порты в тело своего трафика, что делает стандартную NAT-недостаточной, так как она меняет только заголовки пакетов.

ALG работает на уровне приложений (**Application Layer**) для динамической обработки этих данных и поддержания работоспособности протоколов.

# Основные задачи ALG NAT
1. Обнаружение и корректировка IP-адресов в теле сообщений. Протоколы, такие как FTP или SIP, передают IP-адреса в своем содержимом, которые могут стать недействительными после NAT. ALG выявляет и обновляет их.

2. Динамическое открытие портов. ALG автоматически открывает порты на шлюзе NAT для обработки соединений, инициированных определенным приложением.

3. Поддержка динамических протоколов. Протоколы, такие как H.323, SIP, или RTSP, полагаются на динамическое назначение портов. ALG упрощает их работу через NAT.

# Примеры работы ALG NAT
## **FTP (File Transfer Protocol)**
FTP использует два канала:

1. Канал управления (порт 21): Отправляет команды.
2. Канал данных: Динамически открываемый порт для передачи файлов.
   
Когда клиент за NAT отправляет команду PORT или PASV, он указывает свой IP и порт для передачи данных. NAT изменяет заголовок пакета, но IP и порт внутри тела команды остаются неизменными, что приводит к разрыву связи.

**Как помогает ALG**: ALG перехватывает команды PORT и PASV, заменяет IP и порт в теле пакета на корректные данные после NAT.

## **SIP (Session Initiation Protocol)**
SIP — это протокол, используемый в VoIP-телефонии для установления, изменения и завершения голосовых и видеосоединений. SIP-устройства часто встраивают свои IP-адреса в тело сообщений, таких как INVITE.

Проблема: Когда SIP-устройство за NAT отправляет запрос, встроенный IP-адрес становится недоступным для удаленного устройства.

Как помогает ALG: ALG обновляет SIP-сообщения, заменяя IP-адреса и порты на актуальные для удаленного узла.

## **H.323**

H.323 используется для передачи голосового и видео-трафика. Этот протокол создает несколько подключений через NAT с использованием динамических портов.

Как помогает ALG: ALG открывает порты и заменяет адреса в теле сообщений H.323, позволяя медиасессиям корректно устанавливать соединение.