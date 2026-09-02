# Especificação: Aplicação de Previsão do Tempo

## 1. Overview

A aplicação permitirá consultar o clima atual e a previsão diária de cinco dias para uma cidade. O usuário pesquisará o nome da cidade, escolherá o resultado correto quando houver ambiguidades e visualizará os dados meteorológicos em uma interface responsiva, acessível e em português do Brasil.

O MVP atende dois objetivos principais:

- apoiar decisões imediatas, como escolher roupa, transporte ou levar guarda-chuva;
- apoiar o planejamento dos quatro dias seguintes.

A fonte de geocodificação e previsão será a Open-Meteo. A jornada principal não exigirá autenticação e não armazenará dados de usuário em servidor.

## 2. Functional Requirements

### FR1 — Pesquisar cidade

O sistema deve permitir que o usuário pesquise uma cidade informando seu nome em um formulário.

- A pesquisa será iniciada por envio explícito do formulário.
- O nome deverá conter pelo menos dois caracteres após a validação da entrada.
- A versão inicial não exibirá sugestões automáticas durante a digitação.
- A pesquisa deverá aceitar nomes com acentos e preservar o texto informado até a conclusão da busca ou apresentação do erro.
- A pesquisa deverá aceitar letras, espaços, acentos, hífens e apóstrofos. Entradas vazias, formadas somente por espaços, números, símbolos, emojis ou caracteres de controle deverão ser rejeitadas sem requisição à fonte.
- A validação deverá remover espaços nas extremidades e considerar espaços consecutivos como parte da normalização, sem alterar o nome apresentado ao usuário.

### FR2 — Selecionar a cidade correta

O sistema deve apresentar os resultados de geocodificação para que o usuário escolha a localização desejada.

- Cada resultado deverá exibir, quando disponíveis, cidade, estado ou região, país e coordenadas.
- A previsão só poderá ser consultada após a seleção de um resultado.
- A seleção deverá identificar uma localização única por suas coordenadas e dados retornados pela fonte.
- Quando não houver resultados, o sistema deverá informar que nenhuma cidade foi encontrada.

### FR3 — Exibir clima atual

Após a seleção de uma cidade e o recebimento válido da previsão, o sistema deve exibir:

- temperatura atual;
- condição meteorológica;
- sensação térmica;
- umidade;
- vento;
- precipitação;
- cidade e região/país consultados;
- horário da atualização;
- fonte dos dados.

São obrigatórios para considerar o clima atual válido: temperatura, condição
meteorológica, sensação térmica, umidade, vento e precipitação. Cidade, região
ou país, horário de atualização e fonte são obrigatórios para a apresentação
completa da consulta; se a fonte não os fornecer, a consulta deverá ser tratada
como resposta inválida.

### FR4 — Exibir previsão de cinco dias

O sistema deve exibir a previsão diária do dia local atual e dos quatro dias seguintes.

Cada dia deve apresentar:

- data;
- temperatura mínima;
- temperatura máxima;
- condição meteorológica;
- precipitação.

Os cinco dias e, para cada dia, data, temperatura mínima, temperatura máxima,
condição meteorológica e precipitação são obrigatórios para considerar a
previsão válida.

Não faz parte deste requisito exibir previsão por hora.

### FR5 — Alternar unidade de temperatura

O sistema deve permitir alternar a apresentação das temperaturas entre Celsius e Fahrenheit.

- A unidade atualmente selecionada deverá ser identificada junto aos valores.
- A alteração deverá atualizar o clima atual e todos os cinco dias da previsão.
- A alteração de unidade não deverá exigir uma nova consulta meteorológica.
- A conversão deverá ocorrer somente na apresentação; os dados meteorológicos serão tratados internamente em Celsius.
- Temperaturas serão arredondadas para o inteiro mais próximo na apresentação. Não haverá conversão de vento ou precipitação nesta versão.

### FR6 — Preservar a unidade escolhida

O sistema deve preservar a unidade selecionada entre visitas no mesmo navegador e durante novas buscas.

