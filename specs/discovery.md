# Discovery: Aplicação de Previsão do Tempo

## Contexto

A empresa precisa de uma aplicação web que permita consultar condições
meteorológicas de forma rápida e simples. O usuário deve informar uma cidade,
visualizar o clima atual e consultar a previsão dos próximos cinco dias. A
experiência precisa funcionar bem em dispositivos móveis, sem perder
usabilidade em telas maiores.

O produto atende principalmente dois objetivos: apoiar decisões imediatas,
como escolher roupa ou decidir se é necessário levar um guarda-chuva, e ajudar
no planejamento dos dias seguintes. A jornada principal esperada é: buscar uma
cidade, escolher o resultado correto e visualizar os dados meteorológicos.

## Requisitos Funcionais

- **RF1 — Buscar cidade:** permitir que o usuário pesquise uma cidade por nome.
- **RF2 — Desambiguar resultados:** quando houver cidades com o mesmo nome,
  apresentar informações adicionais, como estado, região e país, para apoiar a
  escolha correta.
- **RF3 — Exibir clima atual:** mostrar temperatura, condição meteorológica,
  sensação térmica, umidade, vento, precipitação, local consultado, horário da
  atualização e fonte dos dados.
- **RF4 — Exibir previsão de cinco dias:** apresentar a previsão diária para
  hoje e os quatro dias seguintes, incluindo temperatura máxima e mínima,
  condição meteorológica e precipitação.
- **RF5 — Alternar unidade de temperatura:** permitir alternar entre Celsius e
  Fahrenheit e atualizar os valores exibidos conforme a unidade selecionada.
- **RF6 — Preservar a unidade escolhida:** manter a preferência entre visitas
  no mesmo navegador, inclusive ao realizar uma nova busca.
- **RF7 — Tratar estados da jornada:** informar claramente quando a aplicação
  está carregando dados, quando não encontrou a cidade e quando ocorreu uma
  falha na busca ou na consulta da previsão.
- **RF8 — Tentar novamente:** oferecer uma ação para repetir manualmente a
  consulta após uma falha recuperável de rede ou do serviço meteorológico,
  preservando a cidade selecionada e a unidade escolhida.

## Requisitos Não-Funcionais

- **RNF1 — Responsividade:** a interface deve ser mobile-first e permanecer
  utilizável em celulares, tablets e desktops.
- **RNF2 — Acessibilidade:** os fluxos principais devem ser operáveis por
  teclado, usar HTML semântico, possuir rótulos compreensíveis e atender ao
  contraste mínimo esperado pelas diretrizes WCAG AA.
- **RNF3 — Performance:** a aplicação deve exibir feedback de carregamento em
  até 100 ms após o início de uma consulta e deve apresentar a primeira tela
  utilizável em até 2 s em uma conexão móvel típica. As metas devem ser
  confirmadas com o negócio e medidas em condições definidas.
- **RNF4 — Compatibilidade:** suportar navegadores modernos e orientações de
  tela comuns em dispositivos móveis.
- **RNF5 — Resiliência:** lidar de forma previsível com indisponibilidade da
  rede, respostas inválidas, limite de requisições e indisponibilidade da fonte
  meteorológica, sem deixar a tela em estado indefinido.
- **RNF6 — Clareza da informação:** datas, temperaturas e condições devem ser
  apresentados de forma legível e consistente com a unidade selecionada.
- **RNF7 — Privacidade e segurança:** a solução não deve exigir autenticação
  para a jornada principal nem coletar dados pessoais sem necessidade. Chaves
  ou credenciais de serviços externos, caso existam, não devem ser expostas no
  cliente.
- **RNF8 — Internacionalização:** idioma da interface, formato de data e
  convenções de unidade devem ser definidos antes da implementação; pt-BR é a
  suposição inicial para a interface.
- **RNF9 — Disponibilidade:** o serviço deve atingir a meta de disponibilidade
  acordada para o período de operação, inicialmente sugerida como 99,5% ao
  mês, excluindo manutenções programadas.
