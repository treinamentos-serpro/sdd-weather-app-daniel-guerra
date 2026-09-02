# Plano Técnico: Aplicação de Previsão do Tempo

## 1. Architecture Overview

A aplicação será uma SPA React publicada como site estático no GitHub Pages.
Não haverá backend próprio no MVP.

```text
UI (React components)
        |
        v
useWeather (orquestração e estado da jornada)
        |
        v
Weather service (HTTP, timeout, validação e normalização)
        |
        +--> Open-Meteo Geocoding API
        +--> Open-Meteo Forecast API
        |
        v
Modelos internos em Celsius + timezone da cidade
```

Responsabilidades:

- **Componentes:** renderizar formulário, resultados, clima atual, previsão,
  unidade e estados de loading/erro/vazio.
- **Hook de weather:** coordenar busca, seleção, consulta meteorológica, retry e
  unidade sem expor detalhes de URL à UI.
- **Services:** executar chamadas HTTP, aplicar timeout, validar payloads,
  classificar erros e converter respostas externas para os modelos internos.
- **Lib:** conter funções puras de conversão, arredondamento, formatação e
  tradução de códigos meteorológicos.
- **Types:** definir contratos compartilhados entre services, hook e UI.

Rastreamento: FR1-FR8, NFR1, NFR2, NFR3, NFR5, NFR6 e NFR7.

## 2. Tech Stack

| Tecnologia | Uso | Justificativa | Rastreabilidade |
| --- | --- | --- | --- |
| React + TypeScript strict | UI, composição e contratos | Já é a stack do projeto e oferece tipagem para os dados externos. | NFR1, NFR2, NFR5 |
| Vite | Build e servidor local | Compatível com deploy estático no GitHub Pages. | NFR3, NFR4 |
| Tailwind CSS | Estilos responsivos | Segue a stack existente e facilita abordagem mobile-first. | NFR1, NFR6 |
| `fetch` com `AbortController` | HTTP e cancelamento por timeout | Evita dependência adicional para um fluxo simples. | FR7, FR8, NFR3, NFR5 |
| Vitest + Testing Library | Testes unitários e de componentes | Permite testar regras puras, services e estados da UI. | Todos os FRs, NFR2, NFR3 |
| Playwright | Testes E2E em viewports e browsers | Valida a jornada real e responsividade. | NFR1, NFR2, NFR4, NFR9 |
| Biome | Lint e formatação | Ferramenta configurada no repositório. | Qualidade de entrega |

## 3. Project Structure

```text
src/
├── components/
│   ├── SearchForm.tsx
│   ├── CityResults.tsx
│   ├── CurrentWeather.tsx
│   ├── Forecast.tsx
│   ├── UnitToggle.tsx
│   └── states/
│       ├── LoadingState.tsx
│       ├── EmptyState.tsx
│       └── ErrorState.tsx
├── hooks/
│   └── useWeather.ts
├── services/
│   └── weatherService.ts
├── lib/
│   ├── temperature.ts
│   ├── weatherCodes.ts
│   └── formatters.ts
├── types/
│   └── weather.ts
├── styles/
│   └── globals.css
├── App.tsx
└── main.tsx
```

- `SearchForm`: entrada, validação de apresentação e envio do nome da cidade.
- `CityResults`: lista selecionável com contexto geográfico.
- `CurrentWeather`: dados atuais e metadados da consulta.
- `Forecast`: cinco cartões ou itens diários.
- `UnitToggle`: alternância entre `C` e `F`.
- `states/`: estados visuais sem lógica de acesso à rede.
- `useWeather`: estado e ações da jornada.
- `weatherService`: única fronteira com a Open-Meteo.
- `lib`: regras puras e testáveis.
- `types`: contratos externos normalizados e estados públicos.

Rastreamento: FR1-FR8, NFR1, NFR2, NFR5 e NFR6.

## 4. Data Model

Os modelos abaixo representam dados normalizados pela aplicação. Temperaturas
permanecem em Celsius até a camada de apresentação.

