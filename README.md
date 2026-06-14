# EV ChargeOps — Plataforma de Gestão de Recarga Compartilhada

**Enterprise Challenge 2026 — GoodWe × FIAP**

**Equipe:** GustavoSolutions 

**Integrante:** Gustavo Matheus Prado Moraes — RM: rm570671

---

## Descrição do Problema

O crescimento acelerado de veículos elétricos no Brasil criou uma lacuna operacional concreta: condomínios residenciais, edifícios corporativos e campus universitários que instalam carregadores compartilhados não dispõem de mecanismos integrados para identificar quem carregou, quanto consumiu e quanto deve pagar.

O resultado prático é um cenário de conflito: a energia entra na conta do condomínio, mas não existe rastreabilidade individual. Ou o gasto é absorvido por todos os moradores de forma injusta, ou o síndico passa a fazer controle manual — uma tarefa inviável à medida que a frota cresce.

O EV ChargeOps transforma cada sessão de recarga em dados estruturados, aplicando regras de rateio automáticas e inteligência artificial para gerar faturas individuais, detectar anomalias e otimizar o uso da infraestrutura compartilhada.

---

## Frente 1 — Contexto e Problema

### O que é infraestrutura de recarga compartilhada

Uma infraestrutura de recarga compartilhada consiste em um ou mais carregadores instalados em área comum — garagem de condomínio, estacionamento corporativo ou campus universitário — disponíveis para múltiplos usuários. Em vez de cada morador ter um equipamento individual, o compartilhamento reduz o custo de instalação e o número de pontos de acesso à rede elétrica.

Os principais desafios operacionais enfrentados pelos gestores são:

- Ausência de medição individual por sessão, o que inviabiliza a cobrança justa
- Conflitos entre moradores pelo uso do espaço e do equipamento
- Dificuldade de estimar a demanda futura e o impacto na carga elétrica do edifício
- Falta de uma interface digital para que moradores acompanhem seu próprio consumo
- Ausência de alertas para sessões travadas, equipamentos com falha ou uso fora do horário permitido

### Como funciona uma sessão de recarga

Do ponto de vista técnico, uma sessão de recarga passa pelas seguintes etapas:

1. **Autenticação:** o usuário se identifica via cartão RFID, aplicativo ou QR Code. O sistema verifica se a identidade está cadastrada e autorizada.
2. **Handshake elétrico (IEC 61851):** após a conexão física do cabo, o carregador e o veículo trocam sinais de controle pelo pino CP (Control Pilot) para verificar a compatibilidade e definir a corrente máxima disponível.
3. **Início da sessão:** o carregador registra timestamp de início, identidade do usuário e potência inicial disponível.
4. **Transferência de energia:** a corrente alternada (AC) flui do carregador para o onboard charger do veículo, que converte para DC e carrega a bateria. A potência pode variar durante a sessão conforme a carga da bateria.
5. **Monitoramento contínuo:** a cada intervalo (geralmente 15 segundos a 1 minuto), o carregador registra potência instantânea (kW), tensão, corrente e energia acumulada (kWh).
6. **Encerramento:** o usuário desconecta o cabo ou a sessão atinge o limite programado. O carregador registra o timestamp final, o total de energia entregue e o status de encerramento.

Os dados gerados por sessão incluem: ID de sessão, ID de usuário, timestamp de início, timestamp de fim, energia total entregue (kWh), potência média (kW), potência máxima (kW), status de encerramento e eventuais códigos de erro.

### Modelos de negócio para recarga compartilhada

No Brasil e no mundo, existem ao menos quatro modelos estabelecidos:

**Cobrança por kWh consumido:** o modelo mais justo do ponto de vista técnico. Cada usuário paga exatamente pela energia que consumiu, calculada com base na tarifa local da distribuidora mais uma margem operacional do condomínio ou operador. É o modelo recomendado pela ABNT NBR 17019:2022 para instalações com medição individualizada.

**Cobrança por tempo de sessão:** o usuário paga por hora de conexão, independentemente de quanto carregou. É mais simples de implementar, mas cria distorções — veículos com bateria maior ou mais antiga podem consumir mais energia no mesmo tempo.

**Assinatura mensal fixa:** o usuário paga um valor fixo por mês independentemente do uso. Adequado para ambientes onde o perfil de uso é homogêneo, mas injusto quando há grande variação entre usuários.

