
# Домашнее задание к занятию 2 «Кластеризация и балансировка нагрузки»
*Асадбеков Асадбек*

## Задание 1
Запустите два simple Python сервера на своей виртуальной машине на разных портах.  
Установите и настройте HAProxy.  
Настройте балансировку Round-robin на 4 уровне.  

### Ответы:
#### Конфигурация HAProxy
```
frontend tcp_front
    bind *:8080
    default_backend tcp_servers

backend tcp_servers
    balance roundrobin
    server s1 127.0.0.1:8888 check
    server s2 127.0.0.1:9999 check

listen stats
    bind *:8889
    mode http
    stats enable
    stats uri /stats
    stats refresh 5s
    stats realm Haproxy\ Statistics
```

### Скриншоты
**Ответы от серверов:**  
![](https://github.com/asad-bekov/hw-06/blob/main/img/1.png)
![](https://github.com/asad-bekov/hw-06/blob/main/img/2.png)  

**Веб-интерфейс статистики HAProxy:**  
![](https://github.com/asad-bekov/hw-06/blob/main/img/3.png)  

---

## Задание 2
Запустите три simple Python сервера на своей виртуальной машине на разных портах.  
Настройте балансировку Weighted Round Robin на 7 уровне, чтобы первый сервер имел вес 2, второй - 3, а третий - 4.  
HAProxy должен балансировать только HTTP-трафик, адресованный домену `example.local`.  

### Ответ:
Настройка HAProxy для балансировки нагрузки между тремя Python-серверами с использованием метода Weighted Round Robin.  
Балансировка настроена для домена `example.local`, а также обрабатываются запросы без указания домена.

### Конфигурация HAProxy
```
frontend http_front
    bind *:8088
    mode http
    acl is_example_local hdr(host) -i example.local
    use_backend web_servers if is_example_local
    default_backend web_servers

backend web_servers
    mode http
    balance roundrobin
    option httpchk
    http-check send meth GET uri /
    server s1 10.0.2.15:8888 check weight 2
    server s2 10.0.2.15:9999 check weight 3
    server s3 10.0.2.15:10000 check weight 4

listen stats
    bind *:8889
    mode http
    stats enable
    stats uri /stats
    stats refresh 5s
    stats realm Haproxy\ Statistics
```

### Скриншоты
**Веб-интерфейс статистики HAProxy:**  
![](https://github.com/asad-bekov/hw-06/blob/main/img/5.png)  

**Ответы от серверов:**  
![](https://github.com/asad-bekov/hw-06/blob/main/img/6.png)  

**Ответ без домена example.local:**  
![alt](https://github.com/asad-bekov/hw-06/blob/main/img/4.png)
