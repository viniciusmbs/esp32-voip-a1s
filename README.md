Projeto Interfone VoIP com ESP32-A1S Audio Kit
Placa usada no projeto ESP32 AUDIO KIT V2.2 ai-thinker

<img src="https://i.imgur.com/mkMh8gi.jpeg" width="300">

Sua placa ESP32-A1S Audio Kit v2.2_A (Modelo com ESP32-WROVER)

Este projeto transforma a placa ESP32-A1S Audio Kit v2.2_A em um interfone IP funcional. Ele usa o framework ESP-ADF para fazer chamadas SIP de saída, ideal para portaria ou automação residencial. O desenvolvimento foi feito e testado no Ubuntu 20.04.6 LTS (VirtualBox).
Uma forma facíl de fazer chamadas da Placa para fora de sua rede local. Projeto criado para automação de um interfone e conseguir que o interfone façca um aligacao para seu celular quando acionado o botão de chamada do interfone. 

🧰 Hardware e Ambiente
Placa ESP32-A1S Audio Kit v2.2_A
A placa principal é a ESP32-A1S Audio Kit v2.2_A da Ai-Thinker, com módulo ESP32-WROVER.

Onde comprar: Link para a placa no AliExpress

Documentação: docs.ai-thinker.com/en/esp32-audio-kit

Driver de Áudio (Codec): Usa o codec ES8388. O driver da Ai-Thinker está em: github.com/Ai-Thinker-Open/ESP32-A1S-AudioKit/tree/master/ESP32_AC101_Driver/ai_thinker_audio_kit_v2_2

Pinagem de Áudio (Codec ES8388) e Botões
Para o áudio (codec ES8388) funcionar corretamente, pinos I2C e I2S são usados. Este projeto utiliza arquivos de configuração de placa (board files) adaptados para a ESP32-A1S v2.2_A, copiados para a estrutura do ESP-ADF (~/esp/esp-adf/components/audio_board/lyrat_v4_3/).

Pinos Chave de Áudio e Botões:

Pinos I2C: SDA (GPIO 33), SCL (GPIO 32)

Pinos I2S: BCK (GPIO 27), WS (GPIO 25), Data Out (GPIO 26), Data In (GPIO 35), MCLK (GPIO 0)

Botões (Pinos GPIO):
    * REC: GPIO 36
    * MODE: GPIO 13
    * SET: GPIO 19
    * PLAY: GPIO 23 (Usado para fazer a chamada)
    * VOLUP: GPIO 18
    * VOLDOWN: GPIO 5

Ambiente de Desenvolvimento (Detalhes)
Sistema Operacional: Ubuntu 20.04.6 LTS (VirtualBox)

Python: 3.8.10

Frameworks: ESP-IDF v4.2.5, ESP-ADF v2.2

Toolchain: xtensa-esp32-elf esp-2020r3-8.4.0

🚀 Configuração e Como Usar
1.  Configure seu Ambiente Linux:
    * Certifique-se de ter Ubuntu 20.04.6 LTS e Python 3.8.10.
    * Clone e prepare os frameworks:
        ```bash
        # Navegue para sua pasta de trabalho (ex: ~/)
        cd ~

        # Clone o ESP-IDF (v4.2.5) e ESP-ADF (v2.2)
        git clone -b v4.2.5 --recursive https://github.com/espressif/esp-idf.git esp/esp-idf
        git clone -b v2.2 --recursive https://github.com/espressif/esp-adf.git esp/esp-adf

        # Exporte as variáveis de ambiente (adicione ao ~/.bashrc ou ~/.profile para que sejam permanentes)
        export IDF_PATH=~/esp/esp-idf
        export ADF_PATH=~/esp/esp-adf
        . $IDF_PATH/export.sh
        ```
        (Consulte a documentação oficial da Espressif para mais detalhes de instalação.)