- A unidade padrão será Celsius quando não houver preferência salva.
- A preferência não será sincronizada entre dispositivos.
- O sistema não deverá depender de conta ou autenticação para preservar essa preferência.

### FR7 — Informar estados da jornada

O sistema deve comunicar claramente os estados relevantes da jornada:

- estado inicial, antes de uma cidade ser pesquisada;
- carregamento da busca de cidade;
- carregamento da previsão;
- busca concluída sem resultados;
- erro de rede;
- timeout;
- limite de requisições;
- resposta inválida ou incompleta;
- consulta concluída com sucesso.

A interface não poderá permanecer bloqueada ou sem feedback após uma consulta concluída, falha ou cancelamento.

Uma resposta será considerada válida somente quando contiver todos os dados
necessários para o clima atual e para os cinco dias da previsão. A ausência de
um campo opcional de apresentação não deverá invalidar a consulta; nesse caso,
o sistema exibirá os demais dados disponíveis e omitirá o campo ausente de forma
explícita. Campos obrigatórios ausentes, JSON malformado ou menos de cinco dias
de previsão deverão gerar erro de dados e não poderão ser apresentados como
sucesso.

### FR8 — Tentar novamente

Após uma falha recuperável na consulta, o sistema deve oferecer uma ação de retry manual.

- O retry deverá reutilizar a cidade selecionada.
- O retry deverá preservar a unidade escolhida.
- O sistema deverá permitir uma consulta por vez.
- O tempo limite de uma consulta será de 10 segundos.
- A aplicação não fará retry automático nesta versão.

## 3. User Stories

### US1 — Consulta rápida

Como um decisor do dia a dia, quero pesquisar uma cidade e ver o clima atual para decidir como me preparar para sair.

### US2 — Escolha da localidade

Como um usuário que pesquisa uma cidade com homônimos, quero comparar os dados de localização para escolher o resultado correto e evitar consultar o clima do lugar errado.

### US3 — Planejamento dos próximos dias

Como um viajante planejador, quero visualizar a previsão diária de cinco dias para organizar meus compromissos e atividades.

### US4 — Preferência de unidade

Como um usuário recorrente internacional, quero alternar entre Celsius e Fahrenheit para interpretar as temperaturas na unidade que conheço.

### US5 — Persistência da preferência

Como um usuário recorrente, quero que minha unidade preferida seja mantida em novas visitas e buscas para não precisar configurá-la novamente.

### US6 — Recuperação de falha

Como um usuário com conexão instável, quero receber uma mensagem clara e tentar novamente uma consulta para concluir a busca sem ficar preso em uma tela indefinida.

## 4. Acceptance Criteria

### FR1 — Pesquisar cidade

- **AC-FR1-01 — Given** que o campo contém um nome com pelo menos dois caracteres válidos, **When** o usuário envia o formulário, **Then** o sistema faz uma requisição de geocoding e exibe o estado de carregamento.
- **AC-FR1-02 — Given** que o campo está vazio, contém somente espaços, números, símbolos, emojis ou caracteres de controle, **When** o usuário envia o formulário, **Then** o sistema não faz requisição e informa a correção necessária.
- **AC-FR1-03 — Given** que o nome contém acentos, hífens ou apóstrofos, **When** o usuário envia o formulário, **Then** o sistema preserva o texto informado na interface durante a busca.

### FR2 — Selecionar a cidade correta

- **AC-FR2-01 — Given** que o geocoding retorna resultados, **When** a lista é apresentada, **Then** cada resultado exibe cidade, estado ou região, país e coordenadas quando disponíveis.
- **AC-FR2-02 — Given** que há mais de um resultado, **When** o usuário seleciona um resultado, **Then** a consulta meteorológica usa as coordenadas exclusivamente daquele resultado.
- **AC-FR2-03 — Given** que o geocoding retorna uma lista vazia, **When** a resposta é processada, **Then** o sistema exibe “cidade não encontrada”, não consulta a previsão e permite nova busca.
- **AC-FR2-04 — Given** que o geocoding retorna erro HTTP ou resposta inválida, **When** a resposta é processada, **Then** o sistema exibe erro de serviço ou de dados, sem tratá-lo como cidade inexistente.

