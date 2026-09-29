# Projeto 1 — Bat-Sinal

Sistema de dois nós ESP32-S3 que se comunicam pelo Mosquitto local da máquina do laboratório (`mqtt://192.168.1.107:1883`).

O **Node A** publica o alerta quando o botão é pressionado. O **Node B** inscreve no tópico e acende ou apaga o LED.

## Repositórios

| Nó | Repositório | Placa | Função |
|----|-------------|--------|--------|
| A — botão | [Bat_botao](https://github.com/caiodantass/Bat_botao.git) | ESP32-S3 + botão no GPIO 4 (outro lado em GND) | Publica `BAT_SIGNAL_ON` / `BAT_SIGNAL_OFF` |
| B — farol | [Bat_sinal](https://github.com/RafaelBerg/Bat_sinal/tree/main) | ESP32-S3 + LED no GPIO 10 | Inscreve no tópico e acende ou apaga o LED |

Firmware e roteiro de cada nó ficam nos repositórios acima — este diretório só documenta o conjunto.

## Contrato MQTT

Tópico de comando: `gotham/dpgc/berg_caio/batsignal`

- `BAT_SIGNAL_ON` → LED em nível alto. Log: `[ALERTA] Bat-Sinal Ativado! O Cavaleiro das Trevas foi convocado.`
- `BAT_SIGNAL_OFF` → LED em nível baixo. Log: `[INFO] Bat-Sinal Desativado.`

Heartbeat a cada 30 s em `gotham/dpgc/status`:

```text
{"device": "bat_sinal", "status": "ONLINE", "uptime_s": 120}
```

O Node A envia o mesmo formato com `"device": "bat_button"`.

## Hardware (resumo)

| Item | Valor |
|------|--------|
| Alvo | ESP32-S3 |
| Broker | Mosquitto local — `mqtt://192.168.1.107:1883` |
| Botão (Node A) | GPIO 4 → GND (pull-up interno) |
| LED (Node B) | GPIO 10, ativo em alto: **GPIO 10 → 220 Ω → anodo do LED**, catodo no GND |

## Teste sem o botão

```text
mosquitto_pub -h 192.168.1.107 -t gotham/dpgc/berg_caio/batsignal -m "BAT_SIGNAL_ON"
mosquitto_pub -h 192.168.1.107 -t gotham/dpgc/berg_caio/batsignal -m "BAT_SIGNAL_OFF"
mosquitto_sub -h 192.168.1.107 -t "gotham/dpgc/#" -v
```
