# 🌡️ Sistema de Monitoramento de Temperatura e Umidade — PS 2026.1

> Projeto desenvolvido pela **EletronJun – Empresa Júnior de Engenharia Eletrônica da Universidade de Brasília (UnB/FCTE – Gama)**

---

## 📋 Visão Geral

Sistema embarcado de monitoramento ambiental em tempo real com leitura de **temperatura e umidade**, controle via **interface web** e emissão de **alertas automáticos**. O hardware é baseado no microcontrolador **ESP32 (NodeMCU)** com o sensor **DHT11**, e o software oferece uma dashboard web responsiva acessível pela rede local.

| Campo              | Informação                  |
|--------------------|-----------------------------|
| Gerente responsável | Artur Navarro de Miranda   |
| Data de início      | 17/04/2026                 |
| Data de entrega     | 13/05/2026                 |
| Organização         | EletronJun – UnB           |

---

## ⚙️ Hardware

### Componentes Principais

| # | Componente | Qtd. | Preço estimado (R$) |
|---|-----------|------|----------------------|
| 1 | Microcontrolador Wi-Fi ESP32 (NodeMCU) – 30 pinos | 1 | 25,00 – 45,00 |
| 2 | Sensor de Temperatura e Umidade DHT11 | 1 | 6,99 – 21,89 |
| 3 | LED 5mm Azul | 3 | 0,41 (cada) |
| 4 | Resistor 330Ω, 1/4W (limitador de corrente dos LEDs) | 4 | 0,30 (cada) |
| 5 | Placa Fenolite Perfurada (5cm × 7cm) | 1 | 2,38 – 4,90 |
| 6 | Jumper Macho/Fêmea 10cm | 3 | 0,15 – 0,50 (cada) |
| 7 | Bateria Alcalina 9V (alternativa à fonte) | 1 | 25,00 |
| 8 | Adaptador de Bateria 9V com Plug P4 | 1 | 2,50 |
| 9 | Cabo Micro-USB para programação / alimentação | 1 | 5,00 – 10,00 |

**Custo total estimado:** R$ 69,75 – R$ 113,22

### Conexões do Circuito

**DHT11 → ESP32:**
- VDD → 3.3V
- DATA → Pino 15 (com resistor pull-up de 10kΩ)
- GND → GND

**LEDs → ESP32:**
- LED D1 (ânodo) → Pino 32
- LED D2 (ânodo) → Pino 26
- LED D3 (ânodo) → Pino 12
- Todos os cátodos → GND (via resistores 330Ω)

### Alimentação

O sistema suporta duas formas de alimentação:
- **Fonte 5V DC via Micro-USB** — regulação estável, sem ruído, ideal para uso fixo.
- **Bateria 9V (alcalina)** via adaptador Plug P4 → pino VIN — tensão contínua pura, ideal para uso portátil.

---

## 💻 Software

### Arquitetura

O software é dividido em quatro camadas:

1. **Hardware e Sensoriamento** — Leitura periódica do DHT11; controle de LEDs como feedback visual imediato.
2. **Lógica de Controle (Firmware)** — Máquina de estados (Desligado / Automático / Ligado); persistência local via SPIFFS; sincronização de horário via NTP.
3. **Interface Web (Front-end)** — Página única (`index.html`) com comunicação assíncrona (AJAX) com o ESP32; atualização do DOM em tempo real sem recarregar o navegador.
4. **Gestão de Erros** — Validação de leituras inválidas (NaN); restrições de escrita por modo de operação.

### Modos de Operação

| Modo | Comportamento dos LEDs | Descrição |
|------|------------------------|-----------|
| **Desligado** | 1 LED pisca | Sistema em standby; sem leituras |
| **Automático** | 2 LEDs piscam | Leituras periódicas e contínuas |
| **Ligado** | 3 LEDs acesos | Aguarda solicitação manual do usuário |
| **Alerta (temp. máxima)** | 3 LEDs piscando | Limite de temperatura ultrapassado |

### Fluxo de Dados

```
DHT11 → ESP32 (processamento) → Banco de dados (HTTP/HTTPS) → Interface Web
                ↑                                                      |
                └──────────── Comandos do usuário ────────────────────┘
```

---

## 🚀 Como Usar

### 1. Pré-requisitos

- [PlatformIO](https://platformio.org/) instalado (ou Arduino IDE)
- [Git](https://git-scm.com/) (opcional)
- Cabo Micro-USB

### 2. Clonar o Repositório

```bash
git clone https://github.com/din0t4/esp32_ps26.1.git
```

Ou acesse o repositório, clique em **Code → Download ZIP** e extraia os arquivos.

> - Código principal: pasta `src/`  
> - Arquivos da interface web: pasta `data/`

### 3. Configurar o Ambiente

1. Abra o projeto no PlatformIO (ou Arduino IDE).
2. Selecione a placa: **DOIT ESP32 DEVKIT V1**.
3. Selecione a porta **COM** correta.
4. Instale as bibliotecas necessárias no Gerenciador de Bibliotecas:
   - `DHT sensor library`
   - `WiFi`
   - `WebServer`
   - `SPIFFS`

### 4. Upload

```
# Gravar o firmware
Upload → botão "Upload" na IDE

# Enviar os arquivos da interface web
Usar a ferramenta: ESP32 Sketch Data Upload
```

### 5. Inicialização

1. Abra o **Monitor Serial** em `115200 baud`.
2. A placa exibirá o endereço IP local (ex: `192.168.x.x`).
3. Acesse esse IP no navegador para abrir o dashboard.

### 6. Operação

- **Troca de modo:** Selecione entre *Desligado*, *Automático* ou *Ligado* na interface web.
- **Configuração (modo Ligado):** Ajuste a temperatura máxima e a unidade (°C / °F).
- **Histórico:** Clique em **Baixar Histórico de Logs** para baixar o arquivo `log.txt`.

---

## 📡 Requisitos do Sistema

### Funcionais (essenciais)

- Coleta contínua de temperatura e umidade (sensor DHT11)
- Monitoramento em tempo real via interface web
- Três modos de operação: Desligado, Automático e Ligado
- Feedback visual por LEDs indicadores de estado
- Controle de modos via botões na interface web

### Funcionais (importantes)

- Alertas automáticos na interface web ao ultrapassar limites configurados
- Armazenamento local de logs em arquivo `.txt` (persistente sem rede)

### Não Funcionais

- Latência mínima na exibição dos dados (tempo real)
- Comunicação estável entre hardware e interface (Wi-Fi 802.11 b/g/n)
- Sensor com precisão de ±5% UR e faixa de 0°C a 50°C
- Baixo consumo energético
- Código documentado e estruturado para fácil manutenção

---

## 📁 Estrutura do Projeto

```
esp32_ps26.1/
├── src/
│   └── main.ino          # Firmware principal (ESP32)
├── data/
│   └── index.html        # Interface web (servida via SPIFFS)
├── platformio.ini        # Configuração do PlatformIO
└── README.md
```

---

## 🏛️ Sobre a EletronJun

**EletronJun** é a Empresa Júnior de Engenharia Eletrônica da Universidade de Brasília, campus Gama (FCTE).

- 🌐 [www.eletronjun.com.br](https://www.eletronjun.com.br)
- 📧 contato@eletronjun.com.br
- 📍 Área Especial de Indústria – Projeção A, 72444-240 – Gama/DF