### FR3 — Exibir clima atual

- **AC-FR3-01 — Given** que a consulta retorna todos os campos obrigatórios, **When** a resposta é processada, **Then** o sistema exibe temperatura, condição, sensação térmica, umidade, vento, precipitação, local, horário de atualização e fonte.
- **AC-FR3-02 — Given** que a fonte retorna um código meteorológico conhecido, **When** o clima é renderizado, **Then** o sistema exibe a descrição correspondente em pt-BR.
- **AC-FR3-03 — Given** que a fonte retorna um código desconhecido, **When** o clima é renderizado, **Then** o sistema exibe uma descrição genérica de condição indisponível sem quebrar a tela.

### FR4 — Exibir previsão de cinco dias

- **AC-FR4-01 — Given** que a previsão retorna cinco ou mais dias válidos, **When** é processada, **Then** o sistema exibe exatamente cinco dias, começando pelo dia local atual.
- **AC-FR4-02 — Given** que um dia válido é exibido, **When** o usuário consulta seus dados, **Then** o dia contém data, mínima, máxima, condição e precipitação.
- **AC-FR4-03 — Given** que a resposta tem menos de cinco dias ou falta um campo obrigatório, **When** é processada, **Then** o sistema exibe erro de dados e não apresenta previsão parcial como sucesso.

### FR5 — Alternar unidade de temperatura

- **AC-FR5-01 — Given** que uma previsão está visível em Celsius, **When** o usuário seleciona Fahrenheit, **Then** todas as temperaturas atuais e diárias são convertidas e exibidas em Fahrenheit sem nova requisição.
- **AC-FR5-02 — Given** que uma temperatura é exibida, **When** a unidade selecionada é Celsius ou Fahrenheit, **Then** o valor exibe o símbolo da unidade correspondente.
- **AC-FR5-03 — Given** que a temperatura é negativa ou possui casas decimais, **When** é convertida, **Then** o resultado é arredondado para o inteiro mais próximo.

### FR6 — Preservar a unidade escolhida

- **AC-FR6-01 — Given** que não existe preferência válida armazenada, **When** a aplicação é aberta, **Then** a unidade inicial é Celsius.
- **AC-FR6-02 — Given** que o usuário selecionou Fahrenheit, **When** realiza nova busca ou abre nova visita no mesmo navegador, **Then** a aplicação usa Fahrenheit.
- **AC-FR6-03 — Given** que a preferência armazenada contém valor inválido ou o armazenamento está indisponível, **When** a aplicação é aberta, **Then** a aplicação usa Celsius e a consulta permanece disponível.

### FR7 — Informar estados da jornada

- **AC-FR7-01 — Given** que uma busca ou consulta foi iniciada, **When** a requisição está pendente, **Then** a interface exibe loading identificando a operação.
- **AC-FR7-02 — Given** que a operação termina com sucesso, vazio ou erro, **When** o estado é atualizado, **Then** o loading desaparece e a interface apresenta o estado correspondente.
- **AC-FR7-03 — Given** que ocorre rede, timeout, limite de requisições, erro HTTP ou resposta inválida, **When** a falha é processada, **Then** a mensagem identifica a categoria da falha e não a confunde com cidade inexistente.

### FR8 — Tentar novamente

- **AC-FR8-01 — Given** que uma consulta falhou por rede, timeout, erro de serviço ou limite recuperável, **When** o erro é exibido, **Then** a interface oferece retry manual.
- **AC-FR8-02 — Given** que há uma cidade selecionada e uma unidade atual, **When** o usuário aciona retry, **Then** o sistema repete a consulta para a mesma cidade e mantém a unidade.
- **AC-FR8-03 — Given** que uma consulta está pendente, **When** o usuário tenta iniciar outra, **Then** a nova ação é bloqueada até a conclusão ou timeout da consulta atual.
- **AC-FR8-04 — Given** que uma requisição permanece sem resposta por 10 segundos, **When** o timeout é atingido, **Then** o sistema encerra a requisição, libera a interface e oferece retry.

