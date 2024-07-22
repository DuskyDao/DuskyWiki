#mikrotik #cli 
##### Добавить `ether` интерфейсы в бридж кроме `ether49`
```r
 :foreach i in=[/interface find where (name~"^ether" && name!="ether49")] do={/interface bridge port add interface=$i bridge=bridge.ceha}
```