**Rateio condominial proporcional ao consumo:** a fatura de energia do carregador é separada da fatura geral do condomínio. Ao final de cada mês, o total consumido é dividido entre os usuários proporcionalmente ao kWh individual de cada um. É o modelo adotado por plataformas como EvoWatt e âmbar-e no mercado brasileiro.

O EV ChargeOps adota o modelo de rateio proporcional ao consumo em kWh, detalhado na Frente 3.

---

### Opção C — Análise de Dados Públicos: Frota EV no Brasil e Distribuição de Pontos de Recarga

#### Crescimento da frota

Segundo a Associação Brasileira do Veículo Elétrico (ABVE), a frota acumulada de veículos eletrificados no Brasil ultrapassou 705 mil unidades em março de 2026, considerando BEVs, PHEVs, HEVs e HEV Flex desde o início da série histórica em 2012. Apenas nos três primeiros meses de 2026, foram emplacados 83.947 veículos eletrificados — um ritmo que, mantido, pode levar o Brasil a superar 300 mil emplacamentos no ano, contra o recorde de 223.912 registrado em 2025.

A distribuição por estado (acumulado de jan/2022 a jan/2026) revela forte concentração no Sudeste:

| Estado | Veículos eletrificados |
|---|---|
| São Paulo | 181.305 |
| Distrito Federal | 48.502 |
| Rio de Janeiro | 39.295 |
| Minas Gerais | 37.897 |
| Paraná | 37.703 |

São Paulo concentra mais de 40% da frota eletrificada do país, o que reforça a relevância de soluções como o EV ChargeOps para o mercado paulista — e, especificamente, para o contexto do campus FIAP Aclimação.

Em fevereiro de 2026, os veículos eletrificados atingiram 14% das vendas totais de veículos leves no Brasil, com crescimento de 92% em relação a fevereiro de 2025. Os modelos 100% elétricos (BEV) lideram em 2026 com 37% das vendas eletrificadas, seguidos pelos híbridos plug-in (PHEV) com 35%.

#### Infraestrutura de recarga pública

A última atualização oficial da ABVE, de setembro de 2025, registrava 16.880 eletropostos no Brasil, sendo 13.025 de corrente alternada (AC) e 3.855 de corrente contínua (DC). Especialistas estimavam que esse número chegaria a cerca de 20 mil ainda no primeiro trimestre de 2026.

A relação entre frota e eletropostos revela o desequilíbrio: há aproximadamente 38 veículos eletrificados por ponto de recarga público. Isso não significa, necessariamente, uma crise de infraestrutura — a maioria das recargas ocorre em casa ou no trabalho — mas evidencia que a infraestrutura privada e semipública (como em condomínios e campus) tem papel central na viabilidade da eletromobilidade.

#### Implicação para o EV ChargeOps

A concentração da frota em São Paulo e o crescimento acelerado de veículos plug-in (BEV + PHEV) são os dois fatores que tornam o problema do rateio urgente. Condomínios que hoje têm 1 ou 2 usuários de EV terão 10 ou 20 em dois ou três anos. Implantar uma plataforma de gestão antes que o problema se agrave é mais eficiente do que remediar depois.

**Fontes:** ABVE (abve.org.br), Canal VE (canalve.com.br), Reconecta News (reconectanews.com.br)

---

## Frente 2 — Base Regulatória e Técnica

### Resolução Normativa ANEEL nº 1.000/2021

A RN 1.000/2021 consolidou em um único documento as regras de prestação do serviço público de distribuição de energia elétrica. Para a operação de carregadores veiculares, os pontos mais relevantes são:

**Definição de estação de recarga (Art. 3º, XV):** a norma define estação de recarga como "conjunto de softwares e equipamentos utilizados para o fornecimento de corrente alternada ou contínua ao veículo elétrico, instalado em um ou mais invólucros, com funções especiais de controle e de comunicação, e localizados fora do veículo". Essa definição inclui o GoodWe HCA G2 instalado no campus FIAP.

**Aumento de carga:** sempre que a instalação de um carregador exigir aumento da carga contratada da unidade consumidora, o condomínio ou responsável deve solicitar formalmente à distribuidora (no caso de São Paulo, a Enel/Neoenergia). O projeto elétrico deve ser assinado por engenheiro habilitado com ART.

