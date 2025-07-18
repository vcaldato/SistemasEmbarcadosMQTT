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

Este projeto apresenta o desenvolvimento de um sistema básico de controle de iluminação utilizando o protocolo MQTT. Um microcontrolador NodeMCU será utilizado como publicador, enquanto o broker Mosquitto será responsável pelo envio dos comandos. Tais comandos controlarão o estado de um LED, simulando um cenário simples de automação residencial.

# Estrutura de Pastas

O código possui a seguinte estrutura
```
├── 📁 .pio/                      ← Pasta usada para arquivos de build
├── 📁 .vscode/                   ← Configurações do VSCode
├── 📁 include/                   ← Arquivos de cabeçalho
├── 📁 lib/                       ← Bibliotecas locais do projeto
├── 📁 src/                       ← Código-fonte principal
│   └── 📂 main.cpp               ← Arquivo principal do projeto
├── 📁 test/                      ← Testes unitários
├── 📄 platformio.ini             ← Arquivo de configuração do projeto
```

# Pré-requisitos

-Placa NodeMCU (ESP8266 V2 ou ESP8266 V3)
-Cabo USB do tipo Micro USB
-Broker Mosquitto
-Aplicativo MQTT Explorer
-Conexão Wi-Fi 
-Visual Studio Code
-PlatformIO instalada no VS Code

# Instalação e Configuração

## Driver NodeMCU

Para instalar os drivers da placa, primeiro é necessário identificar qual é a versão dela. 

<p align="center">
  <img alt="NodeMCU V2" src="./Imagens/Componente.png" width="400">
</p>

