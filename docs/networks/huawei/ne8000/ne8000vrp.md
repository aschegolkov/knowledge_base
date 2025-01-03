title: NE8000 VRP

# Huawei NetEngine 8000 VRP

## Конфигурация

### Управление конфигурацией

В Huawei VRP на NE8000 присутствует система candidate конфигураций

* `display configuration candidate changes` (`di co ca ch`) - показать различия между текущей и candidate(незакоммиченной) конфигурацией

* `refresh configuration candidate` (`re co ca`) - если конфигурация была изменена во время конфигурирования, обновить измененный конфиг

* `clear configuration candidate` (`c c c`) - очистить изменённую, но незакомиченную конфигурацию

### Базовая

Примеры конфигураций:

```bash
# Таймзона, add - "+", minus - "-"
clock timezone KRAT add 07:00:00
# Имя устройства
sysname LAB
# Сохранять current-config раз в 30 минут
set save-configuration interval 30

# Отключить по умолчанию включенный FTP-сервер
undo FTP server-source all-interface
undo FTP ipv6 server-source all-interface
# Отключить стандартную политику парольной безопасности
undo user-security-policy enable

# Отключить возможность подключения по telnet
undo telnet server enable
undo telnet ipv6 server enable
undo telnet server-source all-interface
undo telnet ipv6 server-source all-interface

# Алиасы команд
command alias
 alias dicocach command "display configuration candidate changes"
 alias sh command di
 alias shipcef command "display fib slot 1"
 alias show command display
 alias shpfx command "dis c c xpl-pfx"
 alias brp command "display bgp routing-table peer"
 alias bv command "display bgp vpnv4 vpn-instance"
 alias cca command "display configuration commit changes at"
 alias ccl command "display configuration commit changes last 1"
 alias ccs command "display configuration commit changes since"
 alias cl command "display configuration commit list"
 alias cmp command "display configuration candidate changes"
 alias exit command "quit"
 alias lld command "display lldp neighbor brief"
 alias rt command "display ip routing-table"
 alias rtv command "display ip routing-table vpn-instance"
 alias sfp command "display elabel optical-module brief"
 alias show command "display"
 alias sid command "display interface description"
 alias sii command "display ip interface brief"
 alias sr command "display current-configuration"
 alias sri command "display current-configuration interface"
 alias srig command "display current-configuration interface GigabitEthernet"
 alias srp command "display current-configuration configuration"
 alias srpa command "display current-configuration configuration command-alias"
 alias srpb command "display current-configuration configuration bgp"
 alias srps command "display current-configuration configuration route-static"

# DNS-клиент
dns resolve
dns server 80.65.16.1
dns server 80.65.20.1
dns domain orionnet.ru

# Отключение plug-and-play фич
undo dcn
undo pnp enable
undo pnp default route
undo ip vpn-instance __LOCAL_OAM_VPN__
interface ether0/0/0
 undo ip binding vpn-instance __LOCAL_OAM_VPN__
```

### Интерфейсы

#### Port-Group

Аналог interface range, можно только `shutdown`/`undo shutdown`

```bash
# Создаём временную группу
port-group group-member $INT1 to $INT2 $INT3 to $INT4
  shutdown

# Создаём постоянную группу
port-group $GRNAME
  group-member $INT1 to $INT2 $INT3 to $INT4
```

#### Ether-Trunk

Аналог PortChannel у Cisco

```bash
# Создаём сам интерфейс
interface eth-trunk $ID
  portswitch				# перевести в L2 режим (не обязательно, естественно)
  mode lacp-static			# включаем LACP
  load-balance {packet-all|src-dst-ip|src-dst-mac|symmetric-hash}
  statistic enable			# без этого не включаются counter'ы

# Добавление интерфейсов в ET
interface eth-trunk $ID
  trunkport $INT1 to $INT2 mode active
# или
interface $INT1
  eth-trunk $ID mode active
interface $INT2
  eth-trunk $ID mode active
```

#### Subinterface

```bash
# Создание интерфейса
interface Eth-Trunk $ID.$VID
  vlan-type dot1q $VID
  statistic enable # вкл стат загруженности
```

### Маршрутизация

#### BGP

```bash
# Создание процесса BGP
bgp $ASN
 router-id $RID
 # Добавление peer'а
 peer $IPADDR as-number $PEERASN
 peer $IPADDR description $DESCR
 peer $IPADDR connect-interface $INT # source-interface
 peer $IPADDR ignore # временное отключение сессии с peer'ом
 ipv4-family unicast
  # Указание максимального кол-ва маршрутов с одинаковой ценой
  maximum load-balancing {ebgp|ibgp) $NUM
  peer $IPADDR reflect-client # настройка peer'а как RR-client
  peer $IPADDR next-hop-local # смена IP next-hop в маршруте на собственный адрес устройства
  peer $IPADDR advertise-community
  peer $IPADDR route-filter $XPL-RF {export|import} # Extended routing-policy language (XPL) XPL route-filter на peer'а
  
```

##### Route redistribution

```bash
bgp $ASN
 ipv4-family unicast
  import-route direct route-policy $POLICY
```

#### IS-IS