- **RNF10 — Tempo de resposta:** em condições normais, a consulta de uma
  previsão deve retornar ou indicar erro em até 3 s; timeouts e falhas devem
  ser comunicados sem deixar a interface bloqueada.
- **RNF11 — Testabilidade entre dispositivos:** a aplicação deve ser validada
  em pelo menos uma viewport móvel, uma tablet e uma desktop, incluindo
  orientação retrato e paisagem quando aplicável.

## Revisão da Classificação

| Item | Classificação | Avaliação |
| --- | --- | --- |
| RF1 — Buscar cidade | Funcional | Correto: define uma ação que o usuário executa e o sistema precisa realizar. |
| RF2 — Desambiguar resultados | Funcional | Correto: define o comportamento da busca diante de cidades homônimas. |
| RF3 — Exibir clima atual | Funcional | Correto: define informações que o sistema deve apresentar. |
| RF4 — Exibir previsão de cinco dias | Funcional | Correto: define o conteúdo e o horizonte da consulta. |
| RF5 — Alternar unidade de temperatura | Funcional | Correto: define uma interação e seu resultado observável. |
| RF6 — Preservar a unidade escolhida | Funcional | Correto: define o comportamento da aplicação durante a sessão. |
| RF7 — Tratar estados da jornada | Funcional | Correto: mensagens de carregamento, vazio e erro são comportamentos do sistema. |
| RF8 — Tentar novamente | Funcional | Correto: define uma ação disponível após uma falha recuperável. |
| RNF1 — Responsividade | Não-funcional | Correto: é uma qualidade de uso da interface, aplicável a várias funcionalidades. |
| RNF2 — Acessibilidade | Não-funcional | Correto: estabelece qualidade, conformidade e restrições de interação. |
| RNF3 — Performance | Não-funcional | Correto após incluir metas mensuráveis de tempo. |
| RNF4 — Compatibilidade | Não-funcional | Correto: limita ambientes suportados pela aplicação. |
| RNF5 — Resiliência | Não-funcional | Correto como qualidade operacional; a ação concreta de nova tentativa permanece em RF8. |
| RNF6 — Clareza da informação | Não-funcional | Correto: define legibilidade e consistência da apresentação. |
| RNF7 — Privacidade e segurança | Não-funcional | Correto: estabelece restrições de proteção e exposição de dados/credenciais. |
| RNF8 — Internacionalização | Não-funcional | Correto: define restrições de idioma e representação de dados. |
| RNF9 — Disponibilidade | Não-funcional | Adicionado: mede a continuidade do serviço, não uma capacidade específica. |
| RNF10 — Tempo de resposta | Não-funcional | Adicionado: transforma a expectativa de rapidez em critério verificável. |
| RNF11 — Testabilidade entre dispositivos | Não-funcional | Adicionado: explicita a evidência necessária para validar responsividade. |

Não há requisitos funcionalmente classificados como não-funcionais na lista
atual. A principal correção foi tornar performance verificável e separar
comportamentos funcionais, como tentar novamente, de qualidades operacionais,
como resiliência e disponibilidade.

## Riscos

