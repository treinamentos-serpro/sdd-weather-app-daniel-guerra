# Backlog de Tarefas: Aplicacao de Previsao do Tempo

As tarefas abaixo derivam de `plans/weather-app-plan.md` e estao ordenadas por dependencia. Cada tarefa deve ser implementada e validada isoladamente.

## Entrega 1: Fundacao e contratos

### T-01 — Definir contratos de dominio

- **Descricao:** Criar os tipos compartilhados para cidade, clima atual, previsao diaria, dados meteorologicos, unidade, estado da jornada e erros.
- **Criterios de aceite:**
  - Os tipos `Unit`, `City`, `CurrentWeather`, `ForecastDay`, `WeatherData`, `WeatherError` e `WeatherState` existem.
  - `City` contem `id`, `name`, `country`, `latitude` e `longitude`, com `admin1` opcional.
  - `CurrentWeather` usa `temperatureC`, `apparentTemperatureC`, `relativeHumidity`, `precipitationMm`, `windSpeedKmh`, `weatherCode` e `observedAt`.
  - `ForecastDay` contem data, minima, maxima, precipitacao e codigo meteorologico.
  - O projeto continua compilando em TypeScript strict.
- **Dependencias:** Nenhuma.
- **Arquivos provaveis:** `src/types/weather.ts`, `tsconfig.app.json`.
- **Tipo:** Data
- **Rastreabilidade:** FR2, FR3, FR4, FR5, FR6, FR7, FR8, NFR5, NFR6.

### T-02 — Configurar base visual e bootstrap

- **Descricao:** Preparar a composicao inicial da SPA, estilos globais e estrutura de layout responsivo conforme a stack existente.
- **Criterios de aceite:**
  - A aplicacao inicia sem erro em viewport mobile e desktop.
  - A tela renderiza areas identificaveis para busca, resultados, estados e dados meteorologicos.
  - A pagina nao apresenta overflow horizontal nem elementos cortados em viewport mobile retrato, mobile paisagem, tablet e desktop.
  - O bootstrap gera uma compilacao de producao sem erro e o preview local carrega a rota principal.
- **Dependencias:** T-01.
- **Arquivos provaveis:** `src/App.tsx`, `src/main.tsx`, `src/styles/globals.css`, `tailwind.config.js`.
- **Tipo:** UI
- **Rastreabilidade:** NFR1, NFR3, NFR4, NFR6, NFR9.

## Entrega 2: Regras puras

### T-03 — Implementar validacao e normalizacao da busca

- **Descricao:** Criar funcoes puras para normalizar espacos e validar nomes de cidade.
- **Criterios de aceite:**
  - Espacos nas extremidades sao removidos para a consulta.
  - Entradas vazias, somente com espacos ou com menos de dois caracteres sao rejeitadas.
  - Entradas compostas somente por numeros, simbolos, emojis ou caracteres de controle sao rejeitadas.
  - Letras, acentos, espacos, hifens e apostrofos sao aceitos.
  - A funcao nao altera o texto original destinado a exibicao.
- **Dependencias:** T-01.
- **Arquivos provaveis:** `src/lib/validation.ts`.
- **Tipo:** Data
- **Rastreabilidade:** FR1, NFR5.

### T-04 — Implementar conversao e formatacao meteorologica

- **Descricao:** Criar funcoes puras para conversao de temperatura, arredondamento, datas por timezone e descricoes de codigos WMO.
- **Criterios de aceite:**
  - Celsius e Fahrenheit sao convertidos corretamente, incluindo valores negativos.
  - Temperaturas sao arredondadas para o inteiro mais proximo.
  - Datas usam o timezone retornado pelo Forecast.
  - Codigos WMO conhecidos retornam descricoes em pt-BR.
  - Codigo WMO desconhecido retorna descricao generica sem lancar erro.
- **Dependencias:** T-01.
- **Arquivos provaveis:** `src/lib/temperature.ts`, `src/lib/formatters.ts`, `src/lib/weatherCodes.ts`.
- **Tipo:** Data
- **Rastreabilidade:** FR3, FR4, FR5, NFR6.

### T-05 — Testar regras puras

- **Descricao:** Cobrir validacao, conversao, arredondamento, timezone e codigos meteorologicos com testes unitarios.
- **Criterios de aceite:**
  - Ha pelo menos um caso de teste para cada classe de entrada valida e invalida da busca.
  - Ha casos de teste para acentos, hifens, apostrofos, temperaturas negativas e valores decimais.
  - Ha casos de teste que verificam conversao e arredondamento de Celsius/Fahrenheit contra valores esperados.
  - Ha um caso de teste de data em timezone diferente do timezone do ambiente de teste.
  - Ha casos de teste para codigo WMO conhecido e desconhecido, verificando descricao e fallback.