```bash
# Создание процесса IS-IS
isis $NUM
 description $DESCR
 is-level {level-1|level-1-2|level-2} # установка уровня устройства
 cost-style {narrow|compatible|wide|...}
 timer lsp-generation 5 1 50 level-1
 timer lsp-generation 5 1 50 level-2
 flash-flood 15 level-1
 flash-flood 15 level-2
 bfd all-interfaces enable
 bfd all-interfaces min-tx-interval 50 min-rx-interval 50
 circuit-cost 100000 level-2
 network-entity 49.0001.1092.2625.5214.00
 is-name LAB-DS-NE8000
 timer spf 5 1 50
 frr
  loop-free-alternate level-2
  ti-lfa level-2
```

Привязка IS-IS процесса к интерфейсу:

```bash
interface 25GE0/1/47.1105
 mtu 9198
 description << LAB-C4900M >>
 ip address 94.73.254.42 255.255.255.254
 statistic enable
 pim sm         
 isis enable 1
 isis circuit-type p2p
 isis circuit-level level-2
 isis authentication-mode md5 plain t,fkfqrf2019
 isis cost 100000 level-2
```



#### VPN-instance

Аналог VRF у Cisco

```bash
# Создание VPN-I
ip vpn-instance $NAME
  description $DESCR
  
# Привязка интерфейса к VPN-I
int $INT
  ip binding vpn-instance $NAME
```

#### XPL

При конфигурировании XPL есть два способа их изменения:

* Paragraph-by-paragraph - неинтерактивное изменение политики как текстового файла во встроенном текстовом редакторе (vim):

	```bash
	# Вход в режим конфигурирования Paragraph-by-paragraph
	<NE8000>edit xpl route-filter RF-NET_NAT
	
	# Комбинации в текстовом редакторе:
	:q # выход при отсуствии изменений
	:q! # выход без сохранения внесённых изменений
	:wq # выход с сохранением внесённых изменений
	i # вход в режим редактирования текста
	Esc # выход из режима редактирования текста
	```

* Line-by-line - интерактивное изменение политики в командной строке

##### Route-filter

```bash
# Создание route-filter
xpl route-filter $NAME
 if ... then
  apply ...
  {approve|break|finish|refuse}
  endif
 ...
 end-filter
```

#### IP-prefix

Аналог prefix-list Cisco

```bash
ip ip-prefix $NAME [ index $SEQ ] { permit|deny } $IPADD $MASK [ greater-equal $GE ] [ less-equal $LE ]

ip ip-prefix netMBR index 10 permit 80.65.16.0 20 gr 24 le 31
```

#### Route-policy

Аналог route-map Cisco

```bash
# Создание route-policy
route-policy $NAME { permit|deny } node $SEQ
 # Установить определенный параметр
 apply $PARAM
 # Установить условие, при котором будут применяться параметры
 if-match $MATCH

route-policy LocPrf_90 permit node 10
  apply local-preference 90
  if-match as-path length 2
```

#### Community-filter

##### Advanced

Аналог community-list Cisco, позволяет создавать именованные листы

```bash
ip community-filter advanced deny_as index 10 deny "31257:5000"
ip community-filter advanced deny_as index 20 permit ".*"
```

### Разное

#### Port-mirroring

Конфигурирование интерфейса, в который осуществлять мирроринг:

```bash
interface $INTERFACE
 port-observing observe-index $INDX [ without-filter ]
 # without-filter - отправлять трафик на инт без фильтров
```

Конфигурирование интерфейса, с которого нужно снимать трафик:

```bash
interface $INTERFACE
 port-mirroring { inbound|outbound } [ cpu-packet ] [ pe-vid ] [ user-defined-filter ] [ vlan ]
 # inbound|outbound - направление трафика для мирроринга
 # cpu-packet - миррорить только прилетающий на CPU траффик
 # pe-vid
 # user-defined-filter - миррорить только трафик, указанный с помощью фильтра:
 ## observe user-defined-filter...
 port-mirroring to observe-index $INDX
 # миррорить трафик на интерфейс с $INDX
 port-mirroring car cir $CIR pir $PIR cbs $CBS pbs $PBS
 # Committed Access Rate:
 ## cir - Committed information rate
 ## pir - Peak information rate
 ## cbs - Committed burst size
 ## pbs - Peak burst size
 # Значения указываются в kbps
```

#### NTP

```bash
# Отключить NTP-сервер, сконфигурировать железку как клиент
ntp-service server disable
ntp-service ipv6 server disable
ntp-service server source-interface all disable
ntp-service ipv6 server source-interface all disable
ntp-service unicast-server 109.226.249.72 source-interface LoopBack0 preference
```


### NQA тесты 

``` bash
nqa test-instance PE1  PE2
 test-type icmpjitter
 destination-address ipv4 1.1.1.1
 source-address ipv4 2.2.2.1
 send-trap testfailure
 interval milliseconds 100
 probe-count 1
 jitter-packetnum 3000
 icmp-jitter-mode icmp-echo
 frequency 3600
 start now
#
nqa test-instance PE1 PE3
 test-type icmpjitter
 destination-address ipv4 1.1.1.11
 source-address ipv4 2.2.2.2
 send-trap testfailure
 interval milliseconds 100
 probe-count 1
 jitter-packetnum 3000
 icmp-jitter-mode icmp-echo
 frequency 3600
 start now
 ```