**Comunicação à distribuidora para exploração comercial:** quando o carregador é usado para exploração comercial — ou seja, cobra dos usuários pelo serviço — há obrigação de comunicação prévia à distribuidora. Para uso interno em condomínio com rateio entre os próprios moradores, a interpretação predominante é de que se trata de uso privado, mas recomenda-se consulta à distribuidora.

**Protocolos abertos:** a norma exige protocolos abertos de comunicação para equipamentos não exclusivos de uso privado. O OCPP (Open Charge Point Protocol) é o protocolo padrão de mercado e está presente nos carregadores compatíveis com a plataforma SEMS da GoodWe.

**ABNT NBR 17019:2022:** complementar à RN 1.000/2021, esta norma estabelece os requisitos para instalação de infraestrutura de recarga de veículos elétricos, incluindo diretrizes de medição individualizada — base técnica para o modelo de rateio do EV ChargeOps.

### Carregador GoodWe HCA G2 — Interfaces e Capacidades

O GoodWe HCA G2 é um carregador AC disponível nas potências de 7 kW, 11 kW e 22 kW, com classificação IP66 (instalação interna ou externa), cabo de 6 metros e conector Tipo 2 (IEC 62196), compatível com a maioria dos veículos elétricos comercializados no Brasil.

As interfaces de conectividade e o que cada uma permite fazer na plataforma EV ChargeOps:

| Interface | Função no equipamento | Uso na plataforma |
|---|---|---|
| RS-485 | Comunicação com inversores GoodWe e medidores MID via protocolo Modbus RTU | Integração com sistema fotovoltaico do campus FIAP; leitura de dados de energia do medidor MID |
| LAN (Ethernet) | Conexão cabeada ao roteador local | Canal principal de comunicação com o back-end quando Wi-Fi não está disponível ou para ambientes que exigem maior estabilidade |
| Wi-Fi | Conexão sem fio ao roteador local | Canal de comunicação mais comum em instalações residenciais e campus; permite mobilidade na instalação sem passar cabos |
| Bluetooth | Configuração local via aplicativo SolarGo | Usado na instalação inicial e em manutenção; não adequado para comunicação contínua com o back-end |
| RFID | Autenticação do usuário por cartão ou tag | Mecanismo principal de identificação do usuário na sessão de recarga; o cartão RFID de cada morador vincula a sessão ao perfil cadastrado |

Os modos de carregamento disponíveis no HCA G2 são: Veloz (potência nominal da rede), Prioridade FV (usa energia solar excedente) e Prioridade FV + Bateria (usa solar e bateria de armazenamento antes da rede). Para o contexto do campus FIAP, o modo Prioridade FV integra o carregador ao sistema fotovoltaico do Energy Innovation Lab.

---

### Opção B — Exploração da API GoodWe SEMS

#### Visão geral da API

A GoodWe oferece três tipos de API pelo SEMS (Smart Energy Management System):

- **Open API:** exclusiva para usuários com conta de organização no SEMS. Dá acesso a todos os dispositivos da organização, suporta leitura de dados e controle remoto. Usa protocolo HTTPS.
- **Real-time Data Monitoring API:** permite que fornecedores terceiros leiam dados em tempo real de inversores e carregadores autorizados, sem controle remoto.
- **Batch Remote Control Interface:** permite controle remoto em lote de dispositivos autorizados.

Para o EV ChargeOps, a Open API é a mais relevante — permite ler dados de sessão, status do carregador e energia entregue, além de acionar comandos de início e parada de carga.

#### Autenticação

O acesso à API SEMS é feito via autenticação por token. O fluxo é:

1. Enviar credenciais ao endpoint `POST /api/v1/Common/CrossLogin` com headers específicos de versão e cliente.
2. Receber em resposta um objeto JSON com os campos `token`, `uid`, `timestamp` e `api` (URL base para chamadas subsequentes).
3. Usar `token`, `uid` e `timestamp` como headers em todas as chamadas subsequentes.

Exemplo de requisição de autenticação:
```
POST https://www.semsportal.com/api/v1/Common/CrossLogin
Header: Token: {"version":"v2.1.0","client":"ios","language":"pt"}
Body: {"account":"email@dominio.com","pwd":"senha"}
```

#### Endpoints relevantes para o EV ChargeOps