```typescript
type Unit = "C" | "F"

interface City {
  id: number
  name: string
  admin1?: string
  country: string
  latitude: number
  longitude: number
}

interface CurrentWeather {
  temperatureC: number
  apparentTemperatureC: number
  relativeHumidity: number
  precipitationMm: number
  windSpeedKmh: number
  weatherCode: number
  observedAt: string
}

interface ForecastDay {
  date: string
  minTemperatureC: number
  maxTemperatureC: number
  precipitationMm: number
  weatherCode: number
}

interface WeatherData {
  city: City
  current: CurrentWeather
  daily: ForecastDay[]
  source: "Open-Meteo"
  timezone: string
  updatedAt: string
}
```

Contratos de estado:

```typescript
type WeatherErrorKind =
  | "invalid-input"
  | "not-found"
  | "network"
  | "timeout"
  | "rate-limit"
  | "http-client"
  | "http-server"
  | "invalid-response"

interface WeatherError {
  kind: WeatherErrorKind
  message: string
  retryable: boolean
  status?: number
}

interface WeatherState {
  phase: "idle" | "searching" | "selecting" | "loading" | "success" | "empty" | "error"
  query: string
  cities: City[]
  selectedCity?: City
  data?: WeatherData
  unit: Unit
  error?: WeatherError
}
```

Regras do contrato:

- `relativeHumidity` deve estar entre 0 e 100.
- Coordenadas, temperaturas e velocidades devem ser números finitos.
- `precipitationMm` deve ser um número finito maior ou igual a zero.
- `daily` deve conter exatamente cinco itens antes de produzir `WeatherData`;
  itens adicionais devem ser descartados após a validação.
- `observedAt`, `updatedAt` e `date` devem ser strings ISO ou datas ISO locais
  conforme o contrato da Open-Meteo.
- `City.id`, `City.name`, `City.country`, `City.latitude` e `City.longitude`
  são obrigatórios; `admin1` é opcional.
- `WeatherData.timezone`, `WeatherData.updatedAt`, todos os campos de
  `CurrentWeather` e todos os campos de cada `ForecastDay` são obrigatórios.
- Campos obrigatórios ausentes, nulos ou não numéricos invalidam a resposta.
- Campos opcionais de apresentação podem ser omitidos sem invalidar o dado
  meteorológico.

Mapeamento do Forecast:

- `current.temperature_2m` -> `CurrentWeather.temperatureC`.
- `current.apparent_temperature` -> `CurrentWeather.apparentTemperatureC`.
- `current.relative_humidity_2m` -> `CurrentWeather.relativeHumidity`.
- `current.precipitation` -> `CurrentWeather.precipitationMm`.
- `current.weather_code` -> `CurrentWeather.weatherCode`.
- `current.wind_speed_10m` -> `CurrentWeather.windSpeedKmh`.
- `current.time` -> `CurrentWeather.observedAt` e `WeatherData.updatedAt`.
- Para cada índice `i` de `daily`, `time[i]`, `weather_code[i]`,
  `temperature_2m_min[i]`, `temperature_2m_max[i]` e
  `precipitation_sum[i]` devem formar um único `ForecastDay`.
- O mapeamento diário deve preservar a ordem e a correspondência por índice;
  nenhum campo de uma posição pode ser associado a outra data.
- As unidades esperadas na resposta são Celsius, percentual, km/h e mm,
  conforme `current_units` e `daily_units`. A camada de apresentação é
  responsável por converter somente temperaturas para Fahrenheit.

Rastreamento: FR2-FR6, NFR5 e NFR6.

## 5. Data Flow

### Busca e seleção

1. `SearchForm` recebe o texto e envia a intenção de busca.
2. `useWeather` normaliza espaços nas extremidades e valida o mínimo de dois
   caracteres.
3. O hook muda `phase` para `searching` e chama `weatherService.searchCities`.
4. O service chama o endpoint de geocoding e retorna `City[]` normalizado.
5. O hook apresenta `selecting` quando há resultados ou `empty` quando a lista é
   vazia.
6. `CityResults` registra a cidade escolhida pelo usuário.

### Consulta meteorológica

1. A seleção de uma cidade inicia `weatherService.getWeather(city)`.
2. O service usa latitude e longitude retornados pela cidade; o timezone é
  obtido na resposta do forecast.
3. O payload é validado e normalizado para `WeatherData`.
4. O hook muda para `success` e mantém a cidade selecionada.
5. `CurrentWeather` e `Forecast` recebem os dados em Celsius.
6. `UnitToggle` altera somente `unit`; a UI converte e arredonda os valores sem
   nova chamada de rede.

