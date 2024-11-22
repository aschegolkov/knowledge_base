 
 



 Пример LDP LSP: 

```bash
                 LSP Information: LDP LSP
----------------------------------------------
FEC                In/Out Label    In/Out IF  
1.1.1.1/32   NULL/54846      -/Eth-Trunk2.2    # Метка in трафика  после per-hop behaviour (PHB)  UPLINK 1 
1.1.1.1/32   51122/54846     -/Eth-Trunk2.2    # Метка out трафика UPLINK 1            
1.1.1.1/32   NULL/6415       -/Eth-Trunk1.2    # Метка in трафика   после per-hop behaviour (PHB) UPLINK 2
1.1.1.1/32   51122/6415      -/Eth-Trunk1.2  # Метка out трафика UPLINK 2 
```