2.  Copie os Arquivos board Adaptados (CRÍTICO para funcionamento dos GPIOs):
    * Estes arquivos garantem a correta pinagem e funcionalidade da sua placa ESP32-A1S v2.2_A dentro do ESP-ADF.
    * Você deve copiar os arquivos do repositório viniciusmbs/audio_board para o seu ambiente local do ESP-ADF.
    * Os arquivos necessários são:
        * board.c
        * board.h
        * board_def.h
        * board_pins_conf.c
    * Eles estão localizados no repositório audio_board dentro da pasta esp32_audio_kit_v2_3.

    * Passos para copiar os arquivos:
        1. Primeiro, baixe ou clone o repositório audio_board para sua máquina (se ainda não o fez):
            bash             cd ~/Música             git clone https://github.com/viniciusmbs/audio_board.git             
            (Se você já fez isso, pode pular este passo.)

        2. Em seguida, copie os arquivos específicos da pasta esp32_audio_kit_v2_3 do seu repositório audio_board para o diretório lyrat_v4_3 do seu ESP-ADF:
            ```bash
            # Navegue para o diretório de componentes do ESP-ADF
            cd ~/esp/esp-adf/components/audio_board/lyrat_v4_3

            # Copie os arquivos do seu repositório local audio_board
            cp ~/Música/audio_board/esp32_audio_kit_v2_3/board.c .
            cp ~/Música/audio_board/esp32_audio_kit_v2_3/board.h .
            cp ~/Música/audio_board/esp32_audio_kit_v2_3/board_def.h .
            cp ~/Música/audio_board/esp32_audio_kit_v2_3/board_pins_conf.c .
            ```
            (Isto substituirá os arquivos padrão e é essencial para o correto reconhecimento e funcionamento da placa.)

3.  Clone este Projeto do Interfone:
    ```bash
    # Navegue para sua pasta de trabalho (ex: ~/)
    cd ~

    # Clone este repositório
    git clone https://github.com/viniciusmbs/esp32-voip-a1s.git

    # Entre no diretório do projeto clonado
    cd esp32-voip-a1s
    ```

4.  Configure o Projeto (idf.py menuconfig):
    Acesse o menu de configurações do projeto para sua rede e SIP.
    bash     idf.py menuconfig     
    No menuconfig:
    * Em Audio HAL, selecione ESP32-LyraT v4.3 Board.
    * Em VoIP App Configuration:
        * WiFi SSID: O nome da sua rede Wi-Fi (ex: Pandora).
        * WiFi Password: A senha da sua rede Wi-Fi (ex: vini5701).
        * SIP Codec: G711 PCMA.
        * SIP URI: O URI SIP de registro da sua placa. Ex: udp://interfone:SUA_SENHA@seu_servidor.sip.signalwire.com:5060.

5.  Acione a Chamada:
    * O interfone ligará quando o botão PLAY (GPIO 23) for pressionado.
    * No arquivo main/voip_app.c, a macro DESTINATION_SIP_URI define o ramal/número para onde a placa ligará (ex: #define DESTINATION_SIP_URI "1003").

6.  Compile, Flashee e Monitore:
    bash     idf.py build     idf.py -p /dev/ttyUSB0 flash monitor     
    (Substitua /dev/ttyUSB0 pela sua porta serial. Para sair do monitor, use Ctrl-].)

📞 Configuração da Plataforma VoIP (SignalWire)
Este projeto foi testado com sucesso na SignalWire. Para as chamadas funcionarem:

SIP Endpoint da Placa: Crie um Endpoint SIP na SignalWire (ex: interfone) com as credenciais. A placa se registrará aqui.

Destino da Chamada: O DESTINATION_SIP_URI (ex: 1003) na sua placa deve ser um ramal ou número configurado na SignalWire.

Roteamento de Chamadas (Call Flow): Na SignalWire, configure um Call Flow para o SIP ID da sua placa (ex: interfone). Este Call Flow direcionará a chamada para o destino final (ex: 1003, celular, um número fixo, etc.), podendo gerenciar múltiplas tentativas, mensagens de áudio e gravação.

📄 Licença
Este código é distribuído sob a Licença MIT (ou CC0, a seu critério).

🌟 Contribuição
Sinta-se à vontade para contribuir com melhorias, sugestões ou correções.
