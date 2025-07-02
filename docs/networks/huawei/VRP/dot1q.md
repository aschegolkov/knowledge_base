![alt text](dot1qvsi.png)

при отправке BUM пакетов в данном VSI они будут реплицироваться во все влан данного сабинтерфейса(где dot1q termination vid XXX to YYY).




VPLS Cloud (qualify-mode)
      |
     PE
      |
  ----------------------
  | dot1q subinterface |
  | VLAN 3706          |
  | VLAN 3709-3710     |
  ----------------------
      |
     CE
👉 Без client-mode single:

BUM трафик из VPLS идёт в 3706, 3709, 3710, и т.д.

👉 С client-mode single:

BUM трафик из VPLS идёт один раз в саб-интерфейс


Почему так?
Исторически VPLS изначально работал без учёта VLAN (трафик реплицировался во все интерфейсы).

Позже добавили qualify-mode (учёт VLAN), но логика репликации на sub-интерфейсе осталась старой (для совместимости).

Без client-mode single он тупо множит BUM-трафик по всем VLAN из dot1q termination vid, даже если это бессмысленно в qualify-mode.

client-mode single — это костыль, чтобы исправить эту неоптимальность.


https://info.support.huawei.com/hedex/api/pages/EDOC1100363264/AEN0403J/06/resources/command/yunshan/DOT1Q_TERMINATION_CLIENT-MODE_SINGLE.html