- **Dependencias:** T-03, T-04.
- **Arquivos provaveis:** `tests/lib/validation.test.ts`, `tests/lib/temperature.test.ts`, `tests/lib/formatters.test.ts`, `tests/lib/weatherCodes.test.ts`.
- **Tipo:** Test
- **Rastreabilidade:** FR1, FR3, FR4, FR5, AC-FR1-02, AC-FR5-03, NFR6.

## Entrega 3: Integracao com dados

### T-06 — Implementar geocoding Open-Meteo

- **Descricao:** Criar o service de busca de cidades, com endpoint, parametros, normalizacao de resultados e tratamento da lista vazia.
- **Criterios de aceite:**
  - O service usa o endpoint de geocoding definido no plano.
  - Envia `name`, `count=10`, `language=pt` e `format=json`.
  - Mapeia cada resultado para `City`, preservando `id`, `name`, `admin1`, `country`, `latitude` e `longitude`.
  - Lista vazia produz erro classificavel como `not-found`.
  - O service nao expoe o formato bruto da API para a UI.
- **Dependencias:** T-01, T-03.
- **Arquivos provaveis:** `src/services/weatherService.ts`, `src/types/weather.ts`.
- **Tipo:** Data
- **Rastreabilidade:** FR1, FR2, FR7, NFR5, NFR7.

### T-07 — Implementar forecast e mapeamento indexado

- **Descricao:** Implementar a consulta de Forecast e converter `current` e as listas paralelas de `daily` para os modelos internos.
- **Criterios de aceite:**
  - O service usa latitude, longitude, `current`, `daily`, `forecast_days=5` e `timezone=auto`.
  - `current` e mapeado para `CurrentWeather` com unidades internas explicitas.
  - Cada indice de `daily.time`, `weather_code`, min/max e precipitacao forma um unico `ForecastDay`.
  - A ordem e a associacao por indice sao preservadas.
  - O resultado contem timezone, `updatedAt` e exatamente cinco dias.
- **Dependencias:** T-01, T-04, T-06.
- **Arquivos provaveis:** `src/services/weatherService.ts`, `src/types/weather.ts`.
- **Tipo:** Data
- **Rastreabilidade:** FR3, FR4, FR7, NFR5, NFR6.

### T-08 — Validar payloads e classificar erros HTTP

- **Descricao:** Adicionar timeout, validacao de schema, campos obrigatorios, valores numericos e classificacao de rede, CORS e HTTP.
- **Criterios de aceite:**
  - Campos obrigatorios ausentes, nulos, nao numericos ou menos de cinco dias produzem `invalid-response`.
  - Campos opcionais podem ser omitidos sem mascarar a resposta como invalida.
  - HTTP 429 produz `rate-limit` e e recuperavel.
  - HTTP 4xx produz `http-client` e nao e recuperavel.
  - HTTP 5xx produz `http-server` e e recuperavel.
  - Falha de rede/CORS produz `network`.
  - Uma requisicao sem resposta em 10 segundos produz `timeout`.
  - O erro preserva o status HTTP quando houver status disponivel.
- **Dependencias:** T-06, T-07.
- **Arquivos provaveis:** `src/services/weatherService.ts`, `src/types/weather.ts`.
- **Tipo:** Data
- **Rastreabilidade:** FR7, FR8, NFR3, NFR5.

### T-09 — Testar services e contratos externos

- **Descricao:** Criar testes unitarios para chamadas, parametros, normalizacao, mapeamento por indice e falhas da API.
- **Criterios de aceite:**
  - Geocoding valido e lista vazia sao testados.
  - Forecast valido verifica todos os campos de `current`.
  - Forecast verifica a associacao correta entre cada indice das listas `daily`.
  - Payload curto, parcial, malformado e com valores invalidos sao rejeitados.
  - HTTP 429, 4xx, 5xx, rede e timeout sao classificados corretamente.
  - Os testes substituem `fetch` ou usam fixture local e nao acessam a rede real.
- **Dependencias:** T-06, T-07, T-08.
- **Arquivos provaveis:** `tests/services/weatherService.test.ts`, `tests/fixtures/openMeteo.ts`.
- **Tipo:** Test
- **Rastreabilidade:** FR1, FR2, FR3, FR4, FR7, FR8, NFR3, NFR5.