| Endpoint | Método | Descrição | Campos retornados |
|---|---|---|---|
| `/powerstation/EvChargerDetail?stationId={id}` | GET | Detalhes do carregador vinculado a uma estação | status, potência atual, energia da sessão, estado do veículo |
| `/PowerStation/GetMonitorDetailByPowerstationId` | GET | Dados gerais da planta / estação | geração, consumo, dados do inverter |
| `/PowerStation/GetPowerStationByUser` | GET | Lista de estações do usuário autenticado | IDs, nomes, localizações |

#### Formato dos dados retornados (JSON simulado — campo EV Charger)

```json
{
  "data": {
    "charger_sn": "GW-HCA-G2-XXXXXXXX",
    "status": "charging",
    "current_power_kw": 7.2,
    "session_energy_kwh": 12.5,
    "session_start": "2026-06-14T08:32:00Z",
    "rfid_tag": "A3B2C1D0",
    "vehicle_state": "connected_charging",
    "temperature_c": 34
  },
  "msg": "Success",
  "code": 0
}
```

Os campos de maior relevância para o EV ChargeOps são `status`, `current_power_kw`, `session_energy_kwh`, `session_start` e `rfid_tag`. Esses campos alimentam diretamente o modelo de rateio — a energia entregue por sessão (`session_energy_kwh`) é a variável central do cálculo.

#### Limitação importante

A documentação pública da API SEMS é parcialmente restrita — parte dos endpoints está documentada na comunidade GoodWe (community.goodwe.com) e parte foi mapeada pela comunidade de desenvolvedores via engenharia reversa do aplicativo. Para acesso oficial à Open API, é necessário solicitar credenciais à equipe GoodWe SEMS, o que foi considerado no plano da Sprint 02.

**Fontes:** GoodWe Community API Technical Document, Medium (binodmx), GitHub (TimSoethout/goodwe-sems-home-assistant), Manual HCA G2 V1.5

---

## Frente 3 — Arquitetura e IA

### Camadas da Plataforma EV ChargeOps

A plataforma é organizada em quatro camadas:

**Camada Física:** o carregador GoodWe HCA G2 instalado no estacionamento L1 do campus FIAP Aclimação. É o ponto de coleta de dados brutos — energia, tempo, identidade do usuário via RFID.

**Camada de Conectividade:** comunicação entre o carregador e o back-end via Wi-Fi/LAN usando o protocolo OCPP 1.6 (ou a API SEMS como alternativa). Os eventos de sessão (início, progresso, fim) são enviados ao servidor como mensagens JSON.

**Camada de Aplicação:** o back-end da plataforma, responsável por receber os eventos, armazená-los no banco de dados, aplicar as regras de rateio, executar os modelos de IA e gerar as faturas mensais. Também expõe as APIs consumidas pelas interfaces.

**Camada de Apresentação:** duas interfaces distintas — o painel do gestor (síndico ou administrador do campus), com visão consolidada de consumo, usuários e alertas; e o app do usuário, com histórico de sessões, consumo individual e fatura do mês.

### Diagrama de Arquitetura

> Veja o arquivo `diagrama_arquitetura.svg` incluído neste repositório.

```
[Veículo Elétrico]
       |
       | Cabo Tipo 2
       v
[GoodWe HCA G2]  ←→  [RFID / App]
       |
       | Wi-Fi / LAN (OCPP 1.6 ou API SEMS)
       v
[Back-end EV ChargeOps]
  ├── Receptor de Eventos OCPP
  ├── Motor de Rateio
  ├── Motor de IA
  └── API REST
       |
  ┌────┴────┐
  ▼         ▼
[Painel   [App do
Gestor]   Usuário]
  |
  ▼
[Banco de Dados]
 ├── Usuários
 ├── Sessões
 ├── Faturas
 └── Eventos
```

### Como os dados fluem da sessão até a fatura

1. **Captura:** o usuário autentica via RFID. O carregador registra o evento `StartTransaction` com RFID tag, timestamp e ID da sessão, e envia ao back-end via OCPP/API SEMS.
2. **Monitoramento:** a cada 60 segundos, o carregador envia `MeterValues` com a energia acumulada da sessão (kWh). O back-end armazena cada leitura na tabela `sessao_leituras`.
3. **Encerramento:** o usuário desconecta. O carregador envia `StopTransaction` com o total de energia entregue. O back-end marca a sessão como concluída e calcula a duração.
4. **Processamento mensal:** no último dia de cada mês, o motor de rateio agrega todas as sessões do período, calcula o total de kWh por usuário e aplica o modelo de faturamento.
5. **Geração da fatura:** o back-end calcula o valor individual de cada usuário (detalhado abaixo) e persiste na tabela `faturas`. O app do usuário exibe a fatura e o histórico de sessões.
6. **IA:** os modelos de IA são executados em paralelo, produzindo alertas de anomalia e previsões de consumo que alimentam o painel do gestor.