### Retry

1. Uma falha classificável como recuperável produz `phase: "error"` e mantém
   `selectedCity`.
2. `ErrorState` exibe retry quando `error.retryable` é verdadeiro.
3. O retry chama novamente a consulta da mesma cidade.
4. Uma consulta pendente bloqueia novos envios até sucesso, erro ou timeout.

Rastreamento: FR1-FR8 e NFR5.

## 6. External APIs

### Geocoding

- **Endpoint:** `https://geocoding-api.open-meteo.com/v1/search`
- **Método:** `GET`
- **Parâmetros:**
  - `name`: texto normalizado da busca;
  - `count=10`: limite de resultados apresentados;
  - `language=pt`: nomes próprios retornados pela API; a interface permanece
    em pt-BR;
  - `format=json`.

O service considera lista vazia como `not-found`. Cada resultado é normalizado
com `id`, nome, `admin1` quando disponível, país, latitude e longitude. O
timezone não faz parte do modelo `City` e será obtido na resposta de forecast.

### Forecast

- **Endpoint:** `https://api.open-meteo.com/v1/forecast`
- **Método:** `GET`
- **Parâmetros:**
  - `latitude` e `longitude`: coordenadas da cidade selecionada;
  - `current=temperature_2m,relative_humidity_2m,apparent_temperature,precipitation,weather_code,wind_speed_10m`;
  - `daily=weather_code,temperature_2m_max,temperature_2m_min,precipitation_sum`;
  - `forecast_days=5`;
  - `timezone=auto`.

A resposta deve fornecer timezone, horário atual e séries diárias alinhadas.
O service normaliza as séries em cinco `ForecastDay` e rejeita payloads com
menos de cinco dias ou campos obrigatórios ausentes.

### Política de acesso

As chamadas serão diretas do navegador porque a Open-Meteo não exige API key e
o produto será publicado no GitHub Pages. O plano não adiciona proxy ou
backend no MVP. O service deverá tratar falhas de CORS, rede e HTTP como erros
classificados, sem expor detalhes técnicos ao usuário.

Rastreamento: FR1-FR4, FR7-FR8, NFR3, NFR5 e NFR7.

## 7. State Management

O estado da jornada ficará concentrado em `useWeather`, usando estado React
local e ações explícitas:

- `search(query)`: valida e executa geocoding;
- `selectCity(city)`: inicia a consulta meteorológica;
- `retry()`: repete a última consulta meteorológica;
- `setUnit(unit)`: altera a unidade e persiste a preferência;
- `clearError()`: remove erro quando uma nova ação é iniciada.

A unidade será lida do `localStorage` na inicialização. Somente `C` e `F` serão
aceitos; valor ausente, inválido ou erro de acesso usa `C`. O dado meteorológico
não será armazenado localmente no MVP.

O hook manterá a identificação da solicitação ativa para ignorar qualquer
resultado que não corresponda à consulta corrente. Novas buscas serão
bloqueadas enquanto houver uma busca ou previsão pendente.

Rastreamento: FR5-FR8, NFR5 e NFR7.

## 8. Error Handling Strategy

| Situação | Classificação | Estado/mensagem | Retry |
| --- | --- | --- | --- |
| Entrada inválida | `invalid-input` | Orientar nome válido; nenhuma requisição | Não |
| Geocoding vazio | `not-found` | “Cidade não encontrada” | Nova busca |
| Falha de rede ou CORS | `network` | “Não foi possível conectar ao serviço.” | Sim |
| HTTP 429 | `rate-limit` | “O serviço está temporariamente indisponível.” | Sim |
| HTTP 4xx diferente de 429 | `http-client` | “Não foi possível consultar a cidade.” | Não |
| HTTP 5xx | `http-server` | “O serviço está indisponível.” | Sim |
| JSON malformado ou schema inválido | `invalid-response` | “O serviço retornou dados inválidos.” | Não |
| Sem resposta em 10 segundos | `timeout` | “A consulta excedeu o tempo limite.” | Sim |

Regras adicionais:

