# Relatório Técnico de Engenharia: Análise de Viabilidade do RFID para Medição Angular Absoluta

---

## 1. Visão Geral e Fundamentos do RFID no Projeto

### 1.1 Tecnologia Proposta

A arquitetura avaliada visa empregar Identificação por Radiofrequência (**RFID**) como transdutor de posicionamento angular absoluto em um anel rotativo estrutural de grande porte ($P = 47\text{ m}$, diâmetro nominal $\approx 15\text{ m}$), buscando uma resolução angular discreta de **0,5°** (equivalente a 720 divisões ao longo da circunferência).

* **Faixas de Frequência Avaliadas:**
* **UHF (860 a 960 MHz, padrão Anatel 902–928 MHz):** Propagação eletromagnética em campo distante com retroespalhamento modulado (*backscatter*). Leitores integrados compactos IP67 (ex.: **SICK RFU620-10101 / RFU620-10104**) operando com transponders industriais blindados para fixação direta em superfícies metálicas (*on-metal*).
* **HF (13,56 MHz, ISO 15693 / Moby D):** Acoplamento indutivo magnético de campo próximo (ex.: **Siemens SIMATIC RF200** com tags **MDS D424** encapsuladas com núcleo de ferrite e fixação central M4).




* **Classificação dos Transponders:** Tags passivas sem bateria interna, energizadas por indução eletromagnética ou retificação da onda portadora de RF irradiada pelo interrogador.

### 1.3 Objetivo Inicial do Estudo

Substituir encoders acoplados por atrito ou rodas de medição mecânicas suscetíveis a escorregamento (*slip*), eliminando erros cumulativos de deriva posicional e provendo capacidade de inicialização direta em coordenada absoluta sem necessidade de rotinas demoradas de referenciamento mecânico (*homing*).

## 2. Arquitetura de Instalação e Integração

### 2.1 Instalação Mecânica e Propagação de RF

* **Geometria do Feixe de Leitura:**
* **UHF (RFU620):** Antena integrada com polarização circular e ângulo de abertura nominal entre $60^\circ$ e $75^\circ$. Emite um cone tridimensional de irradiação cujo diâmetro transversal a $0{,}5\text{ m}$ de distância ultrapassa $0{,}6\text{ m}$.
* **HF (RF200):** Acoplamento indutivo toroidal concentrado. A bolha de leitura possui perfil esferoidal com diâmetro útil de $80\text{ mm}$ a $120\text{ mm}$ nos cabeçotes médios e alcance frontal ($S_n$) entre $20\text{ mm}$ e $60\text{ mm}$.


* **Suportes Mecânicos e Tolerâncias Estruturais:**
* A estrutura rotativa com perímetro de $47\text{ m}$ apresenta excentricidades radiais, dilatação térmica diferencial e folgas mecânicas com oscilações da ordem de $\pm 5\text{ mm}$ a $\pm 15\text{ mm}$.
* O posicionamento do cabeçote leitor exige suportes com desacoplamento elástico para evitar colisões mecânicas caso a pista de rolamento oscile fora dos limites do entreferro (*air gap*).


* **Interação com a Estrutura Metálica:**
* A fixação das tags diretamente sobre chapas de aço induz correntes parasitas (*eddy currents*) que distorcem o campo eletromagnético. Exige-se exclusivamente tags *on-metal* com blindagem traseira de ferrite ou camada dielétrica espessa.



### 2.2 Instalação Elétrica e Cabeamento

* **Alimentação:** Tensão nominal de $24\text{ Vcc}$ ($\pm 15\%$) filtrada e estabilizada, com consumo de pico de até $1\text{ A}$ durante ciclos de transmissão em potência máxima (UHF).
* **Conectividade:** Conectores industriais circulares padrão M12 (codificação A para alimentação/sinais discretos e codificação D para barramento Ethernet Industrial).
* **Compatibilidade Eletromagnética (EMC):** Roteamento em eletrocalhas metálicas aterradas, com separação física mínima de $300\text{ mm}$ em relação aos cabos de potência dos inversores de frequência e servomotores de tração para mitigar transitórios rápidos (EFT) e ruídos de modo comum.

### 2.3 Comunicação e Redes Industriais

