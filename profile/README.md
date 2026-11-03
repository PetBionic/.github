# PetBionic

Prótese canina instrumentada, desenvolvida na **Composites Kingdom** no âmbito da tese de
mestrado de Afonso Cruz. A prótese mede força e movimento durante a marcha do animal, grava
sessões e permite analisá-las e classificá-las com aprendizagem automática.

## Mapa do projeto

| Repositório | O que faz | Onde |
| ----------- | --------- | ---- |
| [PetBionicPCBs](https://github.com/PetBionic/PetBionicPCBs) | **Hardware.** Projetos KiCad e pacotes de produção. V1 (rev1, tag `v1-final`) com XIAO ESP32-C3; V2 com ESP32-S3 em duas placas: principal (`V2_Main`, prótese e auxiliares com DNP) e tampa (`V2_Lid`). Revisões "Cachorro" (bancada) e "Galgo Inglês" (vai para a cadela). | organização |
| [PetBionicFirmware](https://github.com/PetBionic/PetBionicFirmware) | **Firmware.** Aquisição de força, IMU e magnetómetro, gravação de sessões, controlo por BLE. V1 preservada na tag `v1-final`; V2 com ambientes `prosthesis` e `auxiliary`. Define o contrato dos ficheiros de sessão em `docs/session-format.md`. | organização |
| [PetBionicAnalyser](https://github.com/PetBionic/PetBionicAnalyser) | **Python único: app + análise + ML.** `io` lê e sincroniza as placas; `dataset` segmenta passadas, aplica os rótulos do veterinário (intervalos de tempo) e gera janelas; `ml` treina modelos end-to-end e clássicos. Inclui a GUI de visualização. Dados em `PETBIONIC_DATA`. | organização |
| [PetBionicApp](https://github.com/PetBionic/PetBionicApp) | **App** Capacitor (iOS/Android/web): controlo e preview em tempo real por BLE, gravação do vídeo, classificação dos clips pelos veterinários e resultados da análise. Ainda sem código. | organização |
| [PetBionicBackend](https://github.com/PetBionic/PetBionicBackend) | **Servidor.** PocketBase self-hosted na Composites: base de dados, sessões e vídeos em disco, e um serviço Python que corta os vídeos em clips e corre o Analyser. Ainda por instalar. | organização |
| [PetBionicThesis](https://github.com/Zurcaf/PetBionicThesis) | **Tese** (LaTeX, ligada ao Overleaf). Privado, na conta pessoal. | conta pessoal |

## Como se ligam

```
PetBionicPCBs ──▶ PetBionicFirmware ──▶ ficheiros de sessão ──▶ PetBionicAnalyser ──▶ PetBionicThesis
   (hardware)        (aquisição)          (contrato: formato)     io → dataset → ML       (resultados)
                           ▲                      │
                           │ BLE (controlo,       │ Wi-Fi (upload)
                           │ preview)             ▼
                      PetBionicApp ◀────────▶ PetBionicBackend
```

1. Os **PCBs** definem os sensores e o pinout que o **Firmware** usa.
2. O **Firmware** grava **ficheiros de sessão** num formato versionado, que é o contrato
   com o Analyser.
3. A **App** controla as placas por BLE e grava o vídeo; as placas enviam as sessões por
   Wi-Fi para o **Backend**, que corta o vídeo em clips para os veterinários classificarem
   na app.
4. O **Analyser** lê as sessões (`io`), constrói o dataset com os rótulos do veterinário
   (`dataset`) e treina/avalia modelos (`ml`).
5. Os resultados e figuras alimentam a **Tese**.