### Modelo de Rateio

O EV ChargeOps adota o modelo de **rateio proporcional ao consumo em kWh com encargos fixos rateados**, por ser o mais justo e transparente para ambientes de uso coletivo variável.

#### Variáveis do modelo

| Variável | Descrição |
|---|---|
| `E_i` | Energia total consumida pelo usuário i no mês (kWh) |
| `E_total` | Soma de energia consumida por todos os usuários no mês (kWh) |
| `C_energia` | Custo total da energia elétrica na fatura do carregador no mês (R$) |
| `C_fixo` | Custo fixo mensal do sistema (manutenção, plataforma, taxa administrativa) (R$) |
| `N_usuarios` | Número de usuários ativos no mês (com ao menos uma sessão) |

#### Fórmula de cálculo

```
Fatura_i = (E_i / E_total) × C_energia + (C_fixo / N_usuarios)
```

O componente variável `(E_i / E_total) × C_energia` garante que quem consumiu mais paga mais. O componente fixo `C_fixo / N_usuarios` divide os custos de infraestrutura apenas entre quem usou o serviço no mês.

#### Tratamento de casos excepcionais

**Sessão interrompida (falha de energia, erro do equipamento):** a plataforma registra a última leitura de `MeterValues` antes da interrupção. A energia efetivamente entregue até aquele ponto é contabilizada normalmente. Sessões interrompidas por falha do equipamento (não do usuário) têm o custo absorvido pelo fundo de manutenção.

**Usuário que não carregou no mês:** `E_i = 0`, portanto o componente variável é zero. O usuário não paga pela energia. O custo fixo só é cobrado de usuários ativos (`N_usuarios` conta apenas quem teve ao menos uma sessão).

**Dois veículos da mesma unidade:** cada veículo/usuário deve ter um cartão RFID próprio cadastrado. As sessões são somadas por unidade (apartamento ou vaga) para efeito de faturamento consolidado, mas mantidas separadas no histórico para rastreabilidade.

---

### Opção B — Papel da IA na Solução

A IA no EV ChargeOps não é decorativa — ela resolve problemas que a lógica de regras fixa não consegue endereçar. Duas abordagens foram escolhidas:

#### Abordagem 1 — Detecção de Anomalias (Isolation Forest)

**Problema que resolve:** identificar sessões com comportamento atípico — consumo muito acima do padrão do usuário, sessões que duram muitos dias sem encerramento formal, potência constantemente abaixo do mínimo esperado (indicando falha no carregador) ou uso em horários não autorizados.

**Técnica:** Isolation Forest, um algoritmo de aprendizado não supervisionado que isola pontos de dados anômalos sem necessitar de exemplos rotulados de anomalias. Ideal para o contexto porque não é possível rotular antecipadamente o que constitui uma anomalia — o modelo aprende o padrão normal e sinaliza desvios.

**Dados necessários:** histórico de sessões com campos `duracao_minutos`, `energia_kwh`, `potencia_media_kw`, `hora_inicio`, `dia_semana`, `usuario_id`. Com 30 dias de dados de operação já é possível treinar o modelo inicial.

**Impacto esperado:** redução de disputas entre moradores por sessões não encerradas que bloqueiam o carregador; detecção precoce de falhas de equipamento antes que causem prejuízo; alertas automáticos para o gestor.

#### Abordagem 2 — Previsão de Demanda (Regressão com séries temporais)

**Problema que resolve:** prever o consumo total de energia e o número de sessões esperado para os próximos dias ou semanas, permitindo que o gestor antecipe picos de demanda, planeje manutenções e negocie melhor a tarifa com a distribuidora.

**Técnica:** regressão com features de séries temporais (dia da semana, hora do dia, semana do mês, temperatura, feriados) usando um modelo leve como XGBoost ou Prophet (Facebook). A variável alvo é `energia_kwh_dia` ou `numero_sessoes_dia`.

**Dados necessários:** histórico diário de consumo total, calendário de feriados, dados climáticos básicos (temperatura máxima do dia). Com 60 a 90 dias de operação, o modelo já produz previsões com boa acurácia para horizontes de 7 a 14 dias.

