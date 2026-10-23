# Treinamento Angular: do Básico ao Avançado

> Material de apoio para um programa de treinamento completo. Os exemplos usam **Angular 22**, **TypeScript** e a abordagem moderna do framework: **componentes standalone**, **Signals**, **control flow** (`@if`, `@for`), **zoneless** e **Signal Forms**. Onde o Angular mudou bastante nos últimos anos, há uma nota indicando o que é legado.

## Sumário

**Parte 1: Pré-requisitos**

1. [TypeScript para Angular](#módulo-1--typescript-para-angular)

**Parte 2: Angular**

2. [Primeiros passos](#módulo-2--primeiros-passos-com-angular)
3. [Componentes e templates](#módulo-3--componentes-e-templates)
4. [Signals e reatividade](#módulo-4--signals-e-reatividade)
5. [Diretivas, pipes e injeção de dependência](#módulo-5--diretivas-pipes-e-injeção-de-dependência)
6. [Roteamento](#módulo-6--roteamento)
7. [HttpClient e RxJS](#módulo-7--httpclient-e-rxjs)
8. [Formulários](#módulo-8--formulários)
9. [Estado e arquitetura](#módulo-9--estado-e-arquitetura)
10. [Testes](#módulo-10--testes)
11. [Recursos avançados](#módulo-11--recursos-avançados)
12. [Performance e otimização](#módulo-12--performance-e-otimização)
13. [Build, deploy e produção](#módulo-13--build-deploy-e-produção)
14. [Anexo: migração de código legado](#anexo-a--migração-de-código-legado)
15. [Plano de estudos e projeto final](#plano-de-estudos-sugerido)

---

# PARTE 1: PRÉ-REQUISITOS

## Módulo 1 · TypeScript para Angular

O Angular é escrito em **TypeScript**, e sua experiência de desenvolvimento depende de tipos, classes, decorators e módulos. Se você já domina TypeScript, pule para o [Módulo 2](#módulo-2--primeiros-passos-com-angular).

### 1.1 Tipos básicos e inferência

```ts
let titulo: string = "Angular";
let preco = 99.9;                       // inferido como number
let ativo: boolean = true;
let tags: string[] = ["web", "spa"];
let par: [string, number] = ["idade", 30];   // tupla
let qualquer: unknown = JSON.parse("{}");     // prefira unknown a any

// União de literais (substitui enums na maioria dos casos)
type Status = "pendente" | "pago" | "cancelado";
let s: Status = "pago";

// null e undefined (com strictNullChecks, ligado por padrão no Angular)
let nome: string | null = null;
const tamanho = nome?.length ?? 0;      // optional chaining + nullish coalescing
```

### 1.2 Interfaces, types e utilitários

```ts
interface Produto {
  id: number;
  nome: string;
  preco: number;
  descricao?: string;           // opcional
  readonly criadoEm: Date;      // somente leitura
}

type ProdutoNovo = Omit<Produto, "id" | "criadoEm">;
type ProdutoResumo = Pick<Produto, "id" | "nome">;
type ProdutoParcial = Partial<Produto>;
type Catalogo = Record<string, Produto[]>;

// União discriminada
type Resultado<T> =
  | { ok: true; dados: T }
  | { ok: false; erro: string };

function tratar(r: Resultado<Produto>) {
  if (r.ok) console.log(r.dados.nome);   // o TypeScript sabe que "dados" existe aqui
  else console.error(r.erro);
}
```

### 1.3 Classes, modificadores e decorators

```ts
class Conta {
  // Parameter properties: declara e atribui em uma linha
  constructor(
    private readonly titular: string,
    protected saldo = 0,
  ) {}

  depositar(valor: number): void {
    if (valor <= 0) throw new Error("Valor inválido");
    this.saldo += valor;
  }

  get resumo(): string {
    return `${this.titular}: R$ ${this.saldo.toFixed(2)}`;
  }
}

// Decorators são funções que adicionam metadados/comportamento.
// O Angular usa @Component, @Injectable, @Directive, @Pipe...
function Log(alvo: any, nomeMetodo: string, desc: PropertyDescriptor) {
  const original = desc.value;
  desc.value = function (...args: unknown[]) {
    console.log(`Chamando ${nomeMetodo}`, args);
    return original.apply(this, args);
  };
}
```

### 1.4 Generics

```ts
interface Page<T> {
  itens: T[];
  total: number;
  pagina: number;
}

function primeiro<T>(lista: T[]): T | undefined {
  return lista[0];
}

class Repositorio<T extends { id: number }> {
  private dados = new Map<number, T>();
  salvar(item: T) { this.dados.set(item.id, item); }
  buscar(id: number): T | undefined { return this.dados.get(id); }
}
```

### 1.5 Módulos, Promises e async/await

```ts
// produto.ts
export interface Produto { id: number; nome: string; }
export const MAX_ITENS = 50;
export default function criarProduto(nome: string): Produto { return { id: Date.now(), nome }; }

// outro arquivo
import criarProduto, { MAX_ITENS, type Produto } from "./produto";

// async/await
async function buscar(url: string): Promise<Produto[]> {
  const res = await fetch(url);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);   // fetch não rejeita em 4xx/5xx
  return res.json();
}

const [a, b] = await Promise.all([buscar("/api/a"), buscar("/api/b")]);   // em paralelo
```

### 1.6 `tsconfig` estrito

O Angular gera projetos com `"strict": true`. Mantenha assim: ele pega uma quantidade enorme de erros em tempo de compilação, inclusive **dentro dos templates** (`strictTemplates`).

### Exercícios do Módulo 1
1. Modele um carrinho de compras usando uniões discriminadas para `vazio`, `com-itens` e `finalizado`.
2. Escreva um `Repositorio<T>` genérico com `salvar`, `buscar`, `remover` e `listar`.
3. Crie um tipo `ProdutoAtualizacao` a partir de `Produto` usando `Partial` e `Omit`.

---

# PARTE 2: ANGULAR

## Módulo 2 · Primeiros passos com Angular

### 2.1 O que é o Angular

Framework **completo** (*batteries included*), mantido pelo Google, para construir aplicações web de médio e grande porte.

| Recurso | O que oferece |
|---------|---------------|
| Componentes | Blocos de UI com template, estilo e lógica |
| Signals | Reatividade fina e explícita |
| Roteamento | Navegação, *lazy loading*, guards e resolvers |
| DI | Injeção de dependências nativa e hierárquica |
| Formulários | Template-driven, Reactive e **Signal Forms** |
| HTTP | `HttpClient` com interceptors |
| SSR | Renderização no servidor, *hydration* e *prerender* |
| Ferramentas | Angular CLI, DevTools, testes integrados |

**Angular vs. React/Vue (resumo)**: o Angular oferece uma solução integrada e opinativa (roteador, formulários, HTTP, DI, testes), o que favorece padronização em times grandes. Em troca, exige aprender mais conceitos no início.

### 2.2 Instalação e novo projeto

Use a versão **LTS atual do Node.js** (consulte a matriz de compatibilidade em `angular.dev/reference/versions`).

```bash
npm install -g @angular/cli
ng version

ng new meu-app          # o assistente pergunta estilo (CSS/SCSS), SSR e ferramentas de IA
cd meu-app
ng serve --open         # http://localhost:4200
```

### 2.3 Comandos essenciais do CLI

```bash
ng serve                       # servidor de desenvolvimento com HMR
ng build                       # build de produção (dist/)
ng test                        # testes (Vitest nos projetos novos)
ng generate component produtos/lista     # atalho: ng g c produtos/lista
ng generate service produtos/produto
ng generate directive destaque
ng generate pipe moeda
ng generate guard auth
ng generate interceptor auth
ng add @angular/material       # adiciona bibliotecas via schematics
ng update                      # atualiza o Angular e as dependências
```

### 2.4 Estrutura do projeto

```
meu-app/
├── src/
│   ├── app/
│   │   ├── app.ts               ← componente raiz (classe App)
│   │   ├── app.html
│   │   ├── app.css
│   │   ├── app.config.ts        ← providers globais (router, http...)
│   │   └── app.routes.ts        ← rotas
│   ├── index.html
│   ├── main.ts                  ← ponto de entrada
│   └── styles.css               ← estilos globais
├── public/                      ← arquivos estáticos
├── angular.json                 ← configuração do workspace/build
├── package.json
└── tsconfig.json
```

> **Convenção atual (Angular 20+)**: o CLI gera arquivos sem os sufixos `.component`/`.service` por padrão (por exemplo `produto-lista.ts` com a classe `ProdutoLista`). Neste material usamos esse estilo para componentes e mantemos o sufixo `Service` nos serviços, para deixar o papel de cada classe evidente.

### 2.5 Bootstrap da aplicação

```ts
// src/main.ts
import { bootstrapApplication } from "@angular/platform-browser";
import { appConfig } from "./app/app.config";
import { App } from "./app/app";

bootstrapApplication(App, appConfig).catch((err) => console.error(err));
```

```ts
// src/app/app.config.ts
import { ApplicationConfig, provideBrowserGlobalErrorListeners } from "@angular/core";
import { provideRouter, withComponentInputBinding } from "@angular/router";
import { provideHttpClient, withFetch } from "@angular/common/http";
import { routes } from "./app.routes";

export const appConfig: ApplicationConfig = {
  providers: [
    provideBrowserGlobalErrorListeners(),
    provideRouter(routes, withComponentInputBinding()),
    provideHttpClient(withFetch()),
  ],
};
```

```ts
// src/app/app.ts
import { Component } from "@angular/core";
import { RouterOutlet, RouterLink } from "@angular/router";

@Component({
  selector: "app-root",
  imports: [RouterOutlet, RouterLink],
  template: `
    <nav><a routerLink="/">Início</a> | <a routerLink="/produtos">Produtos</a></nav>
    <main><router-outlet /></main>
  `,
  styleUrl: "./app.css",
})
export class App {}
```

### 2.6 Standalone, zoneless e OnPush: o Angular moderno

Três mudanças que definem o Angular atual:

| Conceito | Antes | Agora |
|----------|-------|-------|
| **Componentes standalone** | `NgModule` para declarar tudo | Cada componente declara seus próprios `imports` (padrão desde o v19; não precisa de `standalone: true`) |
| **Zoneless** | `zone.js` monkey-patch detecta mudanças | Padrão em projetos novos (v21+): o Angular só atualiza a tela quando um **signal** muda (ou um evento/`markForCheck` ocorre) |
| **OnPush** | `Default` verificava tudo | **OnPush é o padrão** nas versões mais recentes (v22). A estratégia antiga passou a se chamar `Eager` |

> Em projetos existentes, `ng update` faz as migrações automáticas. Veja o [Anexo A](#anexo-a--migração-de-código-legado).

### 2.7 Primeiro componente completo

```ts
// src/app/contador.ts
import { Component, signal } from "@angular/core";

@Component({
  selector: "app-contador",
  template: `
    <h2>Contador</h2>
    <p>Valor: {{ valor() }}</p>
    <button (click)="incrementar()">+1</button>
    <button (click)="valor.set(0)">Zerar</button>
  `,
})
export class Contador {
  valor = signal(0);

  incrementar() {
    this.valor.update((v) => v + 1);
  }
}
```

Uso em outro componente:

```ts
@Component({
  selector: "app-home",
  imports: [Contador],           // standalone: importe o que usa no template
  template: `<app-contador />`,
})
export class Home {}
```

### Exercícios do Módulo 2
1. Crie um projeto, rode `ng serve` e altere o texto do componente raiz.
2. Gere um componente `saudacao` com `ng g c saudacao` e use-o no `App`.
3. Abra o `angular.json` e identifique os *builders* de `build`, `serve` e `test`.

---

## Módulo 3 · Componentes e templates

### 3.1 Anatomia de um componente

```ts
@Component({
  selector: "app-produto-card",    // como usar no HTML
  imports: [CurrencyPipe],          // dependências de template
  templateUrl: "./produto-card.html",
  styleUrl: "./produto-card.css",   // estilos com escopo no componente
})
export class ProdutoCard { /* ... */ }
```

### 3.2 Data binding

```html
<!-- Interpolação -->
<h1>{{ titulo() }}</h1>
<p>Total: {{ preco() * quantidade() | currency:'BRL' }}</p>

<!-- Property binding: componente → DOM -->
<img [src]="produto.imagem" [alt]="produto.nome" />
<button [disabled]="carregando()">Salvar</button>

<!-- Event binding: DOM → componente -->
<button (click)="salvar()">Salvar</button>
<input (input)="termo.set($any($event.target).value)" />
<input (keyup.enter)="buscar()" />

<!-- Classes e estilos -->
<div [class.ativo]="selecionado()" [style.width.px]="largura()"></div>
<div [class]="{ ativo: selecionado(), erro: temErro() }"></div>

<!-- Two-way binding: [(...)] -->
<input [(ngModel)]="nome" />            <!-- importe FormsModule no componente -->
<app-contador [(valor)]="total" />      <!-- com model() -->

<!-- Template reference variable -->
<input #campo />
<button (click)="buscar(campo.value)">Buscar</button>
```

### 3.3 Control flow: `@if`, `@for`, `@switch`, `@let`

Substitui `*ngIf`, `*ngFor` e `*ngSwitch`. Está embutido no compilador (não precisa importar nada) e é mais rápido.

```html
@if (usuario(); as u) {
  <p>Olá, {{ u.nome }}</p>
} @else if (carregando()) {
  <p>Carregando...</p>
} @else {
  <a routerLink="/login">Entrar</a>
}

<ul>
  @for (p of produtos(); track p.id; let i = $index, ultimo = $last) {
    <li [class.ultimo]="ultimo">{{ i + 1 }}. {{ p.nome }}</li>
  } @empty {
    <li>Nenhum produto encontrado.</li>
  }
</ul>

@switch (status()) {
  @case ("pendente") { <span class="badge amarelo">Pendente</span> }
  @case ("pago")     { <span class="badge verde">Pago</span> }
  @default           { <span class="badge">Desconhecido</span> }
}

<!-- Variável local de template -->
@let total = carrinho().itens.length;
@let nomeCompleto = usuario().nome + " " + usuario().sobrenome;
<p>{{ total }} itens para {{ nomeCompleto }}</p>
```

> **`track` é obrigatório em `@for`.** Use um identificador único (`track p.id`). `track $index` só para listas estáticas. Variáveis de contexto: `$index`, `$count`, `$first`, `$last`, `$even`, `$odd`.

### 3.4 Comunicação entre componentes

**Entradas (`input`)**: pai → filho.

```ts
import { Component, input, numberAttribute } from "@angular/core";

@Component({
  selector: "app-produto-card",
  template: `
    <article>
      <h3>{{ nome() }}</h3>
      <p>R$ {{ preco().toFixed(2) }}</p>
      @if (destaque()) { <span>⭐ Destaque</span> }
    </article>
  `,
})
export class ProdutoCard {
  nome = input.required<string>();                       // obrigatório
  preco = input(0, { transform: numberAttribute });      // padrão + conversão
  destaque = input(false);
}
```

```html
<app-produto-card nome="Mouse" [preco]="49.9" destaque />
```

**Saídas (`output`)**: filho → pai.

```ts
import { output } from "@angular/core";

export class ProdutoCard {
  comprar = output<number>();           // emite o id
  id = input.required<number>();

  aoClicar() { this.comprar.emit(this.id()); }
}
```

```html
<app-produto-card [id]="p.id" (comprar)="adicionarAoCarrinho($event)" />
```

**`model()`**: entrada **e** saída (*two-way binding*).

```ts
export class Stepper {
  valor = model(0);                     // [(valor)] no pai
  mais() { this.valor.update((v) => v + 1); }
}
```

```html
<app-stepper [(valor)]="quantidade" />
```

### 3.5 Projeção de conteúdo (`ng-content`)

```ts
@Component({
  selector: "app-card",
  template: `
    <header><ng-content select="[titulo]" /></header>
    <section><ng-content /></section>
    <footer><ng-content select="[rodape]" /></footer>
  `,
})
export class Card {}
```

```html
<app-card>
  <h2 titulo>Pedido #123</h2>
  <p>Conteúdo principal</p>
  <button rodape>Fechar</button>
</app-card>
```

### 3.6 Consultas: `viewChild` e `contentChild`

```ts
import { viewChild, viewChildren, ElementRef, afterNextRender } from "@angular/core";

@Component({
  selector: "app-busca",
  template: `<input #campo placeholder="Buscar..." />`,
})
export class Busca {
  campo = viewChild.required<ElementRef<HTMLInputElement>>("campo");

  constructor() {
    afterNextRender(() => this.campo().nativeElement.focus());   // acesso seguro ao DOM
  }
}
```

### 3.7 Ciclo de vida

```ts
import { OnInit, OnDestroy, inject, DestroyRef } from "@angular/core";

export class Exemplo implements OnInit, OnDestroy {
  private destroyRef = inject(DestroyRef);

  constructor() {
    // Alternativa moderna ao ngOnDestroy: registrar limpeza onde o recurso é criado
    const id = setInterval(() => console.log("tick"), 1000);
    this.destroyRef.onDestroy(() => clearInterval(id));
  }

  ngOnInit() { /* inputs já disponíveis */ }
  ngOnDestroy() { /* limpeza clássica */ }
}
```

| Hook | Quando |
|------|--------|
| `constructor` | Criação (use para injeção e configuração) |
| `ngOnInit` | Após o primeiro conjunto de inputs |
| `ngOnChanges` | Quando inputs mudam (com signals, prefira `computed`/`effect`) |
| `afterNextRender` / `afterEveryRender` | Após a renderização no navegador (manipular DOM) |
| `ngOnDestroy` / `DestroyRef` | Limpeza |

### 3.8 Encapsulamento de estilos e `host`

```ts
@Component({
  selector: "app-botao",
  template: `<ng-content />`,
  host: {
    role: "button",
    "[class.desabilitado]": "desabilitado()",
    "(click)": "aoClicar($event)",
    "[attr.aria-disabled]": "desabilitado()",
  },
  styles: `
    :host { display: inline-block; padding: 8px 16px; background: #2563eb; color: #fff; }
    :host(.desabilitado) { opacity: .5; pointer-events: none; }
  `,
})
export class Botao {
  desabilitado = input(false);
  aoClicar(e: Event) { /* ... */ }
}
```

> Evite `@HostBinding`/`@HostListener`: o objeto `host` é a forma recomendada.

### 3.9 Pipes de template

```html
{{ nome | uppercase }}
{{ dataPedido | date:'dd/MM/yyyy HH:mm' }}
{{ preco | currency:'BRL':'symbol':'1.2-2' }}
{{ progresso | percent:'1.0-1' }}
{{ objeto | json }}
{{ texto | slice:0:100 }}
{{ promessaOuObservable | async }}
```

**Localização em português do Brasil**

```ts
// app.config.ts
import { registerLocaleData } from "@angular/common";
import localePt from "@angular/common/locales/pt";
import { LOCALE_ID } from "@angular/core";

registerLocaleData(localePt);

providers: [{ provide: LOCALE_ID, useValue: "pt-BR" }]
```

### Exercícios do Módulo 3
1. Crie `ProdutoCard` (com `input`/`output`) e uma lista que renderiza vários com `@for`.
2. Implemente um `Modal` com `ng-content` e slots nomeados (título, corpo, rodapé).
3. Crie um `Stepper` com `model()` e use `[(valor)]` no pai.

---

## Módulo 4 · Signals e reatividade

Signals são a base do Angular moderno: valores reativos que **notificam quem depende deles** quando mudam, e o Angular atualiza somente o necessário.

### 4.1 `signal`, `computed` e `effect`

```ts
import { signal, computed, effect } from "@angular/core";

const preco = signal(100);
const quantidade = signal(2);

// computed: valor derivado, preguiçoso e memorizado (só recalcula se as dependências mudarem)
const total = computed(() => preco() * quantidade());

console.log(total());          // 200

preco.set(150);                // substitui
quantidade.update((q) => q + 1); // atualiza a partir do valor atual
console.log(total());          // 450
```

```ts
// Objetos e arrays: sempre crie novos valores (imutabilidade)
const itens = signal<Item[]>([]);
itens.update((lista) => [...lista, novoItem]);
itens.update((lista) => lista.filter((i) => i.id !== id));
itens.update((lista) => lista.map((i) => (i.id === id ? { ...i, qtd: i.qtd + 1 } : i)));
```

```ts
// effect: efeitos colaterais quando signals mudam (log, localStorage, integrações)
export class Preferencias {
  tema = signal<"claro" | "escuro">("claro");

  constructor() {
    effect(() => {
      document.documentElement.dataset["tema"] = this.tema();
      localStorage.setItem("tema", this.tema());
    });
  }
}
```

> **Regra**: use `computed` para **derivar valores** e `effect` só para **efeitos colaterais** (sincronizar com algo externo). Não use `effect` para propagar estado entre signals, pois isso gera ciclos e código difícil de seguir.

### 4.2 Signals em componentes

```ts
@Component({
  selector: "app-carrinho",
  template: `
    @for (item of itens(); track item.id) {
      <li>{{ item.nome }} × {{ item.qtd }}</li>
    }
    <p>Itens: {{ quantidade() }} | Total: {{ total() | currency:'BRL' }}</p>
    <button (click)="adicionar()">Adicionar</button>
  `,
})
export class Carrinho {
  itens = signal<Item[]>([]);

  quantidade = computed(() => this.itens().reduce((s, i) => s + i.qtd, 0));
  total = computed(() => this.itens().reduce((s, i) => s + i.qtd * i.preco, 0));

  adicionar() {
    this.itens.update((l) => [...l, { id: Date.now(), nome: "Mouse", preco: 50, qtd: 1 }]);
  }
}
```

### 4.3 `linkedSignal`: estado derivado que também pode ser alterado

```ts
import { linkedSignal } from "@angular/core";

export class Selecao {
  opcoes = input.required<string[]>();

  // Reinicia para a primeira opção sempre que "opcoes" mudar, mas o usuário pode alterar
  selecionada = linkedSignal(() => this.opcoes()[0]);

  escolher(o: string) { this.selecionada.set(o); }
}
```

### 4.4 `resource`: dados assíncronos com signals

`resource` conecta uma requisição assíncrona aos signals: quando os parâmetros mudam, ele refaz o carregamento e expõe estado de `loading`, `error` e `value`.

```ts
import { resource, input } from "@angular/core";

@Component({
  selector: "app-produto-detalhe",
  template: `
    @if (produto.isLoading()) {
      <p>Carregando...</p>
    } @else if (produto.error()) {
      <p>Erro ao carregar. <button (click)="produto.reload()">Tentar de novo</button></p>
    } @else if (produto.hasValue()) {
      <h2>{{ produto.value().nome }}</h2>
    }
  `,
})
export class ProdutoDetalhe {
  id = input.required<number>();

  produto = resource({
    params: () => ({ id: this.id() }),                 // reage a mudanças do id
    loader: async ({ params, abortSignal }) => {
      const res = await fetch(`/api/produtos/${params.id}`, { signal: abortSignal });
      if (!res.ok) throw new Error("Falha na requisição");
      return (await res.json()) as Produto;
    },
  });
}
```

Para `HttpClient` (com interceptors, testes e `HttpTestingController`), use **`httpResource`**:

```ts
import { httpResource } from "@angular/common/http";

export class ProdutoLista {
  categoria = signal("todas");

  produtos = httpResource<Produto[]>(() => `/api/produtos?categoria=${this.categoria()}`);
  // produtos.value(), produtos.isLoading(), produtos.error(), produtos.reload()
}
```

> `resource` e `httpResource` evoluíram bastante nas últimas versões (em `v19` eram experimentais e o parâmetro se chamava `request`). Confirme o status e a assinatura na documentação da versão que você usa.

### 4.5 Interoperabilidade com RxJS

```ts
import { toSignal, toObservable, takeUntilDestroyed } from "@angular/core/rxjs-interop";

export class Exemplo {
  private http = inject(HttpClient);

  // Observable → Signal (cancela a inscrição automaticamente)
  produtos = toSignal(this.http.get<Produto[]>("/api/produtos"), { initialValue: [] });

  // Signal → Observable
  termo = signal("");
  termo$ = toObservable(this.termo);

  constructor() {
    interval(1000).pipe(takeUntilDestroyed()).subscribe(console.log);   // evita vazamento
  }
}
```

### 4.6 Detecção de mudanças: como o Angular decide atualizar a tela

```ts
// Com OnPush (padrão no v22) e zoneless, a tela atualiza quando:
//  1. um signal lido no template muda;
//  2. um evento do template dispara (click, input...);
//  3. o async pipe emite;
//  4. você chama markForCheck() / ChangeDetectorRef explicitamente.
```

```ts
// Configuração explícita (útil em projetos migrados)
@Component({
  selector: "app-x",
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `...`,
})
export class X {}
```

**Cuidado com mutações**: `this.lista.push(x)` num objeto simples (sem signal) **não** atualiza a tela. Use signals e imutabilidade.

**Zoneless em projeto existente**

```ts
// app.config.ts
providers: [provideZonelessChangeDetection()],   // e remova "zone.js" de polyfills no angular.json
```

### 4.7 `untracked` e leitura sem dependência

```ts
effect(() => {
  const termo = this.termo();                                // dependência
  const usuario = untracked(() => this.usuarioLogado());     // lido sem criar dependência
  this.analytics.registrarBusca(termo, usuario?.id);
});
```

### Exercícios do Módulo 4
1. Implemente um carrinho com `signal` + `computed` (quantidade e total).
2. Crie uma lista filtrável: `termo` e `categoria` como signals e `filtrados` como `computed`.
3. Use `httpResource` para carregar produtos e mostrar *loading*, erro e botão "tentar novamente".
4. Salve o tema escolhido no `localStorage` com `effect`.

---

## Módulo 5 · Diretivas, pipes e injeção de dependência

### 5.1 Diretivas de atributo

Alteram aparência ou comportamento de um elemento existente.

```ts
import { Directive, ElementRef, inject, input } from "@angular/core";

@Directive({
  selector: "[appDestaque]",
  host: {
    "(mouseenter)": "ligar()",
    "(mouseleave)": "desligar()",
  },
})
export class Destaque {
  private el = inject(ElementRef<HTMLElement>);
  cor = input("#fef08a", { alias: "appDestaque" });

  ligar() { this.el.nativeElement.style.backgroundColor = this.cor(); }
  desligar() { this.el.nativeElement.style.backgroundColor = ""; }
}
```

```html
<p appDestaque>Passe o mouse</p>
<p [appDestaque]="'#bbf7d0'">Outra cor</p>
```

### 5.2 Directive composition (`hostDirectives`)

Reaproveita diretivas existentes dentro de um componente.

```ts
@Component({
  selector: "app-botao-destacado",
  hostDirectives: [{ directive: Destaque, inputs: ["appDestaque: cor"] }],
  template: `<ng-content />`,
})
export class BotaoDestacado {}
```

### 5.3 Pipes customizados

```ts
import { Pipe, PipeTransform } from "@angular/core";

@Pipe({ name: "truncar" })      // pure por padrão: só reexecuta se a entrada mudar
export class TruncarPipe implements PipeTransform {
  transform(valor: string, limite = 50, sufixo = "…"): string {
    return valor.length > limite ? valor.slice(0, limite) + sufixo : valor;
  }
}
```

```html
<p>{{ descricao | truncar:80 }}</p>
```

### 5.4 Injeção de Dependência (DI)

O Angular cria e entrega instâncias de classes (serviços) para quem precisa, via `inject()`.

```ts
import { Injectable, inject, signal } from "@angular/core";

@Injectable({ providedIn: "root" })          // singleton em toda a aplicação, com tree-shaking
export class LogService {
  private mensagens = signal<string[]>([]);
  readonly historico = this.mensagens.asReadonly();   // expõe somente leitura

  registrar(msg: string) {
    this.mensagens.update((l) => [...l, `${new Date().toISOString()} ${msg}`]);
  }
}

@Component({ selector: "app-x", template: `...` })
export class X {
  private log = inject(LogService);      // preferível ao construtor
  constructor() { this.log.registrar("X criado"); }
}
```

> `inject()` só funciona em um **contexto de injeção**: construtor, inicializadores de campo, funções de `provide*`, guards funcionais e `runInInjectionContext`.

### 5.5 Hierarquia e escopos de providers

```ts
// 1) Global (root)
@Injectable({ providedIn: "root" }) class A {}

// 2) Por rota: nova instância para a árvore de rotas
{ path: "checkout", providers: [CheckoutStore], loadChildren: () => import("./checkout/routes") }

// 3) Por componente: uma instância por componente (e seus filhos)
@Component({ selector: "app-form", providers: [FormStateService], template: `...` })
class Form {}
```

### 5.6 Tokens, interfaces e configuração

Interfaces não existem em tempo de execução, então use um `InjectionToken`:

```ts
import { InjectionToken, inject } from "@angular/core";

export interface AppConfig { apiUrl: string; itensPorPagina: number; }

export const APP_CONFIG = new InjectionToken<AppConfig>("APP_CONFIG");

// app.config.ts
providers: [{ provide: APP_CONFIG, useValue: { apiUrl: "/api", itensPorPagina: 20 } }]

// uso
private config = inject(APP_CONFIG);
```

**Trocando implementações (Dependency Inversion)**

```ts
export abstract class Notificador {
  abstract enviar(msg: string): void;
}

@Injectable() export class ToastNotificador extends Notificador { enviar(m: string) { /* ... */ } }
@Injectable() export class ConsoleNotificador extends Notificador { enviar(m: string) { console.log(m); } }

providers: [{ provide: Notificador, useClass: isDevMode() ? ConsoleNotificador : ToastNotificador }]
```

**Factory com dependências**

```ts
providers: [{
  provide: Logger,
  useFactory: () => (inject(APP_CONFIG).debug ? new VerboseLogger() : new SilentLogger()),
}]
```

### 5.7 Inicialização da aplicação

```ts
import { provideAppInitializer, inject } from "@angular/core";

providers: [
  provideAppInitializer(async () => {
    await inject(ConfigService).carregar();    // a app só inicia depois que isso terminar
  }),
]
```

### 5.8 Opções de resolução

```ts
const opcional = inject(Servico, { optional: true });   // null se não houver provider
const doPai = inject(Servico, { skipSelf: true });      // ignora o próprio injetor
const local = inject(Servico, { self: true });          // apenas o injetor do componente
```

### Exercícios do Módulo 5
1. Crie `ToastService` com signals e um componente `ToastContainer` que exiba as mensagens.
2. Crie a diretiva `appClickFora` que emite um `output` ao clicar fora do elemento.
3. Defina um `InjectionToken<AppConfig>` e consuma-o em um serviço.

---

## Módulo 6 · Roteamento

### 6.1 Rotas básicas

```ts
// app.routes.ts
import { Routes } from "@angular/router";
import { Home } from "./home";

export const routes: Routes = [
  { path: "", component: Home, title: "Início" },
  { path: "sobre", loadComponent: () => import("./sobre").then((m) => m.Sobre), title: "Sobre" },
  { path: "produtos", loadChildren: () => import("./produtos/produtos.routes").then((m) => m.PRODUTOS_ROUTES) },
  { path: "antigo", redirectTo: "produtos", pathMatch: "full" },
  { path: "**", loadComponent: () => import("./nao-encontrado").then((m) => m.NaoEncontrado) },
];
```

```ts
// produtos/produtos.routes.ts
export const PRODUTOS_ROUTES: Routes = [
  { path: "", component: ProdutoLista },
  { path: "novo", component: ProdutoForm },
  { path: ":id", component: ProdutoDetalhe, title: "Detalhe do produto" },
  { path: ":id/editar", component: ProdutoForm },
];
```

> **Lazy loading** (`loadComponent` / `loadChildren`) divide o *bundle*: o código de `/produtos` só é baixado quando o usuário acessa essa rota.

### 6.2 Navegação no template

```html
<nav>
  <a routerLink="/" routerLinkActive="ativo" [routerLinkActiveOptions]="{ exact: true }">Início</a>
  <a routerLink="/produtos" routerLinkActive="ativo">Produtos</a>
</nav>

<a [routerLink]="['/produtos', produto.id]">Ver</a>
<a [routerLink]="['/produtos']" [queryParams]="{ categoria: 'telas', pagina: 2 }">Telas</a>

<router-outlet />
```

### 6.3 Navegação programática

```ts
import { Router } from "@angular/router";

export class ProdutoForm {
  private router = inject(Router);

  salvar() {
    // ...
    this.router.navigate(["/produtos", novo.id]);
    this.router.navigate(["/produtos"], { queryParams: { salvo: true } });
    this.router.navigateByUrl("/");
  }
}
```

### 6.4 Parâmetros de rota como `input` (recomendado)

Com `withComponentInputBinding()` no `provideRouter`, parâmetros de rota, *query params* e dados de *resolvers* chegam como **inputs**:

```ts
// rota: { path: ":id", component: ProdutoDetalhe }   URL: /produtos/42?aba=reviews

@Component({ selector: "app-produto-detalhe", template: `...` })
export class ProdutoDetalhe {
  id = input.required<string>();          // path param
  aba = input("detalhes");                // query param (com valor padrão)

  produto = httpResource<Produto>(() => `/api/produtos/${this.id()}`);
}
```

Alternativa clássica: `inject(ActivatedRoute)` com `paramMap`/`queryParamMap` (observables).

### 6.5 Guards funcionais

```ts
// auth.guard.ts
import { CanActivateFn, CanMatchFn, Router } from "@angular/router";

export const authGuard: CanActivateFn = (route, state) => {
  const auth = inject(AuthService);
  const router = inject(Router);

  return auth.autenticado()
    ? true
    : router.createUrlTree(["/login"], { queryParams: { returnUrl: state.url } });
};

export const papelGuard = (papel: string): CanMatchFn => () =>
  inject(AuthService).temPapel(papel) || inject(Router).createUrlTree(["/acesso-negado"]);

// Aplicação
{ path: "dashboard", loadComponent: () => import("./dashboard"), canActivate: [authGuard] },
{ path: "admin", loadChildren: () => import("./admin/routes"), canMatch: [papelGuard("ADMIN")] },
```

Outros guards: `canActivateChild`, `canDeactivate` (avisar sobre alterações não salvas) e `canMatch` (impede até o download do código lazy).

```ts
export const semPerdaDeDados: CanDeactivateFn<{ temAlteracoes: () => boolean }> = (comp) =>
  !comp.temAlteracoes() || confirm("Descartar alterações?");
```

> **Guards são UX, não segurança.** Todo código no navegador pode ser contornado. A autorização real precisa ser feita **no servidor/API**.

### 6.6 Resolvers

Carregam dados **antes** de ativar a rota.

```ts
export const produtoResolver: ResolveFn<Produto> = (route) =>
  inject(ProdutoService).buscar(Number(route.paramMap.get("id")));

{ path: ":id", component: ProdutoDetalhe, resolve: { produto: produtoResolver } }

// Com withComponentInputBinding, o resultado chega como input:
export class ProdutoDetalhe {
  produto = input.required<Produto>();
}
```

### 6.7 Rotas aninhadas e layouts

```ts
export const routes: Routes = [
  {
    path: "",
    component: LayoutPublico,          // tem <router-outlet /> próprio
    children: [
      { path: "", component: Home },
      { path: "login", component: Login },
    ],
  },
  {
    path: "app",
    component: LayoutAutenticado,
    canActivate: [authGuard],
    children: [
      { path: "dashboard", component: Dashboard },
      { path: "perfil", component: Perfil },
    ],
  },
];
```

### 6.8 Recursos extras do roteador

```ts
provideRouter(
  routes,
  withComponentInputBinding(),
  withViewTransitions(),                  // transições animadas entre páginas (View Transitions API)
  withPreloading(PreloadAllModules),      // pré-carrega rotas lazy em segundo plano
  withInMemoryScrolling({ scrollPositionRestoration: "enabled", anchorScrolling: "enabled" }),
  withRouterConfig({ paramsInheritanceStrategy: "always" }),
)
```

```ts
// Título dinâmico
{ path: ":id", component: ProdutoDetalhe, title: (route) => `Produto ${route.paramMap.get("id")}` }

// Dados estáticos na rota
{ path: "admin", component: Admin, data: { papel: "ADMIN" } }
```

> **Novidade (v22.2, developer preview)**: *router resources* para carregar dados antes ou durante a navegação com signals, e a sintaxe `@boundary` para capturar erros em trechos do template. Acompanhe as notas de versão antes de usar em produção.

### Exercícios do Módulo 6
1. Monte rotas para `/`, `/produtos`, `/produtos/:id` e um 404 com *lazy loading*.
2. Crie `authGuard` e proteja `/dashboard`, com redirecionamento para `/login?returnUrl=...`.
3. Implemente `canDeactivate` em um formulário com alterações pendentes.
4. Faça os filtros de uma listagem persistirem nos *query params*.

---

## Módulo 7 · HttpClient e RxJS

### 7.1 Configurando o `HttpClient`

```ts
// app.config.ts
import { provideHttpClient, withFetch, withInterceptors } from "@angular/common/http";

providers: [
  provideHttpClient(
    withFetch(),                                  // usa a Fetch API (necessário para SSR eficiente)
    withInterceptors([authInterceptor, erroInterceptor]),
  ),
]
```

### 7.2 Serviço de API

```ts
@Injectable({ providedIn: "root" })
export class ProdutoService {
  private http = inject(HttpClient);
  private base = "/api/produtos";

  listar(filtros: { categoria?: string; pagina?: number } = {}) {
    let params = new HttpParams();
    if (filtros.categoria) params = params.set("categoria", filtros.categoria);
    if (filtros.pagina) params = params.set("pagina", filtros.pagina);
    return this.http.get<Page<Produto>>(this.base, { params });
  }

  buscar(id: number) {
    return this.http.get<Produto>(`${this.base}/${id}`);
  }

  criar(dados: ProdutoNovo) {
    return this.http.post<Produto>(this.base, dados);
  }

  atualizar(id: number, dados: Partial<ProdutoNovo>) {
    return this.http.patch<Produto>(`${this.base}/${id}`, dados);
  }

  remover(id: number) {
    return this.http.delete<void>(`${this.base}/${id}`);
  }
}
```

> **Observables HTTP são "frios" e completam sozinhos.** Nada acontece até alguém se inscrever (`subscribe`, `async` pipe ou `toSignal`), e eles terminam após a resposta, então não precisam de `unsubscribe`.

### 7.3 Consumindo no componente

```ts
@Component({
  selector: "app-produto-lista",
  imports: [CurrencyPipe],
  template: `
    @if (carregando()) { <p>Carregando...</p> }
    @if (erro()) { <p class="erro">{{ erro() }}</p> }
    @for (p of produtos(); track p.id) {
      <li>{{ p.nome }}: {{ p.preco | currency:'BRL' }}</li>
    }
  `,
})
export class ProdutoLista {
  private service = inject(ProdutoService);

  produtos = signal<Produto[]>([]);
  carregando = signal(true);
  erro = signal<string | null>(null);

  constructor() {
    this.service.listar().subscribe({
      next: (page) => { this.produtos.set(page.itens); this.carregando.set(false); },
      error: () => { this.erro.set("Não foi possível carregar."); this.carregando.set(false); },
    });
  }
}
```

Alternativa mais enxuta com `httpResource` (Módulo 4) ou `toSignal`.

### 7.4 Interceptors funcionais

```ts
// auth.interceptor.ts
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = inject(AuthService).token();
  if (!token || !req.url.startsWith("/api")) return next(req);

  return next(req.clone({ setHeaders: { Authorization: `Bearer ${token}` } }));
};

// erro.interceptor.ts
export const erroInterceptor: HttpInterceptorFn = (req, next) => {
  const router = inject(Router);        // injete FORA do callback assíncrono
  const auth = inject(AuthService);
  const toast = inject(ToastService);

  return next(req).pipe(
    catchError((err: HttpErrorResponse) => {
      if (err.status === 401) { auth.sair(); router.navigate(["/login"]); }
      else if (err.status >= 500) toast.erro("Erro no servidor. Tente novamente.");
      return throwError(() => err);
    }),
  );
};

// Opcional: reexecutar falhas transitórias
export const retryInterceptor: HttpInterceptorFn = (req, next) =>
  next(req).pipe(retry({ count: 2, delay: (_e, tentativa) => timer(tentativa * 500) }));
```

### 7.5 RxJS: o essencial

RxJS modela **fluxos de eventos ao longo do tempo**. No Angular moderno, signals cobrem o estado e RxJS continua ótimo para **eventos, HTTP e composição assíncrona**.

```ts
import { of, from, interval, timer, fromEvent, forkJoin, combineLatest, merge, Subject, BehaviorSubject } from "rxjs";
import { map, filter, tap, switchMap, mergeMap, concatMap, exhaustMap, debounceTime,
         distinctUntilChanged, catchError, retry, takeUntil, shareReplay, startWith, finalize } from "rxjs/operators";
```

| Operador | Uso |
|----------|-----|
| `map`, `filter`, `tap` | Transformar, filtrar, efeitos colaterais |
| `switchMap` | **Cancela** a requisição anterior (busca, autocomplete, navegação) |
| `concatMap` | Executa uma por vez, **em ordem** (salvar sequencial) |
| `mergeMap` | Executa **em paralelo** |
| `exhaustMap` | **Ignora** novas enquanto há uma em andamento (botão de salvar) |
| `debounceTime`, `distinctUntilChanged` | Esperar o usuário parar de digitar; evitar repetição |
| `forkJoin` | Aguarda **todas** terminarem (como `Promise.all`) |
| `combineLatest` | Combina o último valor de cada fonte |
| `catchError`, `retry` | Tratamento e nova tentativa |
| `shareReplay(1)` | Compartilha e guarda o último valor (cache) |

### 7.6 Busca com *debounce* e cancelamento

```ts
@Component({
  selector: "app-busca",
  imports: [FormsModule],
  template: `
    <input [ngModel]="termo()" (ngModelChange)="termo.set($event)" placeholder="Buscar produto..." />
    @for (p of resultados(); track p.id) { <li>{{ p.nome }}</li> }
  `,
})
export class Busca {
  private service = inject(ProdutoService);
  termo = signal("");

  resultados = toSignal(
    toObservable(this.termo).pipe(
      debounceTime(300),
      distinctUntilChanged(),
      switchMap((t) => (t.length < 2 ? of([]) : this.service.buscarPorNome(t).pipe(catchError(() => of([]))))),
    ),
    { initialValue: [] as Produto[] },
  );
}
```

### 7.7 Requisições em paralelo e em sequência

```ts
// Paralelo
forkJoin({
  usuario: this.http.get<Usuario>("/api/me"),
  pedidos: this.http.get<Pedido[]>("/api/pedidos"),
}).subscribe(({ usuario, pedidos }) => { /* ... */ });

// Sequencial dependente
this.http.post<Pedido>("/api/pedidos", dados).pipe(
  switchMap((pedido) => this.http.post(`/api/pedidos/${pedido.id}/pagamento`, pagamento)),
).subscribe();
```

### 7.8 `Subject` e `BehaviorSubject`

```ts
// Evento simples
private cliques$ = new Subject<void>();
cliques$ = this.cliques$.asObservable();

// Estado com valor atual (no Angular moderno, prefira signals)
private usuario$ = new BehaviorSubject<Usuario | null>(null);
```

### 7.9 Gerenciando inscrições

```ts
// 1) async pipe: gerencia sozinho
// <li *...>{{ produtos$ | async }}</li>   → ou use toSignal()

// 2) takeUntilDestroyed(): quando assina manualmente
this.fonte$.pipe(takeUntilDestroyed(this.destroyRef)).subscribe(...);

// 3) Observables HTTP: não precisam (completam sozinhos)
```

### Exercícios do Módulo 7
1. Crie `ProdutoService` com CRUD completo e consuma em um componente.
2. Escreva um interceptor que adicione o `Authorization` e outro que trate 401 e 5xx.
3. Faça um autocomplete com `debounceTime` + `switchMap` e compare com `mergeMap` (observe o que acontece com respostas fora de ordem).

---

## Módulo 8 · Formulários

O Angular oferece **três abordagens**:

| Abordagem | Quando usar |
|-----------|-------------|
| **Template-driven** (`ngModel`) | Formulários muito simples |
| **Reactive Forms** (`FormGroup`, `FormControl`) | Código legado e formulários complexos já existentes; ecossistema maduro |
| **Signal Forms** (`@angular/forms/signals`) | **Recomendado para novos formulários**: modelo em signal, validação por *schema*, tipagem forte |

### 8.1 Signal Forms

O modelo do formulário é um **signal**; o campo reflete e atualiza esse modelo automaticamente.

```ts
import { Component, signal } from "@angular/core";
import { form, FormField, required, email, minLength, submit } from "@angular/forms/signals";

interface LoginData {
  email: string;
  senha: string;
}

@Component({
  selector: "app-login",
  imports: [FormField],
  templateUrl: "./login.html",
})
export class Login {
  private auth = inject(AuthService);

  // 1) Modelo
  loginModel = signal<LoginData>({ email: "", senha: "" });

  // 2) Form + regras de validação (schema)
  loginForm = form(this.loginModel, (path) => {
    required(path.email, { message: "E-mail é obrigatório" });
    email(path.email, { message: "Informe um e-mail válido" });

    required(path.senha, { message: "Senha é obrigatória" });
    minLength(path.senha, 8, { message: "Mínimo de 8 caracteres" });
  });

  // 3) Envio
  enviar(event: Event) {
    event.preventDefault();
    submit(this.loginForm, {
      action: async () => {
        await this.auth.login(this.loginModel());
      },
    });
  }
}
```

```html
<!-- login.html -->
<form (submit)="enviar($event)" novalidate>
  <label>
    E-mail
    <input type="email" [formField]="loginForm.email" />
  </label>
  @if (loginForm.email().touched() && loginForm.email().invalid()) {
    <ul class="erros">
      @for (erro of loginForm.email().errors(); track erro) { <li>{{ erro.message }}</li> }
    </ul>
  }

  <label>
    Senha
    <input type="password" [formField]="loginForm.senha" />
  </label>
  @if (loginForm.senha().touched() && loginForm.senha().invalid()) {
    <ul class="erros">
      @for (erro of loginForm.senha().errors(); track erro) { <li>{{ erro.message }}</li> }
    </ul>
  }

  <button type="submit" [disabled]="loginForm().invalid()">Entrar</button>
</form>
```

**Estado de cada campo (signals)**: `value()`, `valid()`, `invalid()`, `errors()`, `touched()`, `dirty()`, `pending()`, `disabled()`.

**Validadores prontos**: `required`, `email`, `min`, `max`, `minLength`, `maxLength`, `pattern`, `minDate`, `maxDate`.

**Validação customizada e entre campos**

```ts
import { validate } from "@angular/forms/signals";

cadastroForm = form(this.cadastroModel, (path) => {
  required(path.senha);
  minLength(path.senha, 8);
  required(path.confirmacao);

  // Regra entre campos: valueOf() lê outro campo
  validate(path.confirmacao, ({ value, valueOf }) =>
    value() === valueOf(path.senha)
      ? null
      : { kind: "senhasDiferentes", message: "As senhas não conferem" },
  );
});
```

**Validação assíncrona (servidor)**

```ts
import { validateHttp } from "@angular/forms/signals";

form(this.model, (path) => {
  validateHttp(path.usuario, {
    request: ({ value }) => `/api/usuarios/disponivel?nome=${value()}`,
    onSuccess: (resp: any) => (resp.ocupado ? { kind: "ocupado", message: "Usuário já existe" } : null),
    onError: () => ({ kind: "rede", message: "Não foi possível verificar" }),
  });
});
// loginForm.usuario().pending() indica que a verificação está em andamento
```

**Listas (arrays) e validação por item**

```ts
import { applyEach } from "@angular/forms/signals";

interface Item { nome: string; quantidade: number; }

pedidoForm = form(this.pedidoModel, (path) => {
  required(path.titulo);
  applyEach(path.itens, (item) => {
    required(item.nome, { message: "Informe o item" });
    min(item.quantidade, 1, { message: "Mínimo 1" });
  });
});
```

```html
@for (item of pedidoForm.itens; track $index) {
  <input [formField]="item.nome" />
  <input type="number" [formField]="item.quantidade" />
}
<button type="button" (click)="adicionarItem()">+ Item</button>
```

**Integração com Zod/Valibot (Standard Schema)**

```ts
import { validateStandardSchema } from "@angular/forms/signals";
import * as z from "zod";

const schema = z.object({ email: z.email(), senha: z.string().min(8) });

form(this.model, (path) => { validateStandardSchema(path, schema); });
```

**Controle customizado (componente como campo)**

```ts
import { FormValueControl } from "@angular/forms/signals";

@Component({
  selector: "app-estrelas",
  template: `
    @for (n of [1,2,3,4,5]; track n) {
      <button type="button" (click)="value.set(n)" [class.ativa]="n <= value()">★</button>
    }
  `,
})
export class Estrelas implements FormValueControl<number> {
  readonly value = model(0);      // o contrato do controle
}
```

```html
<app-estrelas [formField]="avaliacaoForm.nota" />
```

> Os Signal Forms evoluíram muito entre o v21 (experimental) e o v22 (estável). Consulte o guia oficial em `angular.dev/guide/forms/signals` para a API da sua versão (campos desabilitados/ocultos, `applyWhen`, metadados, testes e migração de Reactive Forms).

### 8.2 Reactive Forms (tipados)

Continuam muito usados em projetos existentes.

```ts
import { FormBuilder, ReactiveFormsModule, Validators } from "@angular/forms";

@Component({
  selector: "app-cadastro",
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form" (ngSubmit)="enviar()">
      <input formControlName="nome" />
      @if (nome.touched && nome.errors?.['required']) { <small>Nome obrigatório</small> }

      <input formControlName="email" type="email" />
      @if (form.controls.email.hasError('email')) { <small>E-mail inválido</small> }

      <div formGroupName="endereco">
        <input formControlName="cep" />
        <input formControlName="cidade" />
      </div>

      <button [disabled]="form.invalid">Salvar</button>
    </form>
  `,
})
export class Cadastro {
  private fb = inject(FormBuilder).nonNullable;   // evita null nos controles

  form = this.fb.group({
    nome: ["", [Validators.required, Validators.minLength(3)]],
    email: ["", [Validators.required, Validators.email]],
    endereco: this.fb.group({ cep: ["", Validators.pattern(/^\d{5}-?\d{3}$/)], cidade: [""] }),
  });

  get nome() { return this.form.controls.nome; }

  enviar() {
    if (this.form.invalid) { this.form.markAllAsTouched(); return; }
    console.log(this.form.getRawValue());   // tipado
  }
}
```

**Validador customizado e `FormArray`**

```ts
export function senhasIguais(): ValidatorFn {
  return (group: AbstractControl): ValidationErrors | null => {
    const s = group.get("senha")?.value;
    const c = group.get("confirmacao")?.value;
    return s === c ? null : { senhasDiferentes: true };
  };
}

form = this.fb.group({ senha: [""], confirmacao: [""] }, { validators: senhasIguais() });

// FormArray
telefones = this.fb.array([this.fb.control("")]);
adicionar() { this.telefones.push(this.fb.control("")); }
remover(i: number) { this.telefones.removeAt(i); }
```

```ts
// Reagindo a mudanças (observables)
this.form.controls.cep.valueChanges.pipe(
  debounceTime(400), filter((c) => c.length === 8),
  switchMap((cep) => this.viaCep.buscar(cep)),
  takeUntilDestroyed(),
).subscribe((end) => this.form.patchValue({ endereco: { cidade: end.localidade } }));
```

### 8.3 Template-driven (simples)

```ts
@Component({
  selector: "app-newsletter",
  imports: [FormsModule],
  template: `
    <form #f="ngForm" (ngSubmit)="assinar(f.value)">
      <input name="email" [(ngModel)]="email" required email #emailCtrl="ngModel" />
      @if (emailCtrl.invalid && emailCtrl.touched) { <small>E-mail inválido</small> }
      <button [disabled]="f.invalid">Assinar</button>
    </form>
  `,
})
export class Newsletter { email = ""; assinar(v: unknown) { /* ... */ } }
```

### 8.4 Boas práticas de formulários

- Mostre erros **após `touched`** (ou após tentativa de envio), não enquanto a pessoa ainda digita.
- Desabilite o botão **durante o envio** (evita envios duplicados).
- Valide **também no servidor**; a validação do cliente é conforto de UX.
- Rotule todos os campos (`<label>`), associe mensagens de erro com `aria-describedby`.
- Para novos formulários, **prefira Signal Forms**; para existentes, migre aos poucos.

### Exercícios do Módulo 8
1. Faça um formulário de cadastro com Signal Forms: nome, e-mail, senha e confirmação (validação entre campos).
2. Adicione verificação assíncrona de e-mail já cadastrado.
3. Crie um formulário de pedido com lista dinâmica de itens (`applyEach`).
4. Reimplemente um formulário simples com Reactive Forms tipados e compare as duas abordagens.

---

## Módulo 9 · Estado e arquitetura

### 9.1 Estado local, compartilhado e global

| Tipo | Onde guardar |
|------|--------------|
| **Local** (aberto/fechado, texto digitado) | `signal` no componente |
| **Compartilhado** entre poucos componentes | Serviço com signals, provido na rota/componente |
| **Global** (usuário logado, carrinho, tema) | Serviço `providedIn: 'root'` ou **NgRx SignalStore** |
| **Servidor** (dados da API) | `httpResource`/`resource`, ou bibliotecas de cache de requisições |
| **URL** (filtros, página, aba) | Query params do roteador |

### 9.2 Serviço de estado com signals

```ts
@Injectable({ providedIn: "root" })
export class CarrinhoService {
  private _itens = signal<ItemCarrinho[]>([]);

  readonly itens = this._itens.asReadonly();
  readonly quantidade = computed(() => this._itens().reduce((s, i) => s + i.qtd, 0));
  readonly total = computed(() => this._itens().reduce((s, i) => s + i.qtd * i.produto.preco, 0));
  readonly vazio = computed(() => this._itens().length === 0);

  adicionar(produto: Produto) {
    this._itens.update((lista) => {
      const existente = lista.find((i) => i.produto.id === produto.id);
      return existente
        ? lista.map((i) => (i === existente ? { ...i, qtd: i.qtd + 1 } : i))
        : [...lista, { produto, qtd: 1 }];
    });
  }

  remover(id: number) {
    this._itens.update((l) => l.filter((i) => i.produto.id !== id));
  }

  limpar() { this._itens.set([]); }
}
```

**Princípios**: o estado é **privado e mutável** só dentro do serviço; o mundo externo recebe **signals somente leitura** e **métodos** que expressam intenções (`adicionar`, `remover`).

### 9.3 NgRx SignalStore

Estrutura padronizada para estados mais complexos.

```bash
ng add @ngrx/signals
```

```ts
import { signalStore, withState, withComputed, withMethods, patchState } from "@ngrx/signals";
import { rxMethod } from "@ngrx/signals/rxjs-interop";

interface ProdutosState {
  itens: Produto[];
  carregando: boolean;
  filtro: string;
}

export const ProdutosStore = signalStore(
  { providedIn: "root" },
  withState<ProdutosState>({ itens: [], carregando: false, filtro: "" }),

  withComputed(({ itens, filtro }) => ({
    filtrados: computed(() => itens().filter((p) => p.nome.toLowerCase().includes(filtro().toLowerCase()))),
    total: computed(() => itens().length),
  })),

  withMethods((store, service = inject(ProdutoService)) => ({
    definirFiltro(filtro: string) { patchState(store, { filtro }); },

    carregar: rxMethod<void>(
      pipe(
        tap(() => patchState(store, { carregando: true })),
        switchMap(() => service.listar()),
        tap((page) => patchState(store, { itens: page.itens, carregando: false })),
      ),
    ),
  })),
);
```

```ts
export class ProdutoLista {
  store = inject(ProdutosStore);
  constructor() { this.store.carregar(); }
}
// template: store.filtrados(), store.carregando(), (input)="store.definirFiltro(...)"
```

> **NgRx Store clássico** (actions/reducers/effects/selectors) ainda é válido em grandes bases de código com requisitos de rastreabilidade (DevTools de tempo de viagem). Para a maioria das aplicações novas, serviços com signals ou SignalStore bastam.

### 9.4 Organização por *features*

```
src/app/
├── core/                         ← singleton: serviços globais, interceptors, guards, layout
│   ├── auth/
│   ├── http/
│   └── layout/
├── shared/                       ← componentes/pipes/diretivas reutilizáveis (sem regras de negócio)
│   ├── ui/                       ← botão, modal, tabela...
│   └── pipes/
├── features/
│   ├── produtos/
│   │   ├── data/                 ← serviços, store, modelos
│   │   ├── ui/                   ← componentes de apresentação
│   │   ├── pages/                ← componentes "container" (roteados)
│   │   └── produtos.routes.ts
│   ├── pedidos/
│   └── dashboard/
├── app.config.ts
├── app.routes.ts
└── app.ts
```

**Regras de dependência**: `features` podem usar `shared` e `core`; `shared` não conhece `features`; uma `feature` **não importa diretamente outra**. Ferramentas como **Nx** ou regras do ESLint (`@nx/enforce-module-boundaries`) ajudam a fazer cumprir isso.

### 9.5 Componentes *container* e de *apresentação*

```ts
// Apresentação: só inputs/outputs, sem serviços
@Component({
  selector: "app-produto-tabela",
  imports: [CurrencyPipe],
  template: `
    <table>
      @for (p of produtos(); track p.id) {
        <tr>
          <td>{{ p.nome }}</td>
          <td>{{ p.preco | currency:'BRL' }}</td>
          <td><button (click)="remover.emit(p.id)">Remover</button></td>
        </tr>
      }
    </table>
  `,
})
export class ProdutoTabela {
  produtos = input.required<Produto[]>();
  remover = output<number>();
}

// Container: conhece serviços/store e orquestra
@Component({
  selector: "app-produtos-page",
  imports: [ProdutoTabela],
  template: `<app-produto-tabela [produtos]="store.filtrados()" (remover)="aoRemover($event)" />`,
})
export class ProdutosPage {
  store = inject(ProdutosStore);
  aoRemover(id: number) { /* ... */ }
}
```

### 9.6 Tratamento global de erros

```ts
@Injectable()
export class GlobalErrorHandler implements ErrorHandler {
  private toast = inject(ToastService);

  handleError(error: unknown): void {
    console.error(error);                 // envie também para Sentry/Datadog
    this.toast.erro("Algo deu errado. Tente novamente.");
  }
}

providers: [{ provide: ErrorHandler, useClass: GlobalErrorHandler }]
```

### 9.7 Princípios gerais de arquitetura

- **Componentes pequenos** e focados; a lógica de negócio vai para serviços/stores.
- **Imutabilidade** nos dados que fluem pela aplicação.
- **Tipos nas fronteiras** (DTOs da API, formulários, rotas).
- **Lazy loading** por feature.
- **Não duplique estado**: derive com `computed`.
- Evite `any`; habilite `strict` e `strictTemplates`.

### Exercícios do Módulo 9
1. Implemente `CarrinhoService` e exiba o total no cabeçalho de qualquer página.
2. Reescreva a listagem de produtos com um `SignalStore` (carregar, filtrar, remover).
3. Reorganize um projeto existente em `core/`, `shared/` e `features/`.

---

## Módulo 10 · Testes

### 10.1 Ferramentas

| Camada | Ferramenta |
|--------|-----------|
| Unitários e de componente | **Vitest** (padrão em projetos novos desde o v21; Karma/Jasmine continuam suportados) + `TestBed` |
| HTTP | `HttpTestingController` |
| E2E | Playwright (ou Cypress) |
| Acessibilidade | axe-core, Lighthouse |

```bash
ng test                 # modo watch
ng test --no-watch      # uma execução (CI)
```

> Nos exemplos abaixo, `describe`, `it`, `expect` e `vi` estão disponíveis como globais no projeto gerado pelo CLI.

### 10.2 Teste de serviço puro

```ts
describe("CarrinhoService", () => {
  let service: CarrinhoService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(CarrinhoService);
  });

  it("calcula o total e a quantidade", () => {
    service.adicionar({ id: 1, nome: "Mouse", preco: 50 });
    service.adicionar({ id: 1, nome: "Mouse", preco: 50 });
    service.adicionar({ id: 2, nome: "Teclado", preco: 150 });

    expect(service.quantidade()).toBe(3);
    expect(service.total()).toBe(250);
  });

  it("remove um item", () => {
    service.adicionar({ id: 1, nome: "Mouse", preco: 50 });
    service.remover(1);
    expect(service.vazio()).toBe(true);
  });
});
```

### 10.3 Teste de componente

```ts
describe("ProdutoCard", () => {
  let fixture: ComponentFixture<ProdutoCard>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({ imports: [ProdutoCard] }).compileComponents();
    fixture = TestBed.createComponent(ProdutoCard);
    fixture.componentRef.setInput("nome", "Mouse");     // define inputs (inclusive signal inputs)
    fixture.componentRef.setInput("id", 7);
    await fixture.whenStable();                          // aguarda a renderização (zoneless)
  });

  it("exibe o nome do produto", () => {
    expect(fixture.nativeElement.textContent).toContain("Mouse");
  });

  it("emite o id ao clicar em Comprar", () => {
    const emitido = vi.fn();
    fixture.componentInstance.comprar.subscribe(emitido);

    fixture.nativeElement.querySelector("button").click();

    expect(emitido).toHaveBeenCalledWith(7);
  });
});
```

### 10.4 Componente com dependências (mock)

```ts
describe("ProdutosPage", () => {
  const servicoFalso = { listar: () => of({ itens: [{ id: 1, nome: "Mouse", preco: 50 }], total: 1 }) };

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [ProdutosPage],
      providers: [{ provide: ProdutoService, useValue: servicoFalso }],
    }).compileComponents();
  });

  it("lista os produtos retornados", async () => {
    const fixture = TestBed.createComponent(ProdutosPage);
    await fixture.whenStable();
    expect(fixture.nativeElement.textContent).toContain("Mouse");
  });
});
```

### 10.5 Teste de HTTP

```ts
describe("ProdutoService", () => {
  let service: ProdutoService;
  let http: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [provideHttpClient(), provideHttpClientTesting()],
    });
    service = TestBed.inject(ProdutoService);
    http = TestBed.inject(HttpTestingController);
  });

  afterEach(() => http.verify());       // garante que não há requisições pendentes inesperadas

  it("busca um produto por id", () => {
    let resultado: Produto | undefined;
    service.buscar(5).subscribe((p) => (resultado = p));

    const req = http.expectOne("/api/produtos/5");
    expect(req.request.method).toBe("GET");
    req.flush({ id: 5, nome: "Monitor", preco: 900 });

    expect(resultado?.nome).toBe("Monitor");
  });

  it("propaga erro 500", () => {
    let status = 0;
    service.buscar(5).subscribe({ error: (e) => (status = e.status) });

    http.expectOne("/api/produtos/5").flush("erro", { status: 500, statusText: "Server Error" });

    expect(status).toBe(500);
  });
});
```

### 10.6 Testando guards e interceptors

```ts
describe("authGuard", () => {
  const executar: CanActivateFn = (...args) => TestBed.runInInjectionContext(() => authGuard(...args));

  it("redireciona anônimos para /login", () => {
    TestBed.configureTestingModule({
      providers: [
        provideRouter([]),
        { provide: AuthService, useValue: { autenticado: () => false } },
      ],
    });

    const resultado = executar({} as any, { url: "/dashboard" } as any);

    expect(resultado).toBeInstanceOf(UrlTree);
    expect(TestBed.inject(Router).serializeUrl(resultado as UrlTree)).toContain("/login");
  });
});
```

### 10.7 Testes de formulário (Signal Forms)

Como o estado é um signal, o teste é direto: altere o modelo e leia o estado dos campos.

```ts
it("invalida e-mail mal formado", async () => {
  const fixture = TestBed.createComponent(Login);
  fixture.componentInstance.loginModel.set({ email: "abc", senha: "12345678" });
  await fixture.whenStable();

  expect(fixture.componentInstance.loginForm.email().invalid()).toBe(true);
  expect(fixture.componentInstance.loginForm.senha().valid()).toBe(true);
});
```

### 10.8 Component Harnesses (Material/CDK)

Harnesses interagem com componentes pela **API pública**, sem depender do DOM interno.

```ts
const loader = TestbedHarnessEnvironment.loader(fixture);
const botao = await loader.getHarness(MatButtonHarness.with({ text: "Salvar" }));
await botao.click();
```

### 10.9 E2E com Playwright

```bash
npm init playwright@latest
```

```ts
// e2e/login.spec.ts
import { test, expect } from "@playwright/test";

test("usuário faz login e vê o painel", async ({ page }) => {
  await page.goto("/login");
  await page.getByLabel("E-mail").fill("ana@exemplo.com");
  await page.getByLabel("Senha").fill("senha-segura-123");
  await page.getByRole("button", { name: "Entrar" }).click();

  await expect(page).toHaveURL(/dashboard/);
  await expect(page.getByRole("heading", { name: /painel/i })).toBeVisible();
});

test("rota protegida redireciona anônimos", async ({ page }) => {
  await page.goto("/dashboard");
  await expect(page).toHaveURL(/login/);
});
```

```ts
// playwright.config.ts
export default defineConfig({
  testDir: "./e2e",
  use: { baseURL: "http://localhost:4200" },
  webServer: { command: "npm start", url: "http://localhost:4200", reuseExistingServer: !process.env.CI },
});
```

### 10.10 Boas práticas

- Teste **comportamento** (o que o usuário vê e faz), não detalhes internos.
- Prefira seletores acessíveis (`getByRole`, `getByLabel`) a classes CSS.
- Mocke apenas as **fronteiras** (HTTP, relógio, serviços externos).
- Mantenha testes rápidos e determinísticos; use `vi.useFakeTimers()` para tempo.
- No CI, rode lint, testes, `build` e E2E.

### Exercícios do Módulo 10
1. Teste `CarrinhoService` cobrindo adicionar, remover, total e itens repetidos.
2. Teste um serviço HTTP com `HttpTestingController` (sucesso e erro).
3. Escreva um teste E2E do fluxo "adicionar ao carrinho → finalizar compra".

---

## Módulo 11 · Recursos avançados

### 11.1 `@defer`: carregamento adiado de partes do template

Divide o código em *chunks* e só baixa/renderiza quando a condição ocorre.

```html
@defer (on viewport; prefetch on idle) {
  <app-grafico-vendas [dados]="dados()" />
} @placeholder (minimum 300ms) {
  <div class="skeleton">Gráfico</div>
} @loading (after 100ms; minimum 500ms) {
  <app-spinner />
} @error {
  <p>Não foi possível carregar o gráfico.</p>
}
```

| Gatilho | Quando |
|---------|--------|
| `on idle` | Navegador ocioso (padrão) |
| `on viewport` | Elemento entra na tela |
| `on interaction` / `on hover` | Clique ou mouse sobre o *placeholder* |
| `on timer(5s)` | Após um tempo |
| `on immediate` | Imediatamente (após a renderização inicial) |
| `when condicao()` | Quando uma expressão vira `true` |
| `prefetch on ...` | Baixa antes, renderiza depois |

> Use `@defer` para componentes **pesados e abaixo da dobra** (gráficos, mapas, editores, comentários). Evite no conteúdo principal visível (prejudica o LCP).

### 11.2 Renderização no servidor (SSR), prerender e hidratação

```bash
ng add @angular/ssr          # ou: ng new meu-app --ssr
```

```ts
// app.config.ts
import { provideClientHydration, withEventReplay, withIncrementalHydration } from "@angular/platform-browser";

providers: [
  provideClientHydration(withEventReplay(), withIncrementalHydration()),
]
```

```ts
// app.routes.server.ts: modo de renderização por rota
import { RenderMode, ServerRoute } from "@angular/ssr";

export const serverRoutes: ServerRoute[] = [
  { path: "", renderMode: RenderMode.Prerender },              // gerada no build (SSG)
  { path: "blog/:slug", renderMode: RenderMode.Prerender,
    async getPrerenderParams() { return (await listarSlugs()).map((slug) => ({ slug })); } },
  { path: "dashboard", renderMode: RenderMode.Client },        // somente no navegador (CSR)
  { path: "**", renderMode: RenderMode.Server },               // a cada requisição (SSR)
];
```

| Modo | Quando renderiza | Uso |
|------|------------------|-----|
| `Prerender` | No build | Páginas estáticas, blog, marketing |
| `Server` | A cada requisição | Conteúdo dinâmico e SEO |
| `Client` | No navegador | Áreas autenticadas, sem SEO |

**Hidratação incremental**: o HTML do servidor já aparece, e o JavaScript de cada trecho só é baixado/ativado quando necessário.

```html
@defer (hydrate on viewport) {
  <app-comentarios />
}
```

**Cuidados no SSR**
- Não acesse `window`, `document` ou `localStorage` direto. Use `afterNextRender` ou `isPlatformBrowser`.
- Use `withFetch()` no `HttpClient`.
- Use `TransferState`/cache de HTTP (já integrado na hidratação) para evitar buscar os mesmos dados duas vezes.

```ts
import { PLATFORM_ID } from "@angular/core";
import { isPlatformBrowser } from "@angular/common";

const ehNavegador = isPlatformBrowser(inject(PLATFORM_ID));
```

### 11.3 Componentes dinâmicos

```ts
import { NgComponentOutlet } from "@angular/common";

@Component({
  selector: "app-widget",
  imports: [NgComponentOutlet],
  template: `<ng-container *ngComponentOutlet="componente(); inputs: { titulo: 'Vendas' }" />`,
})
export class Widget {
  componente = signal<Type<unknown>>(GraficoBarras);
}

// Criação imperativa
const ref = viewContainerRef.createComponent(Alerta);
ref.setInput("mensagem", "Olá");
ref.onDestroy(() => console.log("removido"));
```

### 11.4 Templates e `ngTemplateOutlet` (componentes flexíveis)

```ts
@Component({
  selector: "app-lista",
  imports: [NgTemplateOutlet],
  template: `
    @for (item of itens(); track item.id) {
      <ng-container [ngTemplateOutlet]="modelo() ?? padrao" [ngTemplateOutletContext]="{ $implicit: item }" />
    }
    <ng-template #padrao let-item>{{ item.nome }}</ng-template>
  `,
})
export class Lista {
  itens = input.required<Item[]>();
  modelo = contentChild(TemplateRef);
}
```

```html
<app-lista [itens]="produtos()">
  <ng-template let-p><strong>{{ p.nome }}</strong>: {{ p.preco | currency:'BRL' }}</ng-template>
</app-lista>
```

### 11.5 Animações

A partir do Angular 20.2, a forma recomendada usa as sintaxes `animate.enter` e `animate.leave` com **CSS**; o pacote `@angular/animations` está em depreciação.

```html
@if (aberto()) {
  <div animate.enter="entrar" animate.leave="sair">Conteúdo</div>
}
```

```css
.entrar { animation: fade-in 250ms ease-out; }
.sair   { animation: fade-out 200ms ease-in; }
@keyframes fade-in  { from { opacity: 0; transform: translateY(-8px); } }
@keyframes fade-out { to   { opacity: 0; transform: translateY(-8px); } }
```

### 11.6 Internacionalização (i18n)

```bash
ng add @angular/localize
```

```html
<h1 i18n="@@titulo-home">Bem-vindo</h1>
<p i18n>Você tem {count, plural, =0 {nenhuma mensagem} =1 {uma mensagem} other {{{count}} mensagens}}</p>
```

```bash
ng extract-i18n                 # gera messages.xlf
ng build --localize             # um build por idioma
```

Alternativas com troca de idioma em tempo de execução: **Transloco** ou **ngx-translate**.

### 11.7 Angular Material, CDK e Angular Aria

```bash
ng add @angular/material
```

```ts
import { MatButtonModule } from "@angular/material/button";
import { MatTableModule } from "@angular/material/table";
import { MatDialog } from "@angular/material/dialog";
```

- **Material**: componentes prontos com Material Design e temas.
- **CDK**: comportamentos sem estilo (overlay, drag and drop, virtual scroll, a11y, tabelas).
- **Angular Aria**: primitivos de acessibilidade *headless* (estável a partir do v22), para construir seu próprio *design system* acessível.

```html
<!-- Virtual scroll para listas enormes -->
<cdk-virtual-scroll-viewport itemSize="48" class="viewport">
  <div *cdkVirtualFor="let item of itens">{{ item.nome }}</div>
</cdk-virtual-scroll-viewport>
```

### 11.8 Web Components (Angular Elements)

```ts
import { createCustomElement } from "@angular/elements";

const app = await createApplication({ providers: [] });
customElements.define("meu-widget", createCustomElement(MeuWidget, { injector: app.injector }));
// <meu-widget titulo="Olá"></meu-widget> em qualquer página HTML
```

### 11.9 Micro frontends

Para organizações com vários times, use **Native Federation** ou **Module Federation** para carregar aplicações Angular independentes em tempo de execução. Considere custo operacional (versionamento compartilhado, desempenho) antes de adotar. Um monorepo com **Nx** e *module boundaries* costuma resolver sem esse custo.

### 11.10 PWA e Service Workers

```bash
ng add @angular/pwa
```

Habilita cache offline, ícones e *manifest*. O `SwUpdate` notifica quando há nova versão.

### 11.11 Segurança

- O Angular **sanitiza** HTML, URLs e estilos por padrão. Evite `bypassSecurityTrustHtml` e `innerHTML` com conteúdo não confiável.
- **XSRF**: o `HttpClient` lê o cookie `XSRF-TOKEN` e envia `X-XSRF-TOKEN` (configure com `withXsrfConfiguration`).
- **Tokens JWT**: prefira **cookies `HttpOnly` + `SameSite`** definidos pelo servidor a `localStorage` (vulnerável a XSS).
- Configure **CSP** (Content-Security-Policy) e cabeçalhos de segurança no servidor/CDN.
- **Guards não são segurança**: sempre autorize no backend.
- Mantenha `@angular/*` **atualizado**: o v22 incluiu correções importantes contra SSRF no SSR.
- Audite dependências (`npm audit`, Dependabot).

### 11.12 Acessibilidade (a11y)

```html
<button type="button" aria-label="Fechar diálogo" (click)="fechar()">✕</button>

<div role="alert" aria-live="polite">{{ mensagemErro() }}</div>

<input id="email" [formField]="form.email" [attr.aria-invalid]="form.email().invalid()"
       aria-describedby="email-erro" />
<p id="email-erro">...</p>
```

- Use elementos semânticos (`button`, `nav`, `main`, `label`).
- Garanta navegação por teclado e **gerenciamento de foco** (modais, rotas).
- Contraste adequado; teste com leitores de tela e axe.

### 11.13 Ferramentas de IA no Angular

O Angular CLI traz um **servidor MCP** com ferramentas (modernização de código, migração para OnPush/zoneless, *tutor*) que assistentes de código podem usar, além de *Agent Skills* com boas práticas. Veja `angular.dev/ai`.

### Exercícios do Módulo 11
1. Adicione um gráfico pesado com `@defer (on viewport)`, com *placeholder* e *loading*.
2. Ative SSR + prerender e meça o LCP antes e depois.
3. Construa um `Tabs` com `contentChildren` e `ng-template`.
4. Rode `npm audit` e o Lighthouse (Acessibilidade) e corrija os problemas encontrados.

---

## Módulo 12 · Performance e otimização

### 12.1 Métricas

| Métrica | Mede | Meta |
|---------|------|------|
| **LCP** | Carregamento do maior elemento | ≤ 2,5 s |
| **INP** | Responsividade às interações | ≤ 200 ms |
| **CLS** | Estabilidade visual | ≤ 0,1 |

### 12.2 Checklist de otimização

**Detecção de mudanças**
- Use **signals + OnPush + zoneless**: atualizações fiéis e mínimas.
- Evite cálculos pesados no template: use `computed`.
- Não chame funções no template que façam trabalho caro (elas rodam a cada verificação); use `computed` ou pipes puros.

**Listas**
- `@for` com `track` por identificador único.
- `cdk-virtual-scroll-viewport` para milhares de itens.
- Paginação ou *infinite scroll* no servidor.

**Carregamento**
- **Lazy loading** em todas as rotas de features.
- `@defer` para trechos pesados.
- `withPreloading(...)` para rotas prováveis.
- SSR/prerender para páginas públicas, com hidratação e *event replay*.

**Imagens**

```ts
import { NgOptimizedImage } from "@angular/common";

@Component({
  imports: [NgOptimizedImage],
  template: `
    <img ngSrc="/capa.jpg" width="1200" height="600" priority alt="Capa" />
    <img ngSrc="/produto.jpg" width="400" height="300" alt="Produto" />    <!-- lazy por padrão -->
  `,
})
export class Home {}
```

**Bundle**
- Importe só o que usa (evite `import * as _ from "lodash"`; use `lodash-es` com imports nomeados ou funções nativas).
- Controle com **budgets** no `angular.json`:

```json
"budgets": [
  { "type": "initial", "maximumWarning": "500kB", "maximumError": "1MB" },
  { "type": "anyComponentStyle", "maximumWarning": "4kB", "maximumError": "8kB" }
]
```

```bash
ng build --stats-json          # gera stats.json para analisar no esbuild bundle analyzer
```

**Rede**
- Compressão (Brotli/gzip) e cache longo para arquivos com *hash* no nome.
- CDN para os estáticos.
- `HttpClient` com cache via interceptor ou `shareReplay` para dados estáveis.

### 12.3 Diagnóstico

- **Angular DevTools**: *Profiler* mostra quais componentes foram verificados e quanto custou.
- **Chrome DevTools → Performance** para INP e tarefas longas.
- **Lighthouse** / PageSpeed Insights.
- **RUM** (dados reais): Sentry, Datadog, Vercel/Cloudflare analytics.

### Exercícios do Módulo 12
1. Meça com o Profiler uma tela com 1.000 itens e otimize com `track` + virtual scroll.
2. Troque todas as `<img>` por `NgOptimizedImage` e compare o LCP.
3. Gere o `stats.json` e identifique as 3 maiores dependências.

---

## Módulo 13 · Build, deploy e produção

### 13.1 Configuração por ambiente

O Angular não gera mais `environment.ts` por padrão. Prefira **configuração em tempo de execução**, para construir **uma única imagem** e promovê-la entre ambientes.

```json
// public/config.json (substituído no deploy)
{ "apiUrl": "https://api.exemplo.com", "sentryDsn": "" }
```

```ts
@Injectable({ providedIn: "root" })
export class ConfigService {
  private _config = signal<AppConfig | null>(null);
  readonly config = this._config.asReadonly();

  async carregar() {
    const resp = await fetch("/config.json", { cache: "no-store" });
    this._config.set(await resp.json());
  }
}

// app.config.ts
provideAppInitializer(() => inject(ConfigService).carregar()),
```

Para valores fixos em build, use `ng generate environments` e `fileReplacements` no `angular.json`.

### 13.2 Build de produção

```bash
ng build                      # produção por padrão → dist/meu-app/browser
```

Saída com *hash* nos nomes (`main-ABC123.js`) para cache de longa duração.

### 13.3 Docker com Nginx (SPA)

```dockerfile
# Etapa 1: build
FROM node:22-alpine AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npx ng build

# Etapa 2: servir com Nginx
FROM nginx:1.27-alpine
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist/meu-app/browser /usr/share/nginx/html
EXPOSE 80
```

```nginx
# nginx.conf
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html;

  # Fallback da SPA: o roteador do Angular cuida da URL
  location / {
    try_files $uri $uri/ /index.html;
  }

  # Arquivos com hash: cache longo
  location ~* \.(?:js|css|woff2?|png|jpe?g|svg|webp|avif)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
  }

  # Nunca cachear o index.html nem o config.json
  location ~* (index\.html|config\.json)$ {
    add_header Cache-Control "no-store";
  }

  gzip on;
  gzip_types text/css application/javascript application/json image/svg+xml;

  add_header X-Content-Type-Options nosniff;
  add_header X-Frame-Options DENY;
  add_header Referrer-Policy strict-origin-when-cross-origin;
}
```

### 13.4 Docker com SSR (Node)

```dockerfile
FROM node:22-alpine AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npx ng build

FROM node:22-alpine
WORKDIR /app
ENV NODE_ENV=production PORT=4000
COPY --from=build /app/dist/meu-app ./dist/meu-app
COPY package.json package-lock.json ./
RUN npm ci --omit=dev
USER node
EXPOSE 4000
CMD ["node", "dist/meu-app/server/server.mjs"]
```

> Ao usar SSR, configure a lista de **hosts permitidos** do servidor (proteção contra manipulação do cabeçalho `Host`/SSRF) conforme o guia de segurança do SSR da sua versão.

### 13.5 Proxy de desenvolvimento para a API

```json
// proxy.conf.json
{ "/api": { "target": "http://localhost:8080", "secure": false, "changeOrigin": true } }
```

```bash
ng serve --proxy-config proxy.conf.json
```

### 13.6 CI/CD com GitHub Actions

```yaml
name: CI
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm
      - run: npm ci
      - run: npx ng lint            # após: ng add angular-eslint
      - run: npx ng test --no-watch
      - run: npx ng build
      - run: npx playwright install --with-deps chromium
      - run: npx playwright test
      - uses: actions/upload-artifact@v4
        with:
          name: dist
          path: dist/
```

### 13.7 Atualizações de versão

O Angular lança **uma versão principal a cada 6 meses**, com suporte ativo por 6 meses e LTS por mais 12. Atualize **uma versão principal por vez**:

```bash
ng update                                   # lista o que pode ser atualizado
ng update @angular/core@22 @angular/cli@22  # execute as migrações automáticas
```

Consulte o **Angular Update Guide** em `angular.dev/update-guide` para passos específicos entre versões.

### 13.8 Observabilidade

- **Erros**: Sentry (ou similar) integrado ao `ErrorHandler`.
- **Desempenho real**: Core Web Vitals via biblioteca `web-vitals` enviados à sua telemetria.
- **Logs de API**: *correlation id* propagado por interceptor.

```ts
export const correlationInterceptor: HttpInterceptorFn = (req, next) =>
  next(req.clone({ setHeaders: { "X-Correlation-Id": crypto.randomUUID() } }));
```

### 13.9 Checklist de produção

- [ ] Angular e dependências atualizados; `npm audit` limpo.
- [ ] `strict`/`strictTemplates` ativos; sem `any` desnecessário.
- [ ] Lazy loading por feature; *budgets* configurados.
- [ ] Configuração em runtime (sem segredos no front-end: tudo que está no bundle é público).
- [ ] Autorização no backend; guards só para UX.
- [ ] Cookies `HttpOnly`, CSP e cabeçalhos de segurança.
- [ ] Tratamento global de erros e telemetria.
- [ ] Acessibilidade verificada (Lighthouse/axe, teclado, leitor de tela).
- [ ] `index.html` sem cache; arquivos com *hash* com cache longo.
- [ ] Testes (unitários, integração e E2E) no CI.
- [ ] Estratégia de *rollback* e *feature flags*.

---

## Anexo A · Migração de código legado

Projetos mais antigos usam `NgModule`, `*ngIf`/`*ngFor`, `@Input()`/`@Output()`, injeção por construtor e `zone.js`. A migração pode ser **gradual**, porque tudo isso continua funcionando. O CLI traz *schematics* para automatizá-la:

```bash
ng generate @angular/core:standalone              # NgModules → componentes standalone
ng generate @angular/core:control-flow            # *ngIf/*ngFor → @if/@for
ng generate @angular/core:inject                  # construtor → inject()
ng generate @angular/core:signal-input-migration  # @Input → input()
ng generate @angular/core:output-migration        # @Output → output()
ng generate @angular/core:signal-queries-migration # @ViewChild → viewChild()
ng generate @angular/core:route-lazy-loading      # rotas → loadComponent
```

| Legado | Moderno |
|--------|---------|
| `NgModule`, `declarations` | Componentes standalone com `imports` |
| `*ngIf`, `*ngFor`, `*ngSwitch` | `@if`, `@for`, `@switch` |
| `@Input()`, `@Output()`, `@ViewChild()` | `input()`, `output()`, `viewChild()` |
| `constructor(private s: Servico)` | `private s = inject(Servico)` |
| `@HostBinding`, `@HostListener` | Objeto `host` |
| `BehaviorSubject` para estado | `signal` / `computed` |
| `async` pipe em toda parte | `toSignal` / `httpResource` |
| Guards/interceptors de classe | Funções (`CanActivateFn`, `HttpInterceptorFn`) |
| Reactive Forms | Signal Forms (novos formulários) |
| `zone.js` + `Default` | Zoneless + `OnPush` |
| `@angular/animations` | `animate.enter` / `animate.leave` com CSS |
| Karma + Jasmine | Vitest |

**Ordem sugerida**: (1) atualize para a última versão suportada, uma principal por vez; (2) standalone; (3) control flow; (4) `inject()`; (5) signal inputs/outputs/queries; (6) OnPush em todos os componentes e, por fim, zoneless; (7) novos formulários em Signal Forms; (8) testes em Vitest.

---

## Plano de estudos sugerido

| Etapa | Conteúdo | Duração sugerida |
|-------|----------|------------------|
| 1 | Módulo 1: TypeScript | 2 semanas |
| 2 | Módulos 2 e 3: Primeiros passos, componentes e templates | 3 semanas |
| 3 | Módulo 4: Signals e reatividade | 2 semanas |
| 4 | Módulo 5: Diretivas, pipes e DI | 1 semana |
| 5 | Módulo 6: Roteamento | 2 semanas |
| 6 | Módulo 7: HttpClient e RxJS | 3 semanas |
| 7 | Módulo 8: Formulários | 2 semanas |
| 8 | Módulo 9: Estado e arquitetura | 2 semanas |
| 9 | Módulo 10: Testes | 2 semanas |
| 10 | Módulos 11 e 12: Avançado e performance | 3 semanas |
| 11 | Módulo 13: Build, deploy e produção | 2 semanas |

### Projeto final sugerido: **Plataforma de gestão de pedidos (backoffice + vitrine)**

Requisitos para aplicar tudo o que foi aprendido:

1. **Vitrine pública**: catálogo com busca (debounce + `switchMap`), filtros nos *query params*, página de produto, SSR/prerender e `NgOptimizedImage`.
2. **Autenticação**: login com Signal Forms, interceptor de token, `authGuard`, `canMatch` por papel e tratamento de 401.
3. **Carrinho e checkout**: `CarrinhoService` com signals, formulário de pedido com lista dinâmica (`applyEach`) e validação assíncrona.
4. **Backoffice**: CRUD de produtos e pedidos com tabelas (Material/CDK), paginação e *virtual scroll*.
5. **Dashboard**: gráficos em `@defer (on viewport)` e dados com `httpResource`.
6. **Estado**: um módulo gerenciado por **NgRx SignalStore**.
7. **Arquitetura**: organização `core/shared/features`, *lazy loading* e regras de dependência verificadas por lint.
8. **Qualidade**: testes de serviços, componentes, HTTP, guards e E2E (Playwright), com acessibilidade verificada.
9. **Operação**: Docker + Nginx, configuração em runtime, CI no GitHub Actions e monitoramento de erros.
10. **Documentação**: README com decisões de arquitetura (ADRs).

---

## Referências recomendadas

- **Documentação oficial**: https://angular.dev (guias, tutoriais e *playground*) · https://rxjs.dev · https://www.typescriptlang.org/docs
- **Tutorial interativo oficial**: https://angular.dev/tutorials
- **Guia de estilo oficial**: https://angular.dev/style-guide
- **Guia de atualização**: https://angular.dev/update-guide
- **Blog do Angular**: https://blog.angular.dev (notas de cada versão)
- **NgRx**: https://ngrx.io · **Angular Material/CDK**: https://material.angular.dev
- **Segurança**: https://angular.dev/best-practices/security e OWASP Top 10
- **Performance**: https://web.dev (Core Web Vitals)
- **Prática**: reproduza aplicações reais (e-commerce, painel administrativo, rede social simples) e contribua com projetos open source.

> **Aviso sobre versões**: o Angular evoluiu muito entre as versões 14 e 22 (standalone, signals, control flow, zoneless, Signal Forms). Ao seguir um tutorial, confira **a versão** utilizada. Se ele usa `NgModule`, `*ngIf` e `@Input()`, é o estilo anterior, que ainda funciona, mas não é mais o recomendado. Consulte sempre a documentação em `angular.dev` para APIs marcadas como *developer preview*.
>
> Dica final: **construa, quebre e refatore**. O Angular recompensa quem pratica a arquitetura em projetos reais.