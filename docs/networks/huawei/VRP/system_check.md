
#Проверка состояния устройства

##Просмотр плат:

<BRAS>display device
MultiserviceEngine 60-X8's Device status:
Slot # Type Online Register Status Primary
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1 BSU Present Registered Normal NA
2 BSU Present Registered Normal NA
3 BSU Present Registered Normal NA
4 BSU Present Registered Normal NA
9 MPU Present NA Normal Master
10 MPU Present Registered Normal Slave
11 SFU Present Registered Normal NA
12 SFU Present Registered Normal NA
13 SFU Present Registered Normal NA
14 CLK Present Registered Normal Master
15 CLK Present Registered Normal Slave
16 PWR Present Registered Normal NA
17 PWR Present Registered Normal NA
18 FAN Present Registered Normal NA
19 FAN Present Registered Normal NA

##Просмотр аварий:

<BRAS>display alarm active


##Просмотр готовности горячего резерва MPU:

<BRAS7>display switchover state
Slot 9 HA FSM State(master): realtime or routine backup.
Slot 10 HA FSM State(slave): receiving realtime or routine data.

##Просмотр утилизации СPU и памяти по платам:

<BRAS7>display health
Slot CPU Usage Memory Usage(Used/Total)
---------------------------------------------------------
9 MPU(Master) 14% 17% 618MB/3606MB
1 BSU 12% 20% 418MB/2086MB
2 BSU 16% 23% 494MB/2086MB
3 BSU 11% 19% 410MB/2086MB
4 BSU 10% 19% 410MB/2086MB
10 MPU(Slave) 7% 15% 570MB/3606MB

##Просмотр температуры элементов:
<BRAS7>display temperature


##Просмотр утилизации CPU и памяти по процессам:

<BRAS7>display cpu-usage sorted
CPU Usage Stat. Cycle: 60 (Second)
CPU Usage : 15% Max: 81%
CPU Usage Stat. Time : 2017-11-29 14:54:49
CPU utilization for five seconds: 14%: one minute: 14%: five minutes: 15%
Max CPU Usage Stat. Time : 2017-11-14 15:32:26.
TaskName CPU Runtime(CPU Tick High/Tick Low) Task Explanation


##Просмотр логов syslog:
Для удобства можно использовать фильтрацию вывода - | include/begin
<BRAS7>display logbuffer


##История команд:

display history-command all-users

User : bob, VT0, 212.48.195.80
Time : 2017-12-01 00:42:44+03:00
Command: display history-command all-users
User : bob, VT0, 212.48.195.80
Time : 2017-12-01 00:09:09+03:00
Command: dis logbuffer
User : bob, VT0, 212.48.195.80
Time : 2017-12-01 00:09:01+03:00
Command: dis logbuffer summary
User : bob, VT0, 212.48.195.80
Time : 2017-12-01 00:08:49+03:00
Command: dis logbuffer
User : bob, VT0, 212.48.195.80
Time : 2017-11-30 21:57:47+03:00
Command: q
User : wandl, VT0, 10.245.110.146
Time : 2017-11-30 19:22:56+03:00
Command: quit
