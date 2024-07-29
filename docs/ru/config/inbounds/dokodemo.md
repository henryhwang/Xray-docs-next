# Dokodemo-Door

Dokodemo door может прослушивать локальный порт и отправлять все данные, поступающие на этот порт, на порт указанного сервера, тем самым реализуя перенаправление портов.

## InboundConfigurationObject

```json
{
  "address": "8.8.8.8",
  "port": 53,
  "network": "tcp",
  "timeout": 0,
  "followRedirect": false,
  "userLevel": 0
}
```

> `address`: address

Перенаправлять трафик на этот адрес. Может быть IP-адресом, например, `"1.2.3.4"`, или доменным именем, например, `"xray.com"`. Тип данных: строка.

Если `followRedirect` (см. ниже) равно `true`, то `address` может быть пустым.

> `port`: number

Перенаправлять трафик на указанный порт целевого адреса, диапазон \[1, 65535\], тип данных: число. Обязательный параметр.

> `network`: "tcp" | "udp" | "tcp,udp"

Поддерживаемые типы сетевых протоколов. Например, если указано `"tcp"`, то будет приниматься только трафик TCP. Значение по умолчанию: `"tcp"`.

> `timeout`: number

Ограничение времени простоя соединения. Измеряется в секундах. Значение по умолчанию: `300`. Если во время обработки соединения в течение `timeout` секунд не передается никаких данных, соединение разрывается.

> `followRedirect`: true | false

Если значение равно `true`, dokodemo-door будет распознавать данные, перенаправленные iptables, и пересылать их на соответствующий целевой адрес.

См. настройку `tproxy` в разделе [Конфигурация транспорта](../transport.md#sockoptobject).

> `userLevel`: number

Уровень пользователя, для соединения будет использоваться [локальная политика](../policy.md#levelpolicyobject), соответствующая этому уровню пользователя.

Значение userLevel соответствует значению `level` в разделе [policy](../policy.md#policyobject). Если не указано, используется значение по умолчанию - 0.

## Использование

У Dokodemo door есть два основных варианта использования: прозрачное проксирование (см. ниже) и перенаправление портов.

Иногда некоторые сервисы не поддерживают прямое проксирование, такое как Socks5, а использование Tun или Tproxy является излишним, и эти сервисы взаимодействуют только с одним IP-адресом и одним портом (например: iperf, сервер Minecraft, конечная точка Wireguard), тогда можно использовать произвольную дверь.

Например, следующая конфигурация (предполагается, что исходящее соединение по умолчанию является допустимым прокси):

```json
{
  "listen": "127.0.0.1",
  "port": 25565,
  "protocol": "dokodemo-door",
  "settings": {
    "address": "mc.hypixel.net",
    "port": 25565,
    "network": "tcp",
    "timeout": 0,
    "followRedirect": false,
    "userLevel": 0
  },
  "tag": "mc"
}
```

В этом случае ядро будет прослушивать 127.0.0.1:25565 и перенаправлять трафик через исходящее соединение по умолчанию на mc.hypixel.net:25565 (сервер MC). При подключении клиента Minecraft к 127.0.0.1:25565 будет осуществлено подключение к серверу Hypixel через прокси.

## Пример настройки прозрачного прокси

Эту часть см. в разделе [Руководство по настройке прозрачного прокси (TProxy)](../../document/level-2/tproxy).