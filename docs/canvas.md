# Canvas de Problema — Equipe ___

*Copie aqui o conteúdo de `pbl/templates/canvas-de-problema.md` da disciplina e preencha. Rascunho individual até o Encontro 2; versão consolidada da equipe até o Encontro 3.*

---

## 1. O problema em uma frase

Hoje, operadores e equipes de manutenção industrial precisam identificar rapidamente anomalias, diagnosticar possíveis falhas e decidir a ação mais adequada, mas os dados estão distribuídos entre diversas variáveis, alarmes, históricos e informações operacionais, o que causa demora no diagnóstico, dificuldade na identificação da causa provável e risco de decisões inadequadas ou paradas não planejadas.

## 2. Quem sofre com isso

Os principais usuários afetados são:

Operadores de processo, que precisam interpretar rapidamente alarmes e condições anormais;
Técnicos de manutenção, responsáveis por localizar e diagnosticar falhas;
Engenheiros de manutenção, automação e confiabilidade, que analisam histórico e causas;
Supervisores, responsáveis pela decisão operacional;
A própria organização, devido às perdas de produção, disponibilidade e custos associados às falhas.

No protótipo acadêmico, esses usuários serão representados por um operador humano interagindo com uma planta simulada.

## 3. Como se resolve hoje

Normalmente, uma condição anormal gera um ou vários alarmes. O operador verifica as variáveis do processo e, dependendo da situação, aciona a manutenção.

A equipe então precisa correlacionar informações como:

alarmes + tendências + histórico + experiência técnica + documentação → diagnóstico → decisão.

Grande parte desse processo depende da experiência das pessoas envolvidas.

O sistema proposto pretende funcionar como um apoio inteligente, não como substituto do especialista.

## 4. PEAS

PEAS	Definição para nosso agente
P — Performance	Detectar corretamente condições anormais; identificar a falha correta entre até 3 hipóteses; classificar corretamente a criticidade; recomendar ação coerente; minimizar falsos alarmes; apresentar justificativa para a decisão.
E — Environment	Planta industrial simulada contendo motor elétrico, bomba centrífuga, sensores, alarmes, histórico operacional, base de conhecimento e cenários de falha.
A — Actuators	Consultar histórico/base de conhecimento; gerar hipóteses; classificar criticidade; gerar alerta; recomendar inspeção/manutenção; recomendar redução de carga; recomendar parada; solicitar mais informações; escalar o problema para um humano.
S — Sensors	Temperatura, corrente, vibração, pressão, vazão, rotação, estado do motor/bomba, alarmes, histórico das variáveis, eventos e eventualmente relato textual do operador.

## 5. Dados — a seção decisiva

Que dados são necessários?	Séries temporais de temperatura, corrente, vibração, pressão, vazão, rotação e estado do equipamento; alarmes; eventos; histórico de falhas; regras/descrições das falhas.
Você já tem acesso, ou supõe que terá?	Não dependemos de acesso a dados industriais reais. O conjunto principal será produzido por uma planta simulada desenvolvida pelo grupo. Posteriormente, datasets públicos poderão ser utilizados para validação complementar.
Formato e volume aproximado	CSV/JSON. Inicialmente aproximadamente 10 mil a 100 mil registros, dependendo da frequência e duração da simulação.
Há informação confidencial, pessoal ou proprietária?	Não no conjunto principal, pois serão utilizados dados sintéticos.
Se houver: anonimizar, gerar sintético ou rodar em Ollama local?	A estratégia principal será geração de dados sintéticos. Caso dados industriais reais sejam utilizados posteriormente, deverão ser anonimizados e utilizados somente quando houver autorização.

## 6. Por que isso precisa de um agente

☑ O número de caminhos possíveis é grande ou desconhecido de antemão: diferentes combinações de sintomas podem representar diferentes falhas.
☑ Exige decidir quais informações buscar, não apenas processar informação dada: diante de uma anomalia, o agente poderá decidir consultar histórico, tendência, outros sensores ou base de conhecimento.
☑ Entrada pode ser não estruturada: além das séries temporais, podemos permitir algo como: "Operador relata ruído anormal na bomba."
☑ Requer combinar fontes ou ferramentas de formas que variam por caso: sensores, alarmes, históricos, regras e relatos podem ser utilizados diferentemente conforme o problema.

## 7. Como saberemos que funcionou

Três casos de teste, com a resposta esperada. Serão a semente da suíte de avaliação do Encontro 13.

Caso 1 — Operação normal

Entrada:

Temperatura = 72 °C
Vibração = 2,3 mm/s
Corrente = 20 A
Pressão = 5,2 bar
Vazão = 95 m³/h

Resposta esperada:

Estado normal.
Criticidade: baixa/normal.
Ação: manter operação e monitoramento.

O agente não deve gerar falso alarme.

Caso 2 — Possível falha de rolamento

Entrada:

Temperatura aumentando progressivamente: 72 → 91 °C
Vibração: 2,5 → 8,5 mm/s
Corrente com pequeno aumento.

Resposta esperada:

Anomalia detectada.
Hipótese principal: problema/desgaste de rolamento.
Criticidade: alta.
Recomendar inspeção/manutenção e avaliação da continuidade operacional.

Caso 3 — Cavitação da bomba

Entrada:

Vibração elevada + oscilação de pressão + redução de vazão.

Relato do operador:

"Bomba apresenta ruído anormal."

Resposta esperada:

Hipótese principal: cavitação.
Verificar condições de sucção, nível/pressão de entrada e possíveis restrições.
Criticidade definida conforme intensidade/persistência.
Recomendar intervenção se a condição permanecer.

## 8. Escopo

| **Dentro** — o mínimo que precisa funcionar: Simulação de motor + bomba; geração de dados sintéticos; operação normal + pelo menos 3 falhas; múltiplos agentes especializados; detecção de anomalias; diagnóstico; classificação de criticidade; recomendação; interface simples; registro das decisões; três ou mais testes de avaliação.

| **Fora** — tentação explicitamente descartada: Integração com uma planta industrial real; comando físico de máquinas; PLC/SDCD real; parada automática de equipamento real; desenvolvimento de Digital Twin completo; diagnóstico de dezenas de equipamentos; treinamento de modelo generativo próprio; aplicação em ambiente industrial de produção.

## 9. Riscos

| Risco | Como perceberemos cedo | Plano B |

Dados simulados pouco realistas | Gráficos mostram variáveis aleatórias sem relação coerente | Criar regras simplificadas baseadas no comportamento esperado das falhas
Sistema multiagente ficar complexo demais | Muito tempo sendo gasto na comunicação entre agentes | Reduzir para 3 agentes: Monitor, Diagnóstico e Supervisor
IA não identificar corretamente as falhas | Baixa taxa de acerto nos casos de teste | Utilizar inicialmente regras especialistas e posteriormente incorporar ML/LLM
LLM inventar causas | Diagnósticos não sustentados pelos sensores | Restringir hipóteses a uma base de conhecimento e exigir evidências para cada diagnóstico
Projeto ficar grande demais | Muitas falhas/equipamentos sendo adicionados | Manter somente motor + bomba e 3–5 condições de falha
Dependência de API externa | Falhas de conexão/custo/limite de requisições | Utilizar modelo local via Ollama ou regras determinísticas
Agentes chegarem a conclusões conflitantes | Diagnóstico diferente entre agentes | Agente Supervisor aplica regras de prioridade e registra a justificativa
Falta de tempo para ML | Dataset/modelo não fica pronto | O MVP funciona com regras + agentes; ML passa a ser uma extensão