* **Topologia de Rede:** Integração nativa aos protocolos **PROFINET-IO (RT/IRT)** ou **EtherNet/IP**, operando com tempos de ciclo configurados em $8\text{ ms}$ a $32\text{ ms}$.
* **Handshake com o CLP:**
1. A tag adentra a zona eletromagnética de interrogatório.
2. O leitor decodifica o UID e valida o checksum de RF (CRC16).
3. O leitor seta o bit de status `Tag_Present` e disponibiliza o UID no buffer de entrada da rede.
4. O CLP executa a leitura da tabela de dados via bloco de função normalizado e responde com o bit de controle `Acknowledge`.

## 3. Contrapontos Críticos e Análise de Inviabilidade

A implementação do RFID como transdutor exclusivo para medição contínua e discreta a 0,5° é tecnicamente inviável e economicamente proibitiva pelas seguintes razões:

### 3.1 Inviabilidade Financeira (CAPEX & OPEX)

* **Resolução vs. Custo Cumulativo de Tags:**
* Resolução exigida: $0{,}5^\circ \rightarrow \frac{360^\circ}{0{,}5^\circ} = 720\text{ divisões}$ ao longo da circunferência de $47\text{ m}$.


* Distância linear entre centros de tags consecutivas:

$$\Delta s = \frac{47\text{ m}}{720} \approx 0{,}0653\text{ m} = 65{,}3\text{ mm}$$


* Tags industriais *on-metal* de alta confiabilidade (ex.: Siemens MDS D424 ou SICK UHF On-Metal) têm custo médio unitário nacionalizado entre **R$ 110,00 e R$ 260,00**.


* Custo de aquisição apenas das tags:

$$\text{CAPEX}_{\text{Tags}} = 720 \times \text{R\$} 180{,}00 \approx \mathbf{R\$\,129.600{,}00}$$




* **Custos Periféricos e de Fixação:**
* Custo do leitor industrial integrado + cabos M12 + mestre de rede: **R$ 16.000,00 a R$ 22.000,00**.
* Fabricação e usinagem de 720 pontos de fixação roscados (M4/M8) ao longo do anel de $47\text{ m}$, incluindo torqueamento e trava-rosca químico, elevando drasticamente as horas de calibração mecânica (OPEX).


* **Retorno sobre o Investimento (ROI):** Custo de hardware superior a **R$ 145.000,00** para entregar uma resolução grosseira ($0{,}5^\circ$), quando arquiteturas convencionais com encoder entregam resoluções inferiores a $0{,}01^\circ$ por uma fração mínima deste valor.

### 3.2 Inviabilidade Mecânica e Física

* **Colisão e Sobreposição Espacial Inevitável:**
* Para tags com diâmetro típico de $27\text{ mm}$, o espaçamento livre entre as bordas de duas tags adjacentes é de apenas:

$$d_{\text{livre}} = 65{,}3\text{ mm} - 27\text{ mm} = 38{,}3\text{ mm}$$


* Uma antena HF média possui uma zona de acoplamento de no mínimo $80\text{ a }120\text{ mm}$. A antena abrangerá invariavelmente **duas ou três tags simultaneamente**, tornando impossível identificar o ponto central de medição.


* **O Paradoxo da Redução de Bobina:**
* Para restringir o campo indutivo a uma largura inferior a $30\text{ mm}$ (evitando a sobreposição), o cabeçote precisa ter dimensões reduzidas (M12 ou M18).
* No entanto, o alcance frontal de uma bobina M12/M18 cai para **$2\text{ mm a }6\text{ mm}$**. Como a estrutura mecânica oscila e empena em até $\pm 10\text{ mm}$, a folga útil é excedida: o sistema colide mecanicamente ou perde comunicação continuamente.


* **Inviabilidade de Blindagem Lateral:**
* Inserir anteparos metálicos nas laterais da antena HF gera correntes parasitas que desviam a frequência de ressonância de 13,56 MHz (*detuning*), anulando a energização.
* O uso de ferrites laterais redireciona as linhas de fluxo, derrubando a penetração frontal da mesma forma que uma redução física da bobina.


* **Limitação Operacional do Algoritmo Anti-Colisão:**
* Os algoritmos baseados em *Slotted Aloha* (ISO 15693 / EPC Gen 2) identificam a coexistência de múltiplas tags no campo, mas **não fornecem resolução de coordenada espacial**. O CLP recebe ambos os identificadores sem discriminação de qual deles ocupa o centro geométrico da trajetória.
* O ciclo de anti-colisão introduz latência de processamento de $30\text{ ms a }100\text{ ms}$, gerando histerese posicional e incerteza dinâmica durante o giro da estrutura.