## Entrega 4: UI e orquestracao

### T-10 — Criar componentes de busca e estados

- **Descricao:** Implementar formulario, lista de cidades e componentes reutilizaveis de loading, vazio e erro.
- **Criterios de aceite:**
  - O formulario tem label semantico e envio por teclado.
  - Resultados exibem cidade, `admin1`, pais e coordenadas quando disponiveis.
  - Loading identifica se a busca ou o forecast esta em andamento.
  - Estado vazio informa que a cidade nao foi encontrada e permite nova busca.
  - Estado de erro exibe mensagem sem URL, stack trace ou detalhes internos.
  - Acoes principais possuem foco visivel e ordem de tabulacao coerente.
- **Dependencias:** T-01, T-02, T-03, T-06, T-08.
- **Arquivos provaveis:** `src/components/SearchForm.tsx`, `src/components/CityResults.tsx`, `src/components/states/LoadingState.tsx`, `src/components/states/EmptyState.tsx`, `src/components/states/ErrorState.tsx`.
- **Tipo:** UI
- **Rastreabilidade:** FR1, FR2, FR7, NFR1, NFR2, NFR6.

### T-11 — Implementar hook de jornada

- **Descricao:** Orquestrar busca, selecao, forecast, retry, bloqueio de concorrencia e descarte de respostas obsoletas.
- **Criterios de aceite:**
  - O hook expoe as acoes `search`, `selectCity`, `retry`, `setUnit` e `clearError`.
  - A busca transita por `searching`, `selecting` ou `empty` conforme o resultado.
  - A selecao transita por `loading`, `success` ou `error`.
  - Uma nova busca e bloqueada enquanto houver consulta pendente.
  - Uma resposta antiga nao substitui o resultado mais recente.
  - Retry reutiliza a cidade selecionada e nao inicia tentativa automatica.
- **Dependencias:** T-06, T-07, T-08, T-10.
- **Arquivos provaveis:** `src/hooks/useWeather.ts`.
- **Tipo:** Data
- **Rastreabilidade:** FR1, FR2, FR7, FR8, NFR5.

### T-12 — Renderizar clima, forecast e unidade

- **Descricao:** Implementar os componentes de dados atuais, previsao diaria e alternancia Celsius/Fahrenheit.
- **Criterios de aceite:**
  - O clima atual exibe temperatura, condicao, sensacao, umidade, vento e precipitacao.
  - O local, horario de atualizacao e fonte sao exibidos.
  - A previsao exibe exatamente cinco dias com data, minima, maxima, condicao e precipitacao.
  - O toggle atualiza clima atual e previsao sem nova requisicao.
  - Todas as temperaturas exibem a unidade selecionada.
  - Dados incompletos nao sao renderizados como sucesso.
- **Dependencias:** T-04, T-07, T-10, T-11.
- **Arquivos provaveis:** `src/components/CurrentWeather.tsx`, `src/components/Forecast.tsx`, `src/components/UnitToggle.tsx`, `src/App.tsx`.
- **Tipo:** UI
- **Rastreabilidade:** FR3, FR4, FR5, FR7, NFR1, NFR2, NFR6.

### T-13 — Persistir preferencia de unidade

- **Descricao:** Integrar leitura e escrita local da unidade, com fallback seguro quando o armazenamento estiver indisponivel.
- **Criterios de aceite:**
  - Sem preferencia valida, a unidade inicial e Celsius.
  - Somente `C` e `F` sao aceitos no armazenamento.
  - A unidade escolhida permanece apos nova busca e reload no mesmo navegador.
  - Valor invalido ou erro de acesso ao armazenamento usa Celsius sem bloquear a consulta.
  - Nenhum dado meteorologico e salvo localmente.
- **Dependencias:** T-11, T-12.
- **Arquivos provaveis:** `src/hooks/useWeather.ts`, `src/lib/storage.ts`.
- **Tipo:** Data
- **Rastreabilidade:** FR5, FR6, NFR7.

## Entrega 5: Testes de comportamento

### T-14 — Testar componentes e hook

- **Descricao:** Cobrir a jornada com Testing Library, incluindo estados, acessibilidade basica e regras de unidade.
- **Criterios de aceite:**
  - Submissao valida, input invalido e geocoding vazio sao testados.
  - Loading, sucesso, erro e retry sao testados.
  - Selecao de cidade por teclado e bloqueio de consultas simultaneas sao testados.
  - Clima atual e exatamente cinco dias sao verificados.
  - Toggle nao faz nova chamada de rede.
  - Persistencia e fallback da unidade sao testados.
  - Foco, labels, roles e anuncios de loading/erro sao verificados.