## Traceability Matrix

| User Story | Acceptance Criteria | Requisitos não-funcionais relevantes |
| --- | --- | --- |
| US1 — Consulta rápida | AC-FR1-01, AC-FR1-02, AC-FR1-03, AC-FR3-01, AC-FR3-02, AC-FR3-03 | NFR1, NFR2, NFR3, NFR5, NFR6, NFR9 |
| US2 — Escolha da localidade | AC-FR2-01, AC-FR2-02, AC-FR2-03, AC-FR2-04 | NFR1, NFR2, NFR5, NFR6, NFR9 |
| US3 — Planejamento dos próximos dias | AC-FR4-01, AC-FR4-02, AC-FR4-03 | NFR1, NFR2, NFR3, NFR5, NFR6, NFR9 |
| US4 — Preferência de unidade | AC-FR5-01, AC-FR5-02, AC-FR5-03 | NFR1, NFR2, NFR6, NFR9 |
| US5 — Persistência da preferência | AC-FR6-01, AC-FR6-02, AC-FR6-03 | NFR2, NFR5, NFR6, NFR7 |
| US6 — Recuperação de falha | AC-FR7-01, AC-FR7-02, AC-FR7-03, AC-FR8-01, AC-FR8-02, AC-FR8-03, AC-FR8-04 | NFR1, NFR2, NFR3, NFR5, NFR8, NFR9 |

## 5. Non-Functional Requirements

### NFR1 — Responsividade

A interface deve ser mobile-first e permanecer utilizável em celulares, tablets e desktops, nos modos retrato e paisagem quando aplicável. Busca, seleção de cidade, alternância de unidade, leitura da previsão e retry devem ser operáveis em todas as viewports suportadas.

### NFR2 — Acessibilidade

Os fluxos principais devem:

- ser operáveis integralmente por teclado;
- usar HTML semântico e rótulos compreensíveis;
- manter foco visível e ordem de foco coerente;
- comunicar mudanças de carregamento, vazio e erro a tecnologias assistivas;
- atender ao contraste mínimo esperado pela WCAG 2.1 nível AA.

### NFR3 — Performance

- O feedback visual de carregamento deve aparecer em até 100 ms após o início de uma consulta.
- A primeira tela utilizável deve ser apresentada em até 2 segundos em uma conexão móvel definida para a validação.
- Em condições normais, uma consulta que receber resposta do serviço deve retornar sucesso ou erro em até 3 segundos.
- Independentemente do tempo de resposta esperado, uma consulta que não terminar dentro de 10 segundos deve ser encerrada como timeout e comunicar a falha.

As medições devem registrar navegador, viewport, dispositivo e condições de rede utilizadas.

### NFR4 — Compatibilidade

A aplicação deve suportar as duas versões estáveis mais recentes de Chrome,
Edge, Firefox e Safari, além de orientações retrato e paisagem em dispositivos
móveis.

### NFR5 — Resiliência

A aplicação deve tratar previsivelmente indisponibilidade de rede ou fonte, timeout, limite de requisições, respostas inválidas e campos ausentes, sem exibir dados meteorológicos incompletos como se fossem válidos.

### NFR6 — Clareza da informação

Datas, temperaturas, condições, precipitação, local, horário de atualização e unidade devem ser apresentados de forma legível e consistente com o locale pt-BR e a unidade selecionada.

### NFR7 — Privacidade e segurança

- A jornada principal não exigirá autenticação.
- Não serão coletados dados pessoais sem necessidade para o MVP.
- Não haverá armazenamento de histórico de buscas ou localização no servidor.
- Credenciais de serviços externos, caso existam, não poderão ser expostas no cliente.
- O uso de armazenamento local e os dados eventualmente mantidos em cache deverão respeitar as regras de privacidade aplicáveis.