### 3.3 Inviabilidade Operacional e Regulatória

* **Restrições de Homologação em UHF:** Equipamentos UHF operando em bandas ETSI (865–868 MHz) não são homologados pela Anatel no Brasil, exigindo estritamente a versão para a faixa de 902–928 MHz (padrão FCC/Anatel). Modelos importados incorretamente violam a regulação nacional de espectro.
* **Manutenção Preditiva Elevada:** A presença de 720 nós passivos expostos a ciclos térmicos, poeira e vibração mecânica eleva a probabilidade de falhas locais (tags trincadas, soltas ou descalibradas), exigindo rotinas complexas de rastreamento de falhas.

---

## 4. Matriz Comparativa e Conclusão Técnica

### 4.1 Tabela Comparativa de Tecnologias de Posicionamento

| Parâmetro de Avaliação | **RFID Puro (HF/UHF)** | **Encoder Incremental c/ Pinhão/Cremalheira** | **Encoder Absoluto Óptico Flutuante** | **Sistema Híbrido (Encoder + RFID)** |
| --- | --- | --- | --- | --- |
| **Resolução Atingível** | Limitada a $\approx 0{,}5^\circ$ ($65\text{ mm}$) | **$< 0{,}005^\circ$** (Pulsos contínuos) | **$< 0{,}001^\circ$** (24 a 32 bits) | **$< 0{,}005^\circ$** (Interpolação fina) |
| **Custo Estimado (Hardware)** | R$ 145.000 a 190.000 | **R$ 6.000 a 12.000** | R$ 15.000 a 30.000 | R$ 22.000 a 35.000 |
| **Complexidade Mecânica** | 720 furos/tags no anel | Baixa (acoplamento motor/anel) | Moderada (guia flutuante/mola) | Baixa a moderada |
| **Incerteza Espacial de Borda** | Alta ($> 30\text{ mm}$ de janela) | **Zero (pulsos mecânicos diretos)** | **Zero (leitura óptica direta)** | **Zero (sincronizada)** |
| **Robustez a Empenamentos** | Pífia (se ajustada p/ $65\text{ mm}$) | **Excelente (compensada)** | **Excelente (mancal flexível)** | **Excelente** |
| **Recuperação pós-desligamento** | Imediata (720 marcos) | Exige homing | **Totalmente Imediata** | Imediata (giro máx. $10^\circ$) |

---

### 4.2 Parecer Técnico e Recomendação Final

> **PARECER: DESCARTE DA SOLUÇÃO RFID PURO (720 TAGS)**
> A utilização de RFID como transdutor único para medição de posição angular com resolução de 0,5° está **formalmente descartada**. A solução viola princípios básicos do eletromagnetismo (impossibilidade de confinar o feixe indutivo ou radiado em $65\text{ mm}$ sem reduzir o entreferro a patamares mecanicamente perigosos de $< 5\text{ mm}$), além de exigir um CAPEX desproporcional que inviabiliza o projeto.

#### Solução Técnica Recomendada:

Para alcançar posicionamento angular contínuo de alta precisão imune a folgas e escorregamentos mecânicos, a engenharia recomenda:

1. **Opção Principal (Mais Robusta):** Instalação de um **Encoder Absoluto Multivoltas (com protocolo PROFINET ou SSI)** montado sobre um conjunto flutuante com guia linear pré-carregada por mola, engrenado diretamente em uma cremalheira de precisão instalada na pista da cúpula. Essa configuração isola o sistema de medição das excentricidades radiais e axiais da estrutura, fornecendo leitura contínua com resolução milesimal de grau e custo significativamente menor.
2. **Opção Secundária (Arquitetura Híbrida):** Caso a tração seja feita por rodas de atrito, utilizar um **Encoder Incremental** acoplado ao eixo de giro para interpolação fina e aplicar o RFID em sua função original: **como marco de calibração grosseira**. Para este fim, bastam **24 a 36 tags** (espaçadas a cada $15^\circ$ ou $10^\circ$, distância linear de $1{,}3\text{ a }1{,}9\text{ m}$), reduzindo o custo de tags para menos de R$ 6.000,00 e eliminando qualquer risco de sobreposição de campo eletromagnético.