**Impacto esperado:** painel de previsão para o gestor com estimativa de consumo semanal; geração proativa de alertas quando a demanda prevista excede a capacidade elétrica disponível; base para otimização de tarifas em horários de menor custo.

---

## Diagrama de Arquitetura

O diagrama completo está no arquivo `diagrama_arquitetura.svg` neste repositório.

Em resumo, a arquitetura segue o fluxo:

```
Usuário (RFID/App) → GoodWe HCA G2 → API SEMS / OCPP → Back-end EV ChargeOps
                                                              ↓
                                                    Banco de Dados (PostgreSQL)
                                                    Motor de Rateio
                                                    Motor de IA (Anomalia + Previsão)
                                                              ↓
                                              Painel do Gestor | App do Usuário
```

---

## Plano para a Sprint 02

### O que será desenvolvido

A Sprint 02 implementa o núcleo funcional da plataforma EV ChargeOps com base nas decisões desta Sprint 01.

**Ordem de desenvolvimento:**

1. **Banco de dados (semana 1):** criação do schema PostgreSQL com as entidades Usuario, Veiculo, Sessao, SessaoLeitura, Fatura e Evento. Carga de dados simulados baseados no dataset Kaggle "Electric Vehicle Charging Sessions".

2. **Integração com a API SEMS (semana 1-2):** desenvolvimento do conector que autentica na API GoodWe, faz polling do endpoint `EvChargerDetail` e persiste os eventos de sessão no banco.

3. **Motor de rateio (semana 2):** implementação da função de cálculo de fatura conforme o modelo definido, com cobertura de casos excepcionais.

4. **Modelos de IA (semana 3):** treinamento do Isolation Forest para detecção de anomalias e do modelo de previsão de demanda com os dados simulados. Exposição dos resultados via API REST.

5. **Interfaces (semana 4):** painel do gestor (web) com visão de consumo, usuários ativos, alertas e previsão. App do usuário (mobile ou web responsivo) com histórico de sessões e fatura.

6. **Testes e ajustes (semana 5-6):** testes de integração, ajuste dos modelos de IA com mais dados e preparação do vídeo pitch.

### Tecnologias

| Camada | Tecnologia |
|---|---|
| Back-end | Python (FastAPI) |
| Banco de dados | PostgreSQL |
| IA | scikit-learn (Isolation Forest), Prophet ou XGBoost  |
| Integração carregador | API SEMS GoodWe (HTTPS + JSON) |
| Painel do gestor | React ou HTML/CSS  |
| App do usuário | React Native ou PWA |
| Deploy | Railway / Render  |

---

## Fontes Consultadas

- ABVE — Associação Brasileira do Veículo Elétrico. Estatísticas de emplacamentos. abve.org.br
- Canal VE. "Frota de eletrificados no Brasil ultrapassa 700 mil unidades." canalve.com.br, abril 2026.
- Reconecta News. "Veículos eletrificados no Brasil podem chegar a 300 mil vendas em 2026." reconectanews.com.br, março 2026.
- ANEEL. Resolução Normativa nº 1.000/2021. aneel.gov.br
- OAB/PE Editora Digital. "Aumento de carga em condomínios com estações de recarga de veículos elétricos." editoraoabdigital.org.br, setembro 2025.
- GoodWe. User Manual AC Charger HCA Series G2 V1.5. mg-solar-shop.com
- Canal Solar. "GoodWe HCA G2: carregamento inteligente para veículos elétricos." canalsolar.com.br, setembro 2025.
- GoodWe Community. API Technical Document. community.goodwe.com
- Karunanayake, Binod. "Accessing GoodWe Sems Portal API." Medium, maio 2024.
- GitHub — TimSoethout/goodwe-sems-home-assistant. Discussão #179. github.com
- âmbar-e. "Carregamento compartilhado em condomínios." ambare.com.br, julho 2025.
- Power2Go. "Cobrança por kWh ou assinatura mensal?" power2go.com.br, abril 2025.
- EvoWatt. "Quanto custa carregar um carro elétrico?" evowatt.com.br, fevereiro 2026.
- ABNT NBR 17019:2022 — Infraestrutura de recarga para veículos elétricos.
- Kaggle. Dataset "Electric Vehicle Charging Sessions". kaggle.com