- O timeout de 10 segundos aplica-se individualmente a cada requisição.
- O service deve usar `AbortController` para encerrar a requisição excedida.
- Nenhum erro deve deixar `phase` em `searching` ou `loading`.
- O retry não é automático.
- Após falha de forecast, `selectedCity` permanece disponível.
- Uma resposta inválida nunca substitui dados válidos já exibidos.
- Erros HTTP devem preservar o status numérico em `WeatherError.status` para
  diagnóstico e teste, sem exibi-lo necessariamente na UI.
- A UI não exibirá URL, stack trace ou detalhes internos da API.

Rastreamento: FR7-FR8, NFR3, NFR5 e NFR6.

## 9. Testing Strategy

### Unitários

Testar com Vitest as funções puras:

- normalização e validação de nomes de cidade;
- conversão Celsius/Fahrenheit;
- arredondamento para inteiro mais próximo;
- formatação de datas no timezone recebido;
- tradução de códigos WMO conhecidos e fallback desconhecido;
- validação e normalização de payloads;
- classificação de respostas HTTP, incluindo status 4xx, 429 e 5xx, timeout e
  rede;
- validação de campos obrigatórios, listas diárias e valores numéricos.

### Componentes e hook

Usar Testing Library para verificar:

- submissão válida e rejeição de input inválido;
- loading de busca e forecast;
- lista de cidades e seleção por teclado;
- estado vazio para geocoding sem resultados;
- renderização de todos os campos obrigatórios;
- exibição de exatamente cinco dias;
- alternância sem nova chamada de rede;
- persistência e fallback da unidade;
- mensagens de erro e retry;
- bloqueio de submissões simultâneas;
- descarte de resposta obsoleta.
- foco visível, ordem de tabulação, labels, roles e anúncio de loading/erro;
- operação completa do formulário, seleção e retry somente por teclado.

### E2E com Playwright

Usar `page.route` para mockar as APIs e evitar dependência da rede real. Cobrir:

1. busca de cidade, seleção e visualização do clima atual;
2. seleção entre cidades homônimas;
3. previsão de cinco dias;
4. troca Celsius/Fahrenheit;
5. restauração da preferência após reload;
6. cidade inexistente e geocoding vazio;
7. erro de rede, HTTP 429, HTTP 500, JSON inválido e timeout;
8. retry manual bem-sucedido;
9. teclado no fluxo principal;
10. viewports mobile retrato, mobile paisagem, tablet e desktop;
11. ausência de campos obrigatórios e resposta parcial com campo opcional;
12. unidade e mensagens legíveis sem perda de conteúdo nas viewports suportadas.

### Evidências e limites

- Cada teste deve referenciar um ou mais IDs `AC-FR*` ou NFRs relacionados.
- A validação de acessibilidade deve incluir auditoria automatizada de
  contraste e uma verificação manual dos fluxos de teclado e foco.
- Testes de performance devem registrar navegador, viewport, dispositivo e
  rede; cada métrica deve ser aprovada conforme o limite definido na spec.
- A disponibilidade de 99,5% será acompanhada por monitoramento da interface
  publicada, separado da disponibilidade da Open-Meteo, e não será simulada
  localmente no ciclo unitário/E2E.

Rastreamento: todos os FRs e NFR1-NFR9.

## 10. Risks & Trade-offs

| Decisão | Benefício | Trade-off | Mitigação |
| --- | --- | --- | --- |
| Chamadas diretas à Open-Meteo | Simplicidade e compatibilidade com GitHub Pages | Dependência de CORS, limites e disponibilidade externa | Timeout, classificação de erros, mocks nos testes e monitoramento posterior |
| Sem cache de forecast no MVP | Evita dados desatualizados e complexidade de privacidade | Requisições repetidas podem aumentar dependência da fonte | Limitar uma consulta pendente por vez e avaliar cache no próximo ciclo |
| Estado local em hook | Baixa complexidade e escopo pequeno | Estado não é compartilhado entre abas | Persistir somente a unidade, conforme FR6 |
| Dados internos em Celsius | Conversão consistente e sem nova requisição no toggle | Vento e precipitação não mudam de unidade | Exibir essas medidas na unidade fornecida pela fonte |
| `forecast_days=5` e seleção do primeiro dia | Contrato simples e previsível | Mudanças de data dependem do timezone retornado | Validar timezone e séries antes de renderizar |
| Bloqueio durante consulta | Evita corrida entre respostas | Usuário precisa aguardar até 10 segundos | Feedback de loading e timeout claro |