### NFR8 — Disponibilidade e observabilidade

A meta inicial de disponibilidade da aplicação é 99,5% ao mês, excluindo
manutenções programadas. A medição deverá considerar a disponibilidade da
interface publicada separadamente da disponibilidade da Open-Meteo; falhas de
consulta e degradação relevante deverão ser registradas para observação.

### NFR9 — Validação entre dispositivos

A solução deve ser validada em pelo menos uma viewport móvel, uma tablet e uma desktop, incluindo retrato e paisagem quando aplicável.

## 6. Edge Cases

- **Input vazio ou curto:** para entrada vazia, somente espaços ou com menos de dois caracteres, não consultar a fonte, destacar o campo e orientar o usuário a informar um nome válido.
- **Caracteres especiais:** aceitar letras, espaços, acentos, hífens e apóstrofos; rejeitar entradas somente com números, símbolos, emojis ou caracteres de controle sem fazer requisição e informar o formato esperado.
- **Cidade inexistente:** quando não houver uma cidade correspondente, exibir “cidade não encontrada”, limpar o resultado da nova consulta e permitir uma nova busca.
- **Geocoding sem resultados:** tratar a lista vazia do geocoding como estado vazio, não iniciar a consulta meteorológica e manter o formulário disponível para correção.
- Múltiplos resultados ou homônimos: exigir seleção explícita antes de consultar a previsão.
- Resultado sem estado, região ou país: exibir somente os dados disponíveis, sem impedir a seleção quando a localização for identificável.
- Usuário envia nova busca durante uma consulta: bloquear o envio até a conclusão ou timeout da consulta atual, mantendo a busca e o resultado atual inalterados.
- Resposta antiga após uma nova ação do usuário: não substituir o resultado mais recente por dados obsoletos.
- **Falha de API:** para erro HTTP, indisponibilidade do serviço ou falha de comunicação, informar que a consulta não pôde ser concluída, preservar a cidade selecionada e oferecer retry quando a falha for recuperável.
- **Timeout:** após 10 segundos sem resposta, encerrar a consulta, liberar a interface, informar o timeout e oferecer retry manual.
- **Limite de requisições:** informar que o serviço está temporariamente indisponível, não tratar o caso como cidade inexistente e oferecer retry manual quando aplicável.
- **Resposta parcial:** se faltar qualquer campo obrigatório do clima atual ou dos cinco dias, informar falha de dados e não renderizar sucesso parcial; se faltar apenas campo opcional, exibir os demais dados e indicar a ausência sem bloquear a consulta.
- Falha na busca de cidade: não iniciar a consulta de previsão sem uma localização válida.
- Falha na previsão após seleção: manter a cidade selecionada para permitir retry.
- Falta de preferência salva: usar Celsius.
- Armazenamento local indisponível ou bloqueado: usar Celsius ou a unidade em memória sem impedir a consulta.
- Atualização manual após uma consulta bem-sucedida não faz parte do MVP; uma nova consulta poderá ser feita enviando novamente o formulário.
- Virada do dia na cidade consultada: usar o timezone da cidade para definir o dia atual e os quatro seguintes.
- Dados meteorológicos negativos ou extremos: formatar valores conforme as regras de unidade e arredondamento definidas no plano.

## 7. Assumptions

- O MVP será uma aplicação web responsiva, acessível sem login.
- A fonte será a Open-Meteo para geocodificação e previsão, sem API key.
- O usuário terá conexão com a internet para pesquisar e atualizar dados.
- O horizonte será o dia local atual mais os quatro dias seguintes, sem previsão por hora.
- Celsius será a unidade padrão; Fahrenheit será uma alternativa de apresentação.
- A preferência de unidade será mantida no mesmo navegador, sem sincronização entre dispositivos.
- A preferência de unidade será armazenada localmente pelo navegador; somente os valores `C` e `F` serão aceitos, com fallback para Celsius em qualquer outro valor.
- A interface inicial será disponibilizada em pt-BR.
- Os dados exibidos representarão a fonte consultada, sem promessa de precisão superior à do serviço de origem.
- Não haverá autenticação nem persistência de usuário em servidor.
- Geolocalização automática, favoritos, histórico, alertas, notificações, mapas e compartilhamento não fazem parte do MVP.