| Categoria | Risco | Probabilidade | Impacto | Estratégia de mitigação |
| --- | --- | --- | --- | --- |
| Técnico | A API meteorológica ou de geocodificação pode ficar indisponível, lenta ou sujeita a limite de requisições. | Média | Alto: o usuário não consegue consultar o clima, comprometendo a proposta central do produto. | Definir timeout, tratamento de erros, nova tentativa com limite, cache temporário quando permitido e monitoramento das falhas. |
| Técnico | A fonte pode retornar dados incompletos, inconsistentes ou em formato incompatível com o esperado. | Média | Alto: a previsão pode ser apresentada parcialmente ou com informação incorreta. | Validar o contrato da API, criar uma camada de normalização, tratar campos ausentes e cobrir respostas reais com testes. |
| Técnico | O geocoding pode selecionar uma cidade homônima ou uma localização diferente da intenção do usuário. | Alta | Alto: o sistema exibirá uma previsão aparentemente válida, porém para o lugar errado, prejudicando a confiança. | Exibir país, estado/região e coordenadas relevantes; exigir seleção em resultados ambíguos e testar nomes internacionais. |
| Técnico | Conversões de Celsius/Fahrenheit ou formatação de unidades podem ser aplicadas de forma incorreta. | Baixa | Alto: valores errados podem levar o usuário a decisões inadequadas. | Centralizar conversões em funções puras, indicar a unidade ao lado do valor e usar testes de limites e valores negativos. |
| Técnico | Falhas de rede, timeout ou respostas inválidas podem deixar a interface bloqueada ou sem feedback. | Média | Médio: o usuário abandona a jornada sem saber se deve aguardar ou tentar novamente. | Modelar estados de loading, vazio, erro e sucesso; oferecer nova tentativa e impedir estados indefinidos. |
| Técnico | O layout pode perder legibilidade ou operabilidade em celulares e orientações diferentes. | Média | Alto: a principal plataforma de uso pode ficar difícil ou impossível de utilizar. | Desenvolver mobile-first, definir breakpoints, testar viewports representativas e validar teclado e toque. |
| Técnico | Desempenho insuficiente pode tornar a busca e o carregamento da previsão frustrantes. | Média | Médio: aumenta abandono e reduz a percepção de confiabilidade do app. | Estabelecer metas de tempo, medir Core Web Vitals, reduzir payloads e chamadas, usar feedback imediato e otimizar o carregamento. |
| Produto | O escopo de “clima atual” e “previsão de cinco dias” pode ser interpretado de formas diferentes. | Alta | Alto: o time pode entregar uma solução tecnicamente correta, mas diferente da expectativa do negócio. | Fechar campos, granularidade, definição de “hoje” e critérios de aceite antes do desenvolvimento. |
| Produto | A unidade padrão, idiomas, formatos de data e fuso horário podem não atender ao público prioritário. | Média | Médio: os dados podem ser compreendidos com dificuldade ou parecer incorretos para o usuário. | Confirmar regiões e personas, definir pt-BR/Celsius como decisão inicial documentada e validar com usuários representativos. |
| Produto | A experiência pode não oferecer recursos esperados, como atualização manual, favoritos, histórico ou localização automática. | Média | Médio: reduz utilidade percebida e pode gerar retrabalho após o lançamento. | Priorizar um MVP explícito, validar necessidades com usuários e registrar recursos fora do escopo como backlog. |
| Produto | O usuário pode interpretar a previsão como garantia ou alerta oficial de segurança. | Média | Alto: decisões inadequadas podem gerar dano reputacional e responsabilidade indevida para a empresa. | Exibir fonte e horário da atualização, comunicar limitações da previsão e decidir formalmente se alertas meteorológicos fazem parte do produto. |
| Produto | A ausência de uma meta de disponibilidade e de um responsável operacional pode prolongar incidentes. | Média | Alto: falhas recorrentes podem permanecer sem diagnóstico e degradar a confiança no serviço. | Definir SLA/SLO, métricas, alertas, painel de monitoramento, responsável por incidentes e procedimento de comunicação. |
| Produto | A solução pode coletar ou armazenar localização, histórico ou preferências sem clareza para o usuário. | Baixa | Alto: cria risco de não conformidade com a LGPD e resistência ao uso do produto. | Minimizar dados, pedir consentimento quando necessário, documentar retenção, proteger armazenamento e publicar política de privacidade. |

## Perguntas em Aberto e Detalhamento

As questões abaixo registram ambiguidades do texto original e lacunas de escopo.
Cada impacto descreve a consequência de iniciar o desenvolvimento sem uma
decisão explícita. As questões já respondidas nesta revisão permanecem na tabela
para rastreabilidade e devem ser consideradas resolvidas; apenas os detalhes
indicados nas decisões continuam pendentes.