Depois, acesse o site da Robo Core ([clique aqui](https://www.robocore.net/tutoriais/instalando-driver-do-nodemcu)) para realizar o download do driver compatível com o seu sistema operacional. Após concluir o download, descompacte o arquivo e execute o instalador (.exe) com privilégios de administrador. Por fim, dependendo da versão da sua placa, será necessário avançar pelas etapas da instalação clicando em Next até a última tela e finalizar em Finish, ou simplesmente pressionar o botão Install.

## PlatformIO

### Instalação

Para instalar o PlatformIO, abra o Visual Studio Code, acesse a aba Extensões, pesquise por PlatformIO, selecione a extensão correspondente e clique em Install para concluir a instalação.

<p align="center">
<img alt="Instalação PlatformIO" src="./Imagens/PlatformIO (1).png" width="1000">
</p>

### Criação do Projeto

Após instalar a extensão, um ícone será exibido na barra lateral esquerda do Visual Studio Code. Clique nesse ícone, depois em Create New Project e, em seguida, em + New Project para iniciar a criação do seu projeto.

<p align="center">
<img alt="Instalação PlatformIO" src="./Imagens/PlatformIO (2).png" width="1000">
</p>

Por último, defina um nome para o projeto, selecione a placa NodeMCU 1.0 (ESP-12E Module) e clique em Finish. Assim, o projeto será gerado.

<p align="center">
<img alt="Instalação PlatformIO" src="./Imagens/PlatformIO (3).png" width="400">
</p>

## Mosquitto (Broker MQTT)

Para instalar o Mosquitto, acesse a página oficial do broker [(CLIQUE AQUI)](https://mosquitto.org/download/)  e baixe o executável correspondente ao seu sistema operacional e à arquitetura do seu computador (32 ou 64 bits). No caso de sistemas Linux, a instalação pode ser realizada via snap com o comando snap install mosquitto. Após concluir a instalação, vá até o diretório onde o Mosquitto foi instalado, abra o arquivo **mosquitto.conf** e adicione as seguintes configurações:
```
listener 1883 0.0.0.0
allow_anonymous true
```

### Liberação da porta 1883

Para que a aplicação funcione corretamente, é essencial criar uma regra no firewall permitindo a entrada pela porta TCP 1883 (essa etapa não é necessária em sistemas Linux). Para criar a regra, abra o Windows Defender Firewall com Segurança Avançada, acesse Regras de Entrada e clique em Nova Regra.

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

Para instalar o MQTT Explorer, acesse o site oficial  ([clique aqui](https://mqtt-explorer.com/)), navegue até a área de Download e faça a instalação conforme o sistema operacional do seu computador. Além disso, no arquivo platformio.ini do seu projeto, é fundamental incluir a dependência abaixo:
```
lib_deps =
 knolleary/PubSubClient
```

Além disso, ao iniciar o aplicativo, é necessário configurar o Host como 127.0.0.1 ou localhost, definir a Porta 1883 e clicar em Connect. Se desejar, é possível modificar o nome da conexão no campo Name.

# Código Fonte

Os arquivos deste projeto encontram-se no diretório Sistemas Embarcados > Projeto MQTT - NodeMCU > src.

## Configurações Wi-Fi e MQTT

No começo, realizam-se as importações das bibliotecas, a definição das credenciais da rede Wi-Fi e a configuração do broker MQTT, incluindo IP, porta e tópicos para publicação e assinatura.
```C++
#include <ESP8266WiFi.h>
#include <PubSubClient.h>

const char* ssid = "Prof.Rafael";
const char* password = "senha123";
const char* mqtt_server = "192.168.58.226";
const int mqtt_port = 1883;

// Define os pinos para cada cômodo
#define LED_SALA    D0
#define LED_COZINHA D1
#define LED_QUARTO1 D2
#define LED_QUARTO2 D3
#define LED_VARANDA D4

// Tópicos de controle e status
const char* sub_topics[] = {
  "casa/sala/led/set",
  "casa/cozinha/led/set",
  "casa/quarto1/led/set",
  "casa/quarto2/led/set",
  "casa/varanda/led/set"
};

const char* pub_topics[] = {
  "casa/sala/led/status",
  "casa/cozinha/led/status",
  "casa/quarto1/led/status",
  "casa/quarto2/led/status",
  "casa/varanda/led/status"
};

// Correspondência dos LEDs com os tópicos
const uint8_t led_pins[] = {
  LED_SALA,
  LED_COZINHA,
  LED_QUARTO1,
  LED_QUARTO2,
  LED_VARANDA
};

const int num_comodos = sizeof(led_pins) / sizeof(led_pins[0]);

WiFiClient espClient;
PubSubClient client(espClient);
``` 

A função ```setup_wifi()``` conecta o NodeMCU à rede Wi-Fi:
```C++
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

A função reconnect() é responsável por gerenciar a conexão com o broker MQTT. Nela, o usuário recebe notificações informando que a tentativa de conexão está em andamento, se foi bem-sucedida ou se houve algum erro.
```C++
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

A função callback() é responsável pelo controle do LED e é ativada sempre que o NodeMCU recebe uma mensagem no tópico em que está inscrito.
```C++
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

```sh
casa/cômodo/led/set
casa/cômodo/led/status
```

- Cômodo: nome do ambiente (cozinha, quarto1, quarto2, varanda, etc.)
- Led: dispositivo sendo controlado
- Set: publica comandos (liga/desliga)
- Status: retorna o estado atual do LED

# Como Compilar e Carregar

Para compilar e enviar o código, conecte primeiro a placa NodeMCU ao computador usando um cabo micro USB. Em seguida, abra o projeto no VS Code e clique no botão de build [✅], localizado no canto superior direito, para compilar o programa.


Depois, escolha a opção de upload [➡️] para enviar o programa à placa. Nesta etapa, a porta USB em que a placa está conectada será identificada automaticamente pelo sistema.

Por fim, clique no botão com o ícone de tomada (Monitor Serial) [🔌] para habilitar a comunicação entre a placa e o computador. Assim, você poderá acompanhar as mensagens referentes à conexão.

Vale lembrar que esses botões também ficam disponíveis na barra inferior do VS Code, e podem ser usados para executar cada uma dessas ações.



# Testes e Exemplos

<p align="center">
<img alt="Compilar e Carregar" src="./Imagens/led.jpg" width="1000">
</p>

<p align="center">
<img alt="Compilar e Carregar" src="./Imagens/Not.jpg" width="1000">
</p>

# Estrutura de Tópicos por Cômodo

| Cômodo   | Tópico Set             | Tópico Status             |
| -------- | ---------------------- | ------------------------- |
| Sala     | `casa/sala/led/set`    | `casa/sala/led/status`    |
| Cozinha  | `casa/cozinha/led/set` | `casa/cozinha/led/status` |
| Quarto 1 | `casa/quarto1/led/set` | `casa/quarto1/led/status` |
| Quarto 2 | `casa/quarto2/led/set` | `casa/quarto2/led/status` |
| Varanda  | `casa/varanda/led/set` | `casa/varanda/led/status` |

# Contribuindo

Alunos do 5° período de Sistemas de Informação da Unimater: Vinicius Caldato, Victor Hugo, William Linhares, Thiago Henrique