## 8. Risks

| Risco | Impacto | Mitigação |
| --- | --- | --- |
| Indisponibilidade, lentidão ou limite da Open-Meteo | O usuário não consegue consultar o clima. | Timeout, mensagens específicas, retry manual, monitoramento e decisão de cache no plano técnico. |
| Alteração ou incompatibilidade do contrato da fonte | Dados incorretos ou indisponibilidade da interface. | Validar respostas, normalizar dados, testar campos obrigatórios e acompanhar a documentação da fonte. |
| Seleção da cidade errada | Previsão válida para a localização incorreta. | Exibir contexto geográfico, exigir seleção explícita e usar coordenadas do resultado escolhido. |
| Erro de conversão ou arredondamento | Usuário interpreta temperatura incorreta. | Centralizar regras de conversão, indicar unidades e testar valores negativos e limites. |
| Estados de erro incompletos | Tela bloqueada ou ausência de feedback. | Modelar estados de jornada, testar timeout, retry, respostas vazias e respostas inválidas. |
| Layout inadequado em dispositivos móveis | Perda de legibilidade e operabilidade. | Abordagem mobile-first, matriz de viewports e validação por teclado e toque. |
| Meta de disponibilidade incompatível com dependência externa | Expectativa operacional que a equipe não controla. | Definir no plano o escopo da métrica, a forma de medição e responsabilidades operacionais. |
| Uso indevido de armazenamento local ou cache | Risco de privacidade e dados desatualizados. | Minimizar dados, documentar retenção, sinalizar dados antigos e validar requisitos legais. |
| Ausência de definição de operação e observabilidade | Incidentes não detectados ou sem responsável. | Definir métricas, alertas, painel, responsável e procedimento de incidentes no plano. |

## 9. Out of Scope

Ficam fora do MVP:

- geolocalização automática;
- favoritos;
- histórico de cidades ou consultas;
- alertas meteorológicos;
- notificações;
- mapas;
- compartilhamento de consultas;
- autenticação e contas de usuário;
- previsão por hora;
- suporte a múltiplos idiomas na interface;
- promessa de precisão superior à fornecida pela fonte;
- armazenamento de dados de usuário em servidor.

## 10. Open Questions

As perguntas abaixo não foram decididas no discovery e devem ser resolvidas no plano técnico ou antes da implementação:

1. A aplicação acessará a Open-Meteo diretamente do navegador ou por meio de um proxy/backend? A decisão deve considerar GitHub Pages, CORS, limites de uso, segurança e observabilidade.
2. Quais endpoints e parâmetros exatos da Open-Meteo serão usados para geocodificação, clima atual e previsão diária?
3. Qual política de cache será adotada, por quanto tempo os dados poderão ser reutilizados e como dados desatualizados serão sinalizados?
4. Qual será o formato exato de datas e horários em pt-BR, incluindo a indicação do timezone da cidade?
5. A regra de arredondamento já definida é o inteiro mais próximo, sem conversão de vento ou precipitação. Qual política de cache será adotada para dados meteorológicos?
6. Quais campos opcionais de localização ou apresentação poderão ser omitidos sem invalidar a resposta?
7. Quais dimensões de viewport, dispositivos e condições de rede formarão a matriz oficial de validação? O suporte de navegador está definido como as duas versões estáveis mais recentes.
8. Quais eventos e métricas serão monitorados e quem será responsável por incidentes e indisponibilidade?
9. Quais avisos legais, atribuição da Open-Meteo e comunicação sobre limitações da previsão serão necessários no produto?
10. Quais requisitos específicos de LGPD, retenção e consentimento se aplicarão ao armazenamento local e ao cache?