| # | Ponto ambíguo ou ausente | Pergunta em aberto | Impacto de seguir sem resposta |
| --- | --- | --- | --- |
| 1 | Fonte de dados | Qual fonte de dados meteorológicos e de geocodificação será utilizada? Ela exige chave, tem custo, limites de uso ou restrições para acesso pelo navegador? | Pode alterar arquitetura, orçamento, segurança, contrato de integração e viabilidade do produto. |
| 2 | Definição de “clima atual” | Quais dados devem ser exibidos: temperatura, condição, sensação térmica, umidade, vento, pressão, precipitação, visibilidade ou horário da última atualização? | A equipe pode entregar uma tela incompleta ou estimar esforço e layout com escopo diferente do esperado. |
| 3 | Horizonte de cinco dias | Os cinco dias incluem hoje ou são os cinco dias completos seguintes? A previsão será diária ou também terá intervalos por hora? | Afeta o contrato da API, a quantidade de dados, o layout, os rótulos de data e os critérios de aceite. |
| 4 | Unidade padrão | A unidade inicial deve ser Celsius ou Fahrenheit? A conversão inclui apenas temperatura ou também vento e precipitação? | Pode gerar retrabalho de apresentação e interpretações incorretas dos valores pelo usuário. |
| 5 | Persistência da preferência | A unidade escolhida deve durar apenas a sessão ou permanecer entre visitas e dispositivos? | Define se será necessário armazenamento local, conta de usuário ou outro mecanismo de persistência, com implicações de privacidade. |
| 6 | Escopo da busca | A busca aceita somente nome de cidade ou também acentos, abreviações, coordenadas e sugestões enquanto o usuário digita? | Sem essa definição, resultados, chamadas à API, debounce, validações e comportamento de busca vazia ficam inconsistentes. |
| 7 | Cidades homônimas | Como o usuário escolherá entre cidades com o mesmo nome? Quais identificadores serão mostrados, como país, estado, região e coordenadas? | O sistema pode exibir a previsão do local errado, comprometendo a confiança no produto. |
| 8 | Localização automática | A aplicação deve pedir permissão para usar a localização do dispositivo e mostrar o clima local automaticamente? | Impacta permissões, privacidade, fluxo inicial, fallback quando a permissão for negada e complexidade da experiência. |
| 9 | Estados da aplicação | O que deve acontecer em carregamento, busca sem resultados, erro de rede, timeout, limite de requisições e resposta inválida? | Usuários podem ficar sem feedback e a implementação pode tratar falhas diferentes de forma incoerente. |
| 10 | Atualização dos dados | Com que frequência a previsão deve ser atualizada? O usuário poderá atualizar manualmente? Deve aparecer a data/hora da última atualização? | Afeta frescor dos dados, volume de chamadas, cache, consumo de recursos e percepção de confiabilidade. |
| 11 | Offline e cache | É necessário funcionar offline ou exibir a última previsão conhecida quando a rede falhar? Por quanto tempo os dados podem ser reutilizados? | Determina a necessidade de cache, armazenamento local, indicação de dados desatualizados e regras de consistência. |
| 12 | Idiomas e localização | Quais idiomas, formatos de data, fuso horário e convenções regionais devem ser suportados na primeira versão? | Pode exigir internacionalização desde o início e alterar textos, formatos, testes e estrutura visual. |
| 13 | Escopo adicional | O produto terá favoritos, histórico, compartilhamento, alertas, mapas, notificações ou autenticação? | Funcionalidades descobertas tardiamente podem mudar modelo de dados, navegação, segurança e prazo. |
| 14 | Público e volume | Quem são os usuários prioritários, em quais regiões a solução será usada e qual volume de consultas é esperado? | Sem isso, decisões de idioma, cobertura geográfica, capacidade, limites da API e prioridades de UX ficam sem base. |
| 15 | Dispositivos e navegadores | Quais navegadores, versões mínimas, tamanhos de tela e orientações precisam ser oficialmente suportados? | Impede definir matriz de testes e pode gerar incompatibilidades descobertas apenas após a entrega. |
| 16 | Metas não-funcionais | Quais são as metas de performance, disponibilidade, acessibilidade e tempo de resposta, e como serão medidas? | Termos como “rápido”, “móvel” e “disponível” ficam subjetivos, dificultando aceite, monitoramento e priorização técnica. |
| 17 | Privacidade e conformidade | A solução armazenará localização, histórico ou preferências? Há requisitos de LGPD, retenção, consentimento ou política de privacidade? | Pode haver coleta indevida de dados e necessidade de mudanças de arquitetura, UX e documentação após a implementação. |
| 18 | Precisão e responsabilidade | Existe expectativa de precisão, fonte oficial ou aviso sobre limitações da previsão? Haverá alertas meteorológicos críticos? | O negócio pode assumir um nível de confiabilidade que a fonte não garante, criando risco reputacional e operacional. |
| 19 | Observabilidade e suporte | Quais eventos, erros e métricas devem ser monitorados? Quem será responsável por acompanhar indisponibilidade e incidentes? | Falhas de API, degradação de performance e problemas de uso podem permanecer invisíveis e sem processo de resposta. |

