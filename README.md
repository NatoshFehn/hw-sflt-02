# Домашнее задание к занятию 2 «Кластеризация и балансировка нагрузки» - Наталья Мартынова (Пономарева)

### Задание 1

1). Устанавливаем HAProxy c конфигурацией
 
[ссылка](https://github.com/NatoshFehn/hw-sflt-02/blob/main/haproxy.cfg)

```
 sudo apt-get install haproxy
 
 sudo nano /etc/haproxy/haproxy.cfg
  
```

2). Cоздаем директории для двух веб-серверов http1 и http2 с индекснымим файлами index.html

3). Запускаем два python сервера командами:

```
python3 -m http.server 8888 --bind 0.0.0.0

python3 -m http.server 9999 --bind 0.0.0.0

```
 
4). Проверка работоспособности:

![Снимок1](https://github.com/NatoshFehn/hw-sflt-02/blob/main/Снимок1.jpg)


5). В секции frontend указываем имя хоста, например: myweb.com

```
frontend myweb  # секция фронтенд
        mode tcp
        bind :3306
        acl ACL_myweb.com hdr(host) -i myweb.com
        use_backend web_servers if ACL_myweb.com

```

6). Конфигурация haproxy для балансировки по методу Round-robin на 4 уровне

```
backend web_servers    # секция бэкенд
        mode tcp
        balance roundrobin
        server s1 127.0.0.1:8888 check
        server s2 127.0.0.1:9999 check

```

7). Проверка:

![Снимок2](https://github.com/NatoshFehn/hw-sflt-02/blob/main/Снимок2.jpg)

8). Открываем порт 888 для проверки веб-хостов

```
sudo ufw allow 888

```

9). HAProxy Statistics

![Снимок3](https://github.com/NatoshFehn/hw-sflt-02/blob/main/Снимок3.jpg)



---

### Задание 2

1). Запускаем три сервера: Server1 порт: 8888, Server1 порт: 9999, Server1 порт: 7777

2). В настройках HAProxy перенастроим  fronted и backend:

для домена "example.local"

```
frontend example # секция фронтенд
        bind :8088
        acl ACL_example.local hdr(host) -i example.local
        use_backend web_servers if ACL_example.local
```

настроим веса для каждого сервера

```
backend web_servers    # секция бэкенд
        mode http
        balance roundrobin
        option httpchk
        http-check send meth GET uri /index.html
        server s1 127.0.0.1:8888 check weight 2
        server s2 127.0.0.1:9999 check weight 3
        server s3 127.0.0.1:7777 check weight 4
```


HAProxy Statistics

![Снимок4](https://github.com/NatoshFehn/hw-sflt-02/blob/main/Снимок4.jpg)



Конфигурация HAProxy 
[ссылка](https://github.com/NatoshFehn/hw-sflt-02/blob/main/haproxy_2.cfg)


Проверяем: все три сервера участвуют в балансировке

![Снимок5](https://github.com/NatoshFehn/hw-sflt-02/blob/main/Снимок5.jpg)
