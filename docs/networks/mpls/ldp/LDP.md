#  Пример LDP LSP.
По две записи для возможности обработки как после per-hop behaviour (PHB) с RIBа , так и с меткой(мало вероятно если это PE): 

```bash
                 LSP Information: LDP LSP
----------------------------------------------
FEC                In/Out Label    In/Out IF  
1.1.1.1/32   NULL/54846      -/Eth-Trunk2.2    
1.1.1.1/32   51122/54846     -/Eth-Trunk2.2             
1.1.1.1/32   NULL/6415       -/Eth-Trunk1.2  
1.1.1.1/32   51122/6415      -/Eth-Trunk1.2  
```

1.1.1.1/32 в LFIB есть запись с Out Label = 54846 и интерфейсом Eth-Trunk2.2. Это означает, что пакет, который приходит с NULL в In Label(display ip routing table *(Label: NULL)*), будет помечен меткой 54846, когда он будет направлен через интерфейс Eth-Trunk2.2 и отправлен дальше по MPLS-сети.

К примеру, для дефолт маршрута

```bash
PE3>display ip routing-table 0.0.0.0 verbose 
Route Flags: R - relay, D - download to fib, T - to vpn-instance, B - black hole route
------------------------------------------------------------------------------
Routing Table : _public_
Summary Count : 4

Destination: 0.0.0.0/0           
     Protocol: ISIS-L1            Process ID: 1              
   Preference: 15                       Cost: 10000          
      NextHop: 2.2.2.2       Neighbour: 0.0.0.0
        State: Active Adv                Age: 33d11h33m36s        
          Tag: 0                    Priority: high           
        Label: NULL                  QoSInfo: 0x0           
   IndirectID: 0x10000FF            Instance:                                 
 RelayNextHop: 0.0.0.0             Interface: Eth-Trunk1.2
     TunnelID: 0x0                     Flags: D  
```

**Label: NULL**

В LFIB:
```bash
PE3>display mpls route-state 0.0.0.0 0 verbose

Codes: B(BGP), I(IGP), L(Public Label BGP), O(Original BGP), U(Unknow)
-----------------------------------------------------------------------------------
Dest/Mask          Next-Hop        Out-Interface              State    LSP VRF Type
-----------------------------------------------------------------------------------
0.0.0.0/0          1.1.1.4   Eth-Trunk2.2               IDLE     0   0      I
0.0.0.0/0          1.1.1.3   Eth-Trunk1.2               IDLE     0   0      I
```

**Отсутствуют LSP. Соотвественно пакет пойдёт чистым IP.**


# Пример вывода стека меток (label-stack) L3VPN в котором **10.0.9.3**  :
```bash
PE3>display mpls label-stack vpn-instance vL3VPN 10.0.9.3 
Label-stack  : 1
Level        : 1
Type         : VPN
Label        : 730279
Level        : 2
Type         : LDP
Label        : 7165
OutInterface : Eth-Trunk1.2

Label-stack  : 1
Level        : 1
Type         : VPN
Label        : 730279
Level        : 2
Type         : LDP
Label        : 55405
OutInterface : Eth-Trunk2.2

Label-stack  : 2
Level        : 1
Type         : VPN
Label        : 730279
Level        : 2
Type         : LDP
Label        : 7165
OutInterface : Eth-Trunk1.2

Label-stack  : 2
Level        : 1
Type         : VPN
Label        : 730279
Level        : 2
Type         : LDP
Label        : 55405
OutInterface : Eth-Trunk2.2
```
видно как верхняя метка по nexthop 

```bash
PE3>display mpls ldp lsp | i 1.1.1.1
Info: It will take a long time if the content you search is too much or the string you input is too long, you can press CTRL_C to break.

 LDP LSP Information
 -------------------------------------------------------------------------------
 Flag after Out IF: (I) - RLFA Iterated LSP, (I*) - Normal and RLFA Iterated LSP
 -------------------------------------------------------------------------------
 DestAddress/Mask   In/OutLabel    UpstreamPeer    NextHop          OutInterface
 -------------------------------------------------------------------------------
 1.1.1.1/32   NULL/7165      -               1.1.1.3    Eth-Trunk1.2
 1.1.1.1/32   50817/7165     2.2.2.2(Loopback)   1.1.1.3    Eth-Trunk1.2
 1.1.1.1/32   50817/7165     2.2.2.2   1.1.1.3    Eth-Trunk1.2
 1.1.1.1/32   NULL/55405     -               1.1.1.4    Eth-Trunk2.2
 1.1.1.1/32   50817/55405    1.1.1.1   1.1.1.4    Eth-Trunk2.2
 1.1.1.1/32   50817/55405    1.1.1.1   1.1.1.4    Eth-Trunk2.2
 -------------------------------------------------------------------------------
```

PE1 откуда L3VPN маршрут: 

```bash
PE1>display mpls lsp protocol bgp
-------------------------------------------------------------------------------
                 LSP Information: BGP LSP
-------------------------------------------------------------------------------
FEC                In/Out Label    In/Out IF                      Vrf Name
-/32               730279/NULL      -/-                            vL3VPN
```

То есть , поступив на роутер откуда светится 10.0.9.3, bottom label (впн метка) будет идентифицировать в какой L3VPN отправлять пакет. 

# Пример BGP LDP (метки L3VPN vrf):
Это, собственно, те метки по которым уже сам PE3 идентифицирует L3VPN трафик из MPLS-сети.
Они указывают, что PE3 завершает MPLS-сессию (Label Switched Path). Распаковываясь в определённый L3VPN. 
```bash
PE3>display mpls lsp protocol bgp
-------------------------------------------------------------------------------
                 LSP Information: BGP LSP
-------------------------------------------------------------------------------
FEC                In/Out Label    In/Out IF                      Vrf Name
-/32               49376/NULL      -/-                             v1083
-/32               49377/NULL      -/-                             v1084
-/32               49506/NULL      -/-                             v1085
-/32               48924/NULL      -/-                             v1086

```