## Suposições

- A primeira versão será uma aplicação web responsiva, acessível sem login.
- O usuário terá conexão com a internet para pesquisar e atualizar os dados.
- A aplicação consultará uma fonte externa de geocodificação e previsão do
  tempo.
- A busca por nome de cidade é o fluxo principal; geolocalização automática
  está fora do MVP.
- "Previsão de cinco dias" significa o dia atual mais os quatro dias seguintes,
  apresentados na granularidade diária.
- Celsius será a unidade padrão inicial, e a preferência será persistida entre
  visitas no mesmo navegador.
- A interface inicial será disponibilizada em pt-BR.
- A aplicação exibirá dados meteorológicos fornecidos pela fonte consultada,
  sem prometer precisão superior à do serviço de origem.
- O escopo inicial não inclui alertas meteorológicos, histórico, favoritos,
  mapas, compartilhamento, notificações ou autenticação.

## Personas

### 1. Decisor do dia a dia

- **Objetivo principal:** consultar rapidamente o clima atual antes de sair de
  casa e decidir roupa, transporte ou necessidade de levar guarda-chuva.
- **Contexto de uso:** principalmente mobile, em deslocamentos ou poucos
  minutos antes de uma atividade; pode usar conexão móvel instável.
- **Métrica de sucesso:** consegue encontrar sua cidade e interpretar o clima
  atual em até 30 segundos, sem precisar repetir a busca ou mudar de tela para
  entender a unidade e a condição meteorológica.

### 2. Viajante planejador

- **Objetivo principal:** comparar a previsão dos próximos cinco dias para
  organizar viagem, compromissos e atividades ao ar livre.
- **Contexto de uso:** desktop ou tablet, em uma sessão mais tranquila de
  planejamento; pode pesquisar mais de uma cidade.
- **Métrica de sucesso:** consulta a cidade correta e visualiza os cinco dias
  completos com datas e temperaturas compreensíveis, sem ambiguidade entre
  localidades ou unidades.

### 3. Usuário recorrente internacional

- **Objetivo principal:** consultar regularmente cidades de regiões diferentes
  e usar a unidade de temperatura com a qual está familiarizado.
- **Contexto de uso:** alterna entre mobile e desktop, realiza buscas
  frequentes e pode estar em um país diferente do idioma padrão da aplicação.
- **Métrica de sucesso:** realiza uma nova consulta e alterna entre Celsius e
  Fahrenheit sem perder o contexto, identificando claramente local, data, hora
  da atualização e unidade exibida.

## Decisões

As decisões abaixo encerram as principais questões necessárias para avançar da
discovery para a especificação funcional. Questões de detalhe que não são
resolvidas por estas escolhas permanecem em **Perguntas em Aberto**.

### Decisões confirmadas nesta revisão

- A busca será iniciada por envio explícito do formulário, com no mínimo dois
  caracteres, sem sugestões automáticas nesta versão.
- A desambiguação exibirá, quando disponíveis, cidade, estado ou região, país e
  coordenadas; o usuário deverá selecionar um resultado antes da consulta.
- O clima atual exibirá temperatura, condição, sensação térmica, umidade,
  vento, precipitação, local, horário da atualização e fonte.
- Cada dia da previsão exibirá temperatura máxima e mínima, condição e
  precipitação.
- Datas e horários usarão o timezone retornado para a cidade consultada; “hoje”
  significa o dia local daquela cidade.
