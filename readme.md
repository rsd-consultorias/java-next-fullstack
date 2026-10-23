# Trilha de Treinamento: Java/Spring Boot, Next.js e Angular

Coleção de **três materiais de estudo em Markdown**, do básico ao avançado, com explicações em português e exemplos de código. Cada arquivo funciona de forma independente e pode ser usado como base para um programa de treinamento, para estudo individual ou como material de consulta.

---

## Índice

1. [Visão geral dos materiais](#visão-geral-dos-materiais)
2. [Treinamento Java e Spring Boot](#1-treinamento-java-e-spring-boot)
3. [Treinamento Next.js](#2-treinamento-nextjs)
4. [Treinamento Angular](#3-treinamento-angular)
5. [Trilhas de estudo sugeridas](#trilhas-de-estudo-sugeridas)
6. [Comparativo entre as stacks](#comparativo-entre-as-stacks)
7. [Como usar este material](#como-usar-este-material)
8. [Versões e avisos](#versões-e-avisos)

---

## Visão geral dos materiais

| # | Arquivo | Tecnologias | Versões de referência | Módulos | Duração sugerida | Tamanho |
|---|---------|-------------|-----------------------|:-------:|:----------------:|:-------:|
| 1 | [java-springboot.md](java-springboot.md) | Java, Spring Boot, JPA, Spring Security | Java 21 (LTS), Spring Boot 3.x | 13 | ~24 semanas | ~2.850 linhas |
| 2 | [next.md](next.md) | JavaScript/TypeScript, React, Next.js | Next.js 16, React 19 | 15 + anexo | ~22 semanas | ~2.900 linhas |
| 3 | [angular.md](angular.md) | TypeScript, Angular, RxJS, NgRx | Angular 22 | 13 + anexo | ~24 semanas | ~2.650 linhas |

**Padrão comum a todos os arquivos**

- Sumário navegável no início.
- Explicação conceitual seguida de exemplos de código.
- **Exercícios** ao final de cada módulo.
- **Plano de estudos** semanal.
- **Projeto final** que integra todos os módulos.
- Lista de referências recomendadas.

---

## 1. Treinamento Java e Spring Boot

📄 **Arquivo:** [java-springboot.md](java-springboot.md)

**Para quem:** quem quer construir **back-ends e APIs** corporativas, partindo do zero em Java até microsserviços em produção.

**O que você aprende:** a linguagem Java completa (do `Hello World` às Virtual Threads), seguida do ecossistema Spring Boot (API REST, persistência, segurança, testes, mensageria e deploy).

### Parte 1: Java

| Módulo | Tema | Destaques |
|:------:|------|-----------|
| [1](java-springboot.md#módulo-1--fundamentos-da-linguagem) | Fundamentos da linguagem | JVM/JDK, tipos, controle de fluxo, arrays, strings, métodos |
| [2](java-springboot.md#módulo-2--programação-orientada-a-objetos) | Programação Orientada a Objetos | Quatro pilares, interfaces, enums, `equals`/`hashCode`, SOLID |
| [3](java-springboot.md#módulo-3--java-intermediário) | Java intermediário | Exceções, Collections, Generics, lambdas, Streams, Optional, `java.time`, NIO, Maven, JUnit 5 |
| [4](java-springboot.md#módulo-4--java-moderno-8-a-21) | Java moderno (8 a 21) | Records, sealed classes, pattern matching, Sequenced Collections |
| [5](java-springboot.md#módulo-5--java-avançado) | Java avançado | Concorrência, `CompletableFuture`, Virtual Threads, memória e GC, reflection, design patterns, Mockito |

### Parte 2: Spring Boot

| Módulo | Tema | Destaques |
|:------:|------|-----------|
| [6](java-springboot.md#módulo-6--primeiros-passos-com-spring-boot) | Primeiros passos | IoC/DI, beans, escopos, `@ConfigurationProperties` |
| [7](java-springboot.md#módulo-7--api-rest) | API REST | DTOs, Bean Validation, `ProblemDetail`, tratamento global de erros |
| [8](java-springboot.md#módulo-8--persistência-com-spring-data-jpa) | Spring Data JPA | Relacionamentos, N+1, paginação, transações, Flyway, Specifications |
| [9](java-springboot.md#módulo-9--configuração-profiles-e-arquitetura) | Configuração e arquitetura | Profiles, camadas, arquitetura hexagonal, logging, CORS |
| [10](java-springboot.md#módulo-10--spring-security-e-jwt) | Spring Security e JWT | Autenticação stateless, filtros, autorização por método |
| [11](java-springboot.md#módulo-11--testes) | Testes | Mockito, `@WebMvcTest`, `@DataJpaTest`, Testcontainers |
| [12](java-springboot.md#módulo-12--spring-boot-avançado) | Spring Boot avançado | Cache, `@Async`, eventos, AOP, `RestClient`, Resilience4j, Kafka/RabbitMQ, Spring Batch |
| [13](java-springboot.md#módulo-13--microsserviços-e-produção) | Microsserviços e produção | Actuator, observabilidade, Docker, Spring Cloud, GraalVM, CI/CD |

**Projeto final:** plataforma de e-commerce (catálogo, usuários, pedidos, pagamento com *circuit breaker*, mensageria e relatórios). [Ver plano de estudos](java-springboot.md#plano-de-estudos-sugerido)

---

## 2. Treinamento Next.js

📄 **Arquivo:** [next.md](next.md)

**Para quem:** quem quer construir **aplicações web full-stack** com React, com foco em desempenho, SEO e backend integrado.

**O que você aprende:** os pré-requisitos (JavaScript moderno, TypeScript e React) e, em seguida, o Next.js com **App Router**, Server Components, Server Actions, o novo modelo de **Cache Components** e a operação em produção.

### Parte 1: Pré-requisitos

| Módulo | Tema | Destaques |
|:------:|------|-----------|
| [1](next.md#módulo-1--javascript-moderno-e-typescript) | JavaScript moderno e TypeScript | `async/await`, desestruturação, tipos, generics, uniões discriminadas |
| [2](next.md#módulo-2--react-essencial) | React essencial | Componentes, hooks, Context, hooks customizados, novidades do React 19 |

### Parte 2: Next.js

| Módulo | Tema | Destaques |
|:------:|------|-----------|
| [3](next.md#módulo-3--primeiros-passos-com-nextjs) | Primeiros passos | Criação do projeto, estrutura, `next.config.ts`, variáveis de ambiente |
| [4](next.md#módulo-4--roteamento-app-router) | Roteamento (App Router) | Rotas dinâmicas, layouts, grupos, `loading`/`error`/`not-found` |
| [5](next.md#módulo-5--server-components-e-client-components) | Server e Client Components | Quando usar cada um, composição, `server-only` |
| [6](next.md#módulo-6--busca-de-dados-renderização-e-streaming) | Dados, renderização e streaming | `Suspense`, `Promise.all`, SSG/SSR/ISR, `generateStaticParams` |
| [7](next.md#módulo-7--server-actions-e-formulários) | Server Actions e formulários | Zod, `useActionState`, `useOptimistic`, segurança |
| [8](next.md#módulo-8--route-handlers-apis) | Route Handlers | CRUD, webhooks assinados, SSE, CORS |
| [9](next.md#módulo-9--estilização-imagens-fontes-e-seo) | Estilo, imagens, fontes e SEO | Tailwind, `next/image`, `next/font`, metadados, sitemap, OG dinâmico |
| [10](next.md#módulo-10--banco-de-dados-e-autenticação) | Banco de dados e autenticação | Prisma, sessão com JWT, Data Access Layer, `proxy.ts`, RBAC |
| [11](next.md#módulo-11--cache-e-cache-components) | Cache e Cache Components | `"use cache"`, `cacheLife`, `cacheTag`, `updateTag` |
| [12](next.md#módulo-12--recursos-avançados) | Recursos avançados | Parallel/intercepting routes, `after()`, i18n, CSP, React Compiler, OpenTelemetry |
| [13](next.md#módulo-13--performance-e-otimização) | Performance | Core Web Vitals, `dynamic()`, diagnóstico |
| [14](next.md#módulo-14--testes) | Testes | Vitest, Testing Library, Playwright |
| [15](next.md#módulo-15--deploy-e-produção) | Deploy e produção | Vercel, Docker `standalone`, várias instâncias, CI/CD |
| [Anexo A](next.md#anexo-a--pages-router-legado) | Pages Router (legado) | Tabela de equivalências e migração incremental |

**Projeto final:** loja virtual com painel administrativo (catálogo com cache, autenticação, checkout com Server Actions, webhooks, modal com rotas interceptadas e E2E). [Ver plano de estudos](next.md#plano-de-estudos-sugerido)

---

## 3. Treinamento Angular

📄 **Arquivo:** [angular.md](angular.md)

**Para quem:** quem quer construir **SPAs e aplicações corporativas de grande porte** em um framework completo e opinativo.

**O que você aprende:** TypeScript e o Angular moderno (standalone, **Signals**, control flow, zoneless, **Signal Forms**), do primeiro componente até SSR, testes e deploy.

### Parte 1: Pré-requisitos

| Módulo | Tema | Destaques |
|:------:|------|-----------|
| [1](angular.md#módulo-1--typescript-para-angular) | TypeScript para Angular | Tipos, interfaces, decorators, generics, `async/await` |

### Parte 2: Angular

| Módulo | Tema | Destaques |
|:------:|------|-----------|
| [2](angular.md#módulo-2--primeiros-passos-com-angular) | Primeiros passos | CLI, estrutura, `app.config.ts`, standalone, zoneless e OnPush |
| [3](angular.md#módulo-3--componentes-e-templates) | Componentes e templates | Binding, `@if`/`@for`/`@switch`/`@let`, `input()`/`output()`/`model()`, projeção de conteúdo |
| [4](angular.md#módulo-4--signals-e-reatividade) | Signals e reatividade | `signal`, `computed`, `effect`, `linkedSignal`, `resource`, `httpResource` |
| [5](angular.md#módulo-5--diretivas-pipes-e-injeção-de-dependência) | Diretivas, pipes e DI | `inject()`, escopos de providers, `InjectionToken`, `hostDirectives` |
| [6](angular.md#módulo-6--roteamento) | Roteamento | Lazy loading, guards e resolvers funcionais, `withComponentInputBinding` |
| [7](angular.md#módulo-7--httpclient-e-rxjs) | HttpClient e RxJS | Interceptors, `switchMap`/`concatMap`/`exhaustMap`, autocomplete com debounce |
| [8](angular.md#módulo-8--formulários) | Formulários | Signal Forms, Reactive Forms tipados, template-driven |
| [9](angular.md#módulo-9--estado-e-arquitetura) | Estado e arquitetura | Serviços com signals, NgRx SignalStore, organização por features |
| [10](angular.md#módulo-10--testes) | Testes | Vitest, `TestBed`, `HttpTestingController`, Playwright |
| [11](angular.md#módulo-11--recursos-avançados) | Recursos avançados | `@defer`, SSR e hidratação incremental, i18n, Material/CDK/Aria, micro frontends, segurança |
| [12](angular.md#módulo-12--performance-e-otimização) | Performance | `NgOptimizedImage`, budgets, virtual scroll, diagnóstico |
| [13](angular.md#módulo-13--build-deploy-e-produção) | Build, deploy e produção | Config em runtime, Docker com Nginx/SSR, CI/CD, `ng update` |
| [Anexo A](angular.md#anexo-a--migração-de-código-legado) | Migração de código legado | *Schematics* e tabela legado → moderno |

**Projeto final:** plataforma de gestão de pedidos, com vitrine pública e backoffice (SSR, Signal Forms, SignalStore, `@defer`, Material/CDK e E2E). [Ver plano de estudos](angular.md#plano-de-estudos-sugerido)

---

## Trilhas de estudo sugeridas

### 🧱 Trilha Back-end (Java)
1. `java-springboot.md`, Módulos 1 a 4 (linguagem)
2. Módulos 6 a 9 (Spring Boot essencial)
3. Módulos 10 e 11 (segurança e testes)
4. Módulo 5 (Java avançado) e Módulos 12 e 13

### 🎨 Trilha Front-end com React (Next.js)
1. `next.md`, Módulos 1 e 2 (JS/TS e React)
2. Módulos 3 a 8 (fundamentos do Next.js)
3. Módulos 9 a 11 (SEO, autenticação e cache)
4. Módulos 12 a 15 (avançado, performance, testes e deploy)

### 🏢 Trilha Front-end corporativo (Angular)
1. `angular.md`, Módulo 1 (TypeScript)
2. Módulos 2 a 6 (núcleo do framework e roteamento)
3. Módulos 7 a 9 (HTTP, formulários e arquitetura)
4. Módulos 10 a 13 (testes, avançado, performance e deploy)

### 🔗 Trilha Full-stack
| Etapa | Material | Objetivo |
|:-----:|----------|----------|
| 1 | Java: Módulos 1 a 4 | Dominar a linguagem e a POO |
| 2 | Spring Boot: Módulos 6 a 8 | Construir uma API REST com banco de dados |
| 3 | **Um** front-end (Next.js **ou** Angular): fundamentos | Consumir a API construída |
| 4 | Spring Boot: Módulos 10 e 11 + front-end: autenticação | Integrar login com JWT ponta a ponta |
| 5 | Módulos avançados de cada lado | Observabilidade, cache e performance |
| 6 | Deploy com Docker + CI/CD | Publicar o conjunto |

> **Dica:** escolha **um** front-end por vez. Next.js (React) e Angular resolvem problemas parecidos com filosofias diferentes, e estudar os dois simultaneamente costuma atrapalhar a fixação.

### Qual front-end escolher?

| Se você... | Considere |
|------------|-----------|
| Precisa de **SEO** forte, sites de conteúdo, e-commerce e um back-end integrado ao front | **Next.js** |
| Trabalha em **aplicações corporativas grandes**, com muitos times e padrões rígidos | **Angular** |
| Já conhece React e quer ampliar para o servidor | **Next.js** |
| Vem de Java/C# e prefere um framework estruturado, com DI e tipagem forte por padrão | **Angular** |

---

## Comparativo entre as stacks

| Conceito | Spring Boot | Next.js | Angular |
|----------|-------------|---------|---------|
| **Linguagem** | Java | TypeScript | TypeScript |
| **Papel** | Back-end / API | Full-stack (React) | Front-end (SPA/SSR) |
| **Injeção de dependências** | Nativa (`@Service`, construtor) | Não nativa (módulos, funções) | Nativa (`inject()`, providers) |
| **Roteamento** | `@RestController` + `@GetMapping` | Pastas e arquivos (`app/`) | Configuração em `Routes` |
| **Dados e persistência** | Spring Data JPA | ORM de sua escolha (Prisma, Drizzle) | `HttpClient` / `httpResource` |
| **Validação** | Bean Validation (`@Valid`) | Zod nas Server Actions | Signal Forms / Reactive Forms |
| **Reatividade** | WebFlux (opcional) | React (estado, Server Components) | **Signals** + RxJS |
| **Segurança** | Spring Security + JWT | Sessão em cookie, DAL, `proxy.ts` | Guards (UX) + cookies `HttpOnly` |
| **Testes** | JUnit 5, Mockito, Testcontainers | Vitest, Testing Library, Playwright | Vitest, `TestBed`, Playwright |
| **Cache** | `@Cacheable` (Caffeine/Redis) | `"use cache"`, `cacheTag` | Interceptors, `shareReplay` |
| **Build/empacotamento** | Maven/Gradle → JAR | Turbopack → `standalone` | esbuild → `dist/` |
| **Deploy típico** | Docker + Kubernetes | Vercel / Docker | Nginx / Docker |
| **Observabilidade** | Actuator + Micrometer | OpenTelemetry (`instrumentation.ts`) | `ErrorHandler` + Sentry |

**Ideia central de cada um**

- **Spring Boot**: *convenção sobre configuração*. Auto-configuração e *starters* eliminam o código repetitivo.
- **Next.js**: *servidor primeiro*. Os componentes rodam no servidor por padrão e você leva ao navegador só o que é interativo.
- **Angular**: *plataforma completa*. Roteador, formulários, HTTP, DI e testes vêm integrados e padronizados.

---

## Como usar este material

1. **Siga a ordem dos módulos** dentro de cada arquivo. Cada um se apoia no anterior.
2. **Digite os exemplos**, não apenas leia. Quebre o código de propósito para entender os erros.
3. **Faça os exercícios** no fim de cada módulo antes de avançar.
4. **Construa o projeto final** aos poucos, aplicando cada módulo ao projeto à medida que o estuda.
5. **Use os checklists** de produção (segurança, performance, deploy) como lista de revisão nos seus projetos reais.
6. Para **treinamentos em grupo**, cada módulo pode virar um encontro: teoria curta, demonstração ao vivo e exercícios em pares.

### Pré-requisitos gerais

| Para estudar | É necessário |
|--------------|--------------|
| Java e Spring Boot | JDK 21, Maven ou Gradle, uma IDE (IntelliJ IDEA ou VS Code), Docker (módulos finais) |
| Next.js | Node.js 20.9 ou superior, editor (VS Code), Docker (módulo final) |
| Angular | Node.js LTS atual, Angular CLI, editor (VS Code), Docker (módulo final) |

---

## Versões e avisos

- **Versões de referência**: Java 21 e Spring Boot 3.x · Next.js 16 com React 19 · Angular 22.
- Esses ecossistemas **mudam rápido**, principalmente Next.js (modelo de cache) e Angular (Signals, Signal Forms, zoneless). Ao seguir tutoriais de terceiros, **confira a versão utilizada** e compare com a documentação oficial.
- Os exemplos têm fins **didáticos**. Não foram executados em um projeto real e podem exigir ajustes de dependências e configuração. Antes de levá-los à produção, revise segurança, tratamento de erros e testes.
- Recursos marcados como *developer preview* ou experimentais (por exemplo, partes de `resource`/`httpResource` e *router resources* no Angular) podem mudar entre versões.
- Documentação oficial para consulta:
  - Java/Spring: https://docs.oracle.com/en/java/ · https://docs.spring.io/spring-boot/
  - Next.js e React: https://nextjs.org/docs · https://react.dev
  - Angular: https://angular.dev

---

## Estrutura dos arquivos

```
.
├── README.md                          ← este arquivo (índice e resumo)
├── java-springboot.md     ← Java + Spring Boot (13 módulos)
├── next.md              ← JS/TS/React + Next.js (15 módulos + anexo)
└── angular.md             ← TypeScript + Angular (13 módulos + anexo)
```