- **Dependencias:** T-10, T-11, T-12, T-13.
- **Arquivos provaveis:** `tests/components/SearchForm.test.tsx`, `tests/components/WeatherView.test.tsx`, `tests/hooks/useWeather.test.ts`.
- **Tipo:** Test
- **Rastreabilidade:** Todos os FRs, NFR1, NFR2, NFR5, NFR6, NFR7.

### T-15 — Criar testes E2E da jornada

- **Descricao:** Validar a aplicacao integrada com Playwright e APIs mockadas por rota.
- **Criterios de aceite:**
  - A jornada busca, seleciona cidade e exibe clima e previsao.
  - Existem cenarios E2E separados para cidades homonimas, cidade inexistente e geocoding vazio.
  - Existem cenarios E2E separados para rede, HTTP 429, HTTP 500, JSON invalido, resposta parcial e timeout.
  - Retry manual funciona e nao ha retry automatico.
  - Celsius/Fahrenheit e reload preservam a unidade.
  - A jornada principal funciona por teclado, sem depender de clique para busca, selecao ou retry.
- **Dependencias:** T-14.
- **Arquivos provaveis:** `tests/e2e/weather.spec.ts`, `playwright.config.ts`.
- **Tipo:** Test
- **Rastreabilidade:** Todos os FRs, NFR2, NFR5, NFR7.

## Entrega 6: Hardening e validacao final

### T-16 — Validar responsividade e compatibilidade

- **Descricao:** Executar a matriz de viewports e browsers definida no plano e verificar ausencia de perda de conteudo ou operabilidade.
- **Criterios de aceite:**
  - Testes cobrem mobile retrato, mobile paisagem, tablet e desktop.
  - Busca, selecao, leitura, toggle e retry concluem sem elementos sobrepostos em cada viewport.
  - A matriz de execucao lista as duas versoes estaveis mais recentes de Chrome, Edge, Firefox e Safari; indisponibilidade de browser e registrada como limitacao.
  - Nao ha overflow horizontal nem corte de texto essencial em nenhuma viewport testada.
- **Dependencias:** T-15.
- **Arquivos provaveis:** `tests/e2e/weather.spec.ts`, `playwright.config.ts`.
- **Tipo:** Test
- **Rastreabilidade:** NFR1, NFR4, NFR6, NFR9.

### T-17 — Auditar acessibilidade e performance

- **Descricao:** Produzir evidencia para WCAG AA, foco/teclado e metas de carregamento e consulta.
- **Criterios de aceite:**
  - A auditoria automatizada nao reporta violacoes de contraste ou semantica com severidade `critical` ou `serious`.
  - Fluxos de busca, selecao e retry sao operaveis somente por teclado.
  - Feedback de loading aparece em ate 100 ms no ambiente definido.
  - A primeira tela utilizavel aparece em ate 2 s na rede de validacao.
  - Consultas com resposta retornam sucesso ou erro em ate 3 s em condicoes normais.
  - Timeout encerra consultas em 10 s.
  - O relatorio registra browser, viewport, dispositivo, rede e resultado de cada limite medido.
- **Dependencias:** T-15, T-16.
- **Arquivos provaveis:** `tests/e2e/accessibility.spec.ts`, `tests/e2e/performance.spec.ts`, `README.md`.
- **Tipo:** Test
- **Rastreabilidade:** NFR1, NFR2, NFR3, NFR4, NFR6, NFR9.

### T-18 — Validar build, lint e cobertura

- **Descricao:** Executar os checks obrigatorios do repositorio e corrigir falhas relacionadas ao escopo da aplicacao.
- **Criterios de aceite:**
  - `pnpm lint` conclui sem erros.
  - `pnpm build` conclui sem erros.
  - `pnpm test` conclui sem falhas.
  - Existe pelo menos um teste ou evidencia de validacao associado a cada FR1-FR8 e NFR1-NFR9.
- **Dependencias:** T-05, T-09, T-14, T-15, T-16, T-17.
- **Arquivos provaveis:** `package.json`, arquivos de configuracao e arquivos de teste afetados.
- **Tipo:** Infra
- **Rastreabilidade:** Todos os FRs e NFRs aplicaveis.
