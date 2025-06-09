<h1 align="center"> Projeto MQTT - NodeMCU </h1>

# Sumário
  1. [Visão Geral](#visão-geral)
  2. [Estrutura de Pastas](#estrutura-de-pastas)
  3. [Pré-requisitos](#pré-requisitos)
  4. [Instalação e Configuração](#instalação-e-configuração)
      - 4.1 [Driver NodeMCU](#driver-nodemcu)
      - 4.2 [PlatformIO](#platformio)
      - 4.3 [Mosquitto](#mosquitto-broker-mqtt)
      - 4.4 [MQTT Explorer](#mqtt-explorer-cliente-gráfico)
  5. [Código Fonte](#código-fonte)
      - 5.1 [Configurações Wi-Fi e MQTT](#configurações-wi-fi-e-mqtt)
      - 5.2 [Callback e Controle do LED](#callback-e-controle-do-led)
      - 5.3 [Estrutura de Tópicos](#estrutura-de-tópicos) 
  6. [Como Compilar e Carregar](#como-compilar-e-carregar)
  7. [Testes e Exemplos](#testes-e-exemplos)
  8. [Estrutura de Tópicos por Cômodo](#estrutura-de-tópicos-por-cômodo)
  9. [Contribuindo](#contribuindo) 

# Visão Geral

Este projeto demonstra a criação de um sistema simples de controle de iluminação utilizando o protocolo MQTT, através de um microcontrolador NodeMCU que atuará como publicador, e o broker Mosquitto para envio de comandos. Esses comandos serão responsáveis por controlar o estado de um LED, simulando um ambiente de automação residencial básico.

# Estrutura de Pastas

O código do projeto possui a seguinte estrutura de pastas:

```
├── 📁 .pio/                      ← Pasta interna usada para arquivos de build
├── 📁 .vscode/                   ← Configurações específicas do VSCode
├── 📁 include/                   ← Arquivos de cabeçalho
├── 📁 lib/                       ← Bibliotecas locais específicas do projeto
├── 📁 src/                       ← Código-fonte principal
│   └── 📂 main.cpp               ← Arquivo principal do projeto
├── 📁 test/                      ← Testes unitários
├── 📄 platformio.ini             ← Arquivo de configuração do projeto
```

# Pré-requisitos

  - Placa NodeMCU (ESP8266 V2 ou ESP8266 V3)
  - Cabo Micro USB
  - Mosquitto (Broker)
  - MQTT Explorer
  - Rede Wi-Fi
  - Visual Studio Code
  - Extensão PlatformIO do VS Code

# Instalação e Configuração

## Driver NodeMCU

Para instalar os drivers da placa, primeiro é necessário identificar qual é a versão dela. 

<p align="center">
  <img alt="NodeMCU V2" src="./Imagens/Versão Placa NodeMCU (V2).png" width="400">
  <img alt="NodeMCU V3" src="./Imagens/Versão Placa NodeMCU (V3).png" width="400">
</p>

Em seguida, basta acessar o site da Robo Core ([clique aqui](https://www.robocore.net/tutoriais/instalando-driver-do-nodemcu)) para baixar o driver de acordo com seu sistema operacional. Após o download, deve-se descompactar o arquivo e executar o arquivo .exe como administrador. Por fim, dependendo da versão da sua placa, será necessário clicar em **Next** até chegar na última página de instalação para finalizar clicando em **Finish** ou apenas pressionar o botão **Install**.

## PlatformIO

### Instalação

Para baixar o PlatformIO será necessário abrir seu Visual Studio Code, por conseguinte, ir na aba **Extensões**, pesquisar por PlatformIO, clicar na extensão em questão e então em **Install**.

<p align="center">
<img alt="Instalação PlatformIO" src="./Imagens/PlatformIO (1).png" width="1000">
</p>

### Criação do Projeto

Para criar um projeto, depois de instalar a extensão, será apresentado um ícone na barra vertical a esquerda da tela, logo, você irá clicar nele, em seguida, em **Create New Project** e em **+ New Project**.

<p align="center">
<img alt="Instalação PlatformIO" src="./Imagens/PlatformIO (2).png" width="1000">
</p>

Por último, é preciso preencher o nome do projeto, selecionar a placa **NodeMCU 1.0 (ESP-12E Module)** e clicar em **Finish**. Dessa forma, um novo projeto será criado.

<p align="center">
<img alt="Instalação PlatformIO" src="./Imagens/PlatformIO (3).png" width="400">
</p>

## Mosquitto (Broker MQTT)

Para instalar o Mosquitto, você deve abrir a página do broker ([clique aqui](https://mosquitto.org/download/)) e selecionar o executável compatível com seu ambiente e arquitetura do computador (32 ou 64 bits). Caso seja Linux, pode-se baixar usando o snap (```snap install mosquitto```). Além disso, após a instalação, precisa-se ir no diretório onde o mesmo foi salvo, abrir o arquivo **mosquitto.conf** e adicionar o seguinte:
```
listener 1883 0.0.0.0
allow_anonymous true
```

### Liberação da porta 1883

Para funcionamento da aplicação, é fundamental criar uma regra no firewall para liberar a porta 1883 TCP como entrada (para Linux não tem necessidade de fazer essa ação). Para a criação, tem de se abrir o **Windows Defender Firewall com Segurança Avançada**, clicar em **Regras de Entrada** e depois em **Nova Regra**.

<p align="center">
<img alt="Liberação da porta 1883" src="./Imagens/Regra de Entrada (1).png" width="1000">
</p>

Selecionar o tipo **Porta** e **Avançar**.

<p align="center">
<img alt="Liberação da porta 1883" src="./Imagens/Regra de Entrada (2).png" width="600">
</p>

Inserir a porta **1883** na opção de **Portas Locais Específicas** e **Avançar**.

<p align="center">
<img alt="Liberação da porta 1883" src="./Imagens/Regra de Entrada (3).png" width="600">
</p>

Marcar a opção **Permitir a Conexão** e **Avançar**.

<p align="center">
<img alt="Liberação da porta 1883" src="./Imagens/Regra de Entrada (4).png" width="600">
</p>

Assinalar as três opções (**Domínio**, **Privado** e **Público**) e **Avançar**.

<p align="center">
<img alt="Liberação da porta 1883" src="./Imagens/Regra de Entrada (5).png" width="600">
</p>

Digitar um **Nome** para a regra e **Concluir**.

<p align="center">
<img alt="Liberação da porta 1883" src="./Imagens/Regra de Entrada (6).png" width="600">
</p>

## MQTT Explorer (Cliente Gráfico)

Para instalar o MQTT Explorer, é necessário abrir o site do produto ([clique aqui](https://mqtt-explorer.com/)), ir até a seção **Download** e instalar o programa conforme a plataforma da sua máquina. Adicionalmente, no seu projeto no arquivo **platformio.ini**, é essencial adicionar a dependência abaixo:
```
lib_deps =
 knolleary/PubSubClient
```

Ademais, ao abrir o aplicativo, é obrigatório informar o **Host** como **127.0.0.1** ou **localhost**, a **Porta 1883** e pressionar o botão **Connect**. Caso queira, você pode alterar o nome da conexão no campo **Name**.

<p align="center">
<img alt="Instalação PlatformIO" src="./Imagens/MQTT Explorer.png" width="600">
</p>

# Código Fonte

O código desse projeto está na pasta **Sistemas Embarcados > Projeto MQTT - NodeMCU > src**.

## Configurações Wi-Fi e MQTT

Inicialmente, são feitas as importações das bibliotecas, definições das credenciais da rede Wi-Fi e das configurações do broker MQTT (IP, porta e tópicos de publicação e assinatura).
```
#include <ESP8266WiFi.h>
#include <PubSubClient.h>

const char* ssid = "ssid";
const char* password = "senha";

const char* mqtt_server = "ip";
const int mqtt_port = 1883;

const char* sub_topic = "casa/sala/led/set";
const char* pub_topic = "casa/sala/led/status";

const uint8_t LED_PIN = D4;

WiFiClient espClient;
PubSubClient client(espClient);
```

A função ```setup_wifi()``` conecta o NodeMCU à rede Wi-Fi:
```
void setup_wifi() {
  Serial.print("Conectando na rede Wi-Fi: ");
  Serial.print(ssid);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println();
  Serial.print("Wi-Fi conectado! IP: ");
  Serial.println(WiFi.localIP());
}
```

A conexão com o broker MQTT é gerenciada pela função ```reconnect()```. Aqui o usuário vai ser comunicado que esta tentando conectar com o broker, se funcionou ou se ocorreu algum problema:
```
void reconnect() {
  while (!client.connected()) {
    String clientId = "NodeMCU-";
    clientId += String(random(0xffff), HEX);
    Serial.print("Tentando conectar ao broker MQTT... ID = ");
    Serial.println(clientId);

    if (client.connect(clientId.c_str())) {
      Serial.println("Conectado ao broker!");
      client.subscribe(sub_topic);
      Serial.print("Inscrito no tópico: ");
      Serial.println(sub_topic);
    } else {
      Serial.print("Falha na conexão. Código de erro: ");
      Serial.print(client.state());
      Serial.println(" – tentando novamente em 5 segundos");
      delay(5000);
    }
  }
}
```

## Callback e Controle do LED

O controle do LED ocorre dentro da função ```callback()```, que é acionada sempre que o NodeMCU recebe uma mensagem no tópico ao qual ele está inscrito.
```
void callback(char* topic, byte* payload, unsigned int length) {
  String msg;
  for (unsigned int i = 0; i < length; i++) {
    msg += (char)payload[i];
  }
  msg.toUpperCase();

  if (msg == "ON") {
    digitalWrite(LED_PIN, HIGH);
    Serial.println("Recebido 'ON' → LED ligado");
  } else if (msg == "OFF") {
    digitalWrite(LED_PIN, LOW);
    Serial.println("Recebido 'OFF' → LED desligado");
  } else {
    Serial.print("Recebido comando desconhecido: ");
    Serial.println(msg);
  }

  client.publish(pub_topic, msg.c_str(), true);
}
```

Parâmetros da função:
  - ```char* topic```: tópico MQTT no qual a mensagem foi recebida.
  - ```byte* payload```: representa o conteúdo (mensagem).
  - ```unsigned int length```: comprimento da mensagem recebida (quantidade de bytes).

O ```payload``` vem como um array de bytes, e no primeiro trecho da função (loop for) ocorre a conversão byte a byte para formar a mensagem.

No fim, o LED responde aos comandos **ON** para ligar e **OFF** para desligar e é feito a publicação do estado atual do tópico.

## Estrutura de Tópicos

```
casa/cômodo/led/set
casa/cômodo/led/status
```

- Cômodo: nome do ambiente (cozinha, quarto1, quarto2, varanda, etc.)
- Led: dispositivo sendo controlado
- Set: publica comandos (liga/desliga)
- Status: retorna o estado atual do LED

# Como Compilar e Carregar

Para compilar e carregar, primeiramente você deve conectar a placa NodeMCU ao seu computador através de um cabo micro USB. Então, abra o projeto no Vs Code e, no canto superior direito da tela, pressione o botão para **buildar** [✅] que serve para compilar o código.

<p align="center">
<img alt="Compilar e Carregar" src="./Imagens/Compilar e Carregar (1).png" width="400">
</p>

Posteriormente, selecione a opção para fazer o **upload** [➡️], enviando o programa para a placa. Nessa etapa, será detectado automaticamente em qual porta USB a placa está conectada em sua máquina.

<p align="center">
<img alt="Compilar e Carregar" src="./Imagens/Compilar e Carregar (2).png" width="400">
</p>

Ao final, clique no botão com ícone de tomada (**Monitor Serial**) [🔌] para que a placa possa se comunicar com o computador. Dessa maneira, é possível acompanhar as mensagens de conexão.

<p align="center">
<img alt="Compilar e Carregar" src="./Imagens/Compilar e Carregar (3).png" width="400">
</p>

Lembrando que esses botões também estão disponíveis na barra inferior do VS Code, e você pode utilizá-los para realizar cada uma das ações.

<p align="center">
<img alt="Compilar e Carregar" src="./Imagens/Compilar e Carregar (4).png" width="1000">
</p>

# Testes e Exemplos



# Estrutura de Tópicos por Cômodo

| Cômodo   | Tópico Set             | Tópico Status             |
| -------- | ---------------------- | ------------------------- |
| Sala     | `casa/sala/led/set`    | `casa/sala/led/status`    |
| Cozinha  | `casa/cozinha/led/set` | `casa/cozinha/led/status` |
| Quarto 1 | `casa/quarto1/led/set` | `casa/quarto1/led/status` |
| Quarto 2 | `casa/quarto2/led/set` | `casa/quarto2/led/status` |
| Varanda  | `casa/varanda/led/set` | `casa/varanda/led/status` |

# Contribuindo

Alunos do 5° período de Sistemas de Informação da Unimater: Eduardo Giasson Correa, João Ricardo Criminácio e Yuri Schenkel. 

<p align="center">
  <img alt="Trio CCY" src="./Imagens/Trio CCY.jpg" width="600">
</p>