- A unidade padrão será Celsius, com alternância para Fahrenheit e conversão
  apenas na apresentação. A preferência será salva no `localStorage` do mesmo
  navegador e não será sincronizada entre dispositivos.
- Falhas terão timeout de 10 segundos, uma consulta por vez e retry manual,
  preservando cidade e unidade. Busca sem resultado, rede/timeout, limite de
  requisições e resposta inválida terão estados comunicados de forma distinta.
- Geolocalização automática, favoritos, histórico, alertas, notificações,
  mapas e compartilhamento ficam fora do MVP.
- As metas iniciais são feedback visual em até 100 ms, primeira tela utilizável
  em até 2 s em uma conexão móvel definida, resposta da consulta em até 3 s em
  condições normais, conformidade WCAG 2.1 AA e suporte às versões atuais de
  Chrome, Edge, Firefox e Safari.

### Arquitetura de acesso à fonte

- **Decisão:** a escolha entre acesso direto do navegador e um proxy/backend
  será feita no plano técnico.
- **Restrição:** qualquer opção deverá respeitar a publicação prevista no
  GitHub Pages, não expor credenciais e definir timeout, limites, cache,
  CORS, observabilidade e comportamento de indisponibilidade.

### Fonte de dados: Open-Meteo, sem API key

- **Decisão:** utilizar a API de geocodificação e previsão meteorológica da
  Open-Meteo, sem exigir uma chave de API.
- **Justificativa:** reduz a complexidade de configuração e implantação, evita
  o armazenamento de credenciais no cliente e é suficiente para o escopo
  inicial de busca de cidades e previsão.
- **Perguntas resolvidas:** fonte de dados, necessidade de chave e requisito de
  autenticação com o provedor.
- **Ainda requer detalhamento:** limites de uso, política de cache, campos
  disponíveis e comportamento em caso de indisponibilidade do serviço.

### Horizonte da previsão: hoje + quatro dias

- **Decisão:** interpretar “previsão de 5 dias” como o dia atual mais os quatro
  dias seguintes, com apresentação diária.
- **Justificativa:** estabelece um horizonte objetivo e compatível com a
  necessidade de planejamento sem incluir, neste momento, detalhamento por
  hora.
- **Perguntas resolvidas:** quantidade de dias, inclusão do dia atual e
  granularidade inicial da previsão.
- **Ainda requer detalhamento:** formato das datas e regras de exibição dos
  campos meteorológicos já definidos.

### Unidade padrão: Celsius

- **Decisão:** iniciar a aplicação exibindo temperaturas em Celsius e permitir a
  alternância para Fahrenheit.
- **Justificativa:** é a convenção mais adequada ao público inicial presumido em
  pt-BR, mantendo a alternativa necessária para usuários internacionais.
- **Perguntas resolvidas:** unidade padrão da primeira renderização.
- **Ainda requer detalhamento:** regras de arredondamento e formatação da
  temperatura em Fahrenheit. A preferência entre visitas será salva no
  `localStorage`; não haverá sincronização entre dispositivos.

### Acesso: sem autenticação e sem persistência de servidor

- **Decisão:** a jornada principal será aberta, sem cadastro ou login, e o
  produto não armazenará dados de usuário em um servidor.
- **Justificativa:** reduz fricção para uma consulta rápida e evita custos e
  responsabilidades de gestão de contas e dados pessoais no MVP.
- **Perguntas resolvidas:** necessidade de autenticação e persistência de dados
  no backend.
- **Ainda requer detalhamento:** uso de cache local para dados meteorológicos e
  as regras de privacidade aplicáveis. Histórico não será armazenado no MVP.

### Idioma da interface: pt-BR

- **Decisão:** disponibilizar a interface inicial em português do Brasil, com
  datas e textos adequados ao locale `pt-BR`.
- **Justificativa:** alinha o produto ao público inicial e evita introduzir
  internacionalização multilíngue antes de validar a necessidade.
- **Perguntas resolvidas:** idioma inicial e convenção regional da interface.
- **Ainda requer detalhamento:** estratégia para futuros idiomas, formatos de
  fuso horário e localização dos nomes das condições meteorológicas.