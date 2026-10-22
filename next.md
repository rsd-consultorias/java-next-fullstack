# Treinamento Next.js: do Básico ao Avançado

> Material de apoio para um programa de treinamento completo. Os exemplos usam **Next.js 16**, **React 19** e **TypeScript**, com o **App Router**. Onde o comportamento mudou em relação ao Next.js 15, há uma nota indicando.

## Sumário

**Parte 1: Pré-requisitos**

1. [JavaScript moderno e TypeScript](#módulo-1--javascript-moderno-e-typescript)
2. [React essencial](#módulo-2--react-essencial)

**Parte 2: Next.js**

3. [Primeiros passos](#módulo-3--primeiros-passos-com-nextjs)
4. [Roteamento (App Router)](#módulo-4--roteamento-app-router)
5. [Server Components e Client Components](#módulo-5--server-components-e-client-components)
6. [Busca de dados, renderização e streaming](#módulo-6--busca-de-dados-renderização-e-streaming)
7. [Server Actions e formulários](#módulo-7--server-actions-e-formulários)
8. [Route Handlers (APIs)](#módulo-8--route-handlers-apis)
9. [Estilização, imagens, fontes e SEO](#módulo-9--estilização-imagens-fontes-e-seo)
10. [Banco de dados e autenticação](#módulo-10--banco-de-dados-e-autenticação)
11. [Cache e Cache Components](#módulo-11--cache-e-cache-components)
12. [Recursos avançados](#módulo-12--recursos-avançados)
13. [Performance e otimização](#módulo-13--performance-e-otimização)
14. [Testes](#módulo-14--testes)
15. [Deploy e produção](#módulo-15--deploy-e-produção)
16. [Anexo: Pages Router (legado)](#anexo-a--pages-router-legado)
17. [Plano de estudos e projeto final](#plano-de-estudos-sugerido)

---

# PARTE 1: PRÉ-REQUISITOS

Next.js é um framework **sobre o React**, que por sua vez é uma biblioteca **de JavaScript**. Quem domina esses dois módulos aprende o restante com muito mais facilidade. Se você já conhece os dois, pule para o [Módulo 3](#módulo-3--primeiros-passos-com-nextjs).

## Módulo 1 · JavaScript moderno e TypeScript

### 1.1 Variáveis, funções e módulos

```js
// const para valores que não são reatribuídos; let quando precisa reatribuir. Evite var.
const nome = "Ana";
let contador = 0;

// Template literals
const msg = `Olá, ${nome}! Contador: ${contador}`;

// Funções
function somar(a, b) { return a + b; }
const subtrair = (a, b) => a - b;                 // arrow function
const dobro = (n) => n * 2;                       // retorno implícito

// Parâmetros padrão, rest e spread
function saudar(nome = "visitante", ...extras) { return `Oi, ${nome}`; }
const a = [1, 2];
const b = [...a, 3];                              // [1, 2, 3]
const pessoa = { nome: "Ana", idade: 30 };
const copia = { ...pessoa, idade: 31 };           // copia com alteração (imutável)

// Desestruturação
const { nome: n, idade } = pessoa;
const [primeiro, segundo] = [10, 20];

// Optional chaining e nullish coalescing
const cidade = usuario?.endereco?.cidade ?? "Não informada";

// Módulos (ES Modules)
// utils.js
export const PI = 3.14;
export default function log(x) { console.log(x); }
// outro arquivo
import log, { PI } from "./utils.js";
```

### 1.2 Arrays e objetos: métodos essenciais

```js
const produtos = [
  { id: 1, nome: "Mouse", preco: 50, categoria: "perifericos" },
  { id: 2, nome: "Teclado", preco: 150, categoria: "perifericos" },
  { id: 3, nome: "Monitor", preco: 900, categoria: "telas" },
];

produtos.map((p) => p.nome);                          // transforma
produtos.filter((p) => p.preco > 100);                // filtra
produtos.find((p) => p.id === 2);                     // primeiro que satisfaz
produtos.some((p) => p.preco > 500);                  // algum?
produtos.every((p) => p.preco > 10);                  // todos?
produtos.reduce((total, p) => total + p.preco, 0);    // acumula
[...produtos].sort((a, b) => a.preco - b.preco);      // copie antes de ordenar

Object.entries({ a: 1, b: 2 });                       // [["a",1],["b",2]]
Object.groupBy(produtos, (p) => p.categoria);         // agrupamento (ES2024)
structuredClone(produtos);                            // cópia profunda
```

### 1.3 Assincronia: Promises e async/await

```js
// Promise
fetch("https://api.exemplo.com/produtos")
  .then((res) => res.json())
  .then((dados) => console.log(dados))
  .catch((erro) => console.error(erro));

// async/await (preferível)
async function buscar() {
  try {
    const res = await fetch("https://api.exemplo.com/produtos");
    if (!res.ok) throw new Error(`HTTP ${res.status}`);   // fetch NÃO rejeita em 4xx/5xx
    return await res.json();
  } catch (e) {
    console.error(e);
    throw e;
  }
}

// Em paralelo (importante para performance)
const [usuarios, pedidos] = await Promise.all([buscarUsuarios(), buscarPedidos()]);

// Resultado individual sem falhar tudo
const resultados = await Promise.allSettled([a(), b()]);
```

### 1.4 TypeScript essencial

```ts
// Tipos básicos
let titulo: string = "Next.js";
let preco: number = 99.9;
let ativo: boolean = true;
let tags: string[] = ["react", "next"];
let par: [string, number] = ["idade", 30];        // tupla

// Interfaces e type aliases
interface Produto {
  id: number;
  nome: string;
  preco: number;
  descricao?: string;             // opcional
  readonly criadoEm: Date;        // somente leitura
}

type Status = "pendente" | "pago" | "cancelado";  // união de literais
type ProdutoResumo = Pick<Produto, "id" | "nome">;
type ProdutoSemId = Omit<Produto, "id">;
type ProdutoParcial = Partial<Produto>;

// Funções tipadas
function aplicarDesconto(p: Produto, percentual: number): number {
  return p.preco * (1 - percentual / 100);
}

// Generics
function primeiro<T>(lista: T[]): T | undefined {
  return lista[0];
}

async function buscarJson<T>(url: string): Promise<T> {
  const res = await fetch(url);
  return res.json() as Promise<T>;
}
const produtos = await buscarJson<Produto[]>("/api/produtos");

// Narrowing (estreitamento)
function formatar(valor: string | number) {
  return typeof valor === "string" ? valor.toUpperCase() : valor.toFixed(2);
}

// Uniões discriminadas
type Resultado =
  | { ok: true; dados: Produto }
  | { ok: false; erro: string };

function tratar(r: Resultado) {
  if (r.ok) console.log(r.dados.nome);
  else console.error(r.erro);
}

// satisfies: valida sem alargar o tipo
const rotas = { home: "/", blog: "/blog" } satisfies Record<string, string>;
```

### Exercícios do Módulo 1
1. Escreva uma função que agrupe produtos por categoria e some os preços de cada grupo.
2. Busque dois endpoints em paralelo e combine os resultados.
3. Modele com TypeScript um carrinho de compras usando uniões discriminadas para os estados `vazio`, `com-itens` e `finalizado`.

---

## Módulo 2 · React essencial

### 2.1 Componentes e JSX

Um componente é uma função que retorna JSX (descrição da interface).

```tsx
type SaudacaoProps = {
  nome: string;
  idade?: number;
};

export function Saudacao({ nome, idade }: SaudacaoProps) {
  return (
    <div className="card">
      <h2>Olá, {nome}!</h2>
      {idade !== undefined && <p>Você tem {idade} anos.</p>}
    </div>
  );
}

// Uso
<Saudacao nome="Ana" idade={30} />
```

Regras do JSX: retorne **um único elemento raiz** (ou um Fragment `<>...</>`), use `className` em vez de `class`, feche todas as tags e use `{}` para expressões JavaScript.

### 2.2 Props, children, listas e renderização condicional

```tsx
type CardProps = { titulo: string; children: React.ReactNode };

function Card({ titulo, children }: CardProps) {
  return (
    <section>
      <h3>{titulo}</h3>
      {children}
    </section>
  );
}

function ListaProdutos({ produtos }: { produtos: Produto[] }) {
  if (produtos.length === 0) return <p>Nenhum produto.</p>;

  return (
    <ul>
      {produtos.map((p) => (
        <li key={p.id}>   {/* key estável e única, nunca o índice se a lista muda */}
          {p.nome}: R$ {p.preco.toFixed(2)}
        </li>
      ))}
    </ul>
  );
}
```

### 2.3 Estado com `useState`

```tsx
"use client"; // veremos o motivo no Módulo 5

import { useState } from "react";

export function Contador() {
  const [valor, setValor] = useState(0);

  return (
    <div>
      <p>Valor: {valor}</p>
      <button onClick={() => setValor((v) => v + 1)}>+1</button>
      <button onClick={() => setValor(0)}>Zerar</button>
    </div>
  );
}
```

**Regras de ouro do estado**
- Nunca mude o estado diretamente (`lista.push(x)`). Crie um novo valor: `setLista([...lista, x])`.
- Use a forma funcional (`setValor(v => v + 1)`) quando o novo valor depende do anterior.
- Estado é *isolado por instância* do componente e *preservado entre renderizações*.

### 2.4 Formulários controlados

```tsx
"use client";
import { useState } from "react";

export function FormBusca({ aoBuscar }: { aoBuscar: (termo: string) => void }) {
  const [termo, setTermo] = useState("");

  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        aoBuscar(termo);
      }}
    >
      <input value={termo} onChange={(e) => setTermo(e.target.value)} placeholder="Buscar..." />
      <button type="submit">Buscar</button>
    </form>
  );
}
```

### 2.5 Efeitos com `useEffect`

Efeitos **sincronizam** o componente com algo externo (timers, eventos do navegador, APIs de terceiros).

```tsx
"use client";
import { useEffect, useState } from "react";

export function LarguraJanela() {
  const [largura, setLargura] = useState(0);

  useEffect(() => {
    const atualizar = () => setLargura(window.innerWidth);
    atualizar();
    window.addEventListener("resize", atualizar);
    return () => window.removeEventListener("resize", atualizar);  // cleanup
  }, []);                                                          // array de dependências

  return <p>Largura: {largura}px</p>;
}
```

> **Armadilha comum**: não use `useEffect` para buscar dados no Next.js nem para derivar estado de outro estado. Em Next.js, a busca de dados acontece **no servidor** (Módulo 6). Para cálculos derivados, calcule direto durante a renderização.

### 2.6 Outros hooks importantes

```tsx
"use client";
import { useRef, useMemo, useCallback, useContext, createContext, useReducer } from "react";

// useRef: referência mutável que não dispara renderização (ex.: elemento do DOM)
const inputRef = useRef<HTMLInputElement>(null);
inputRef.current?.focus();

// useMemo / useCallback: memoização (com o React Compiler, raramente necessário à mão)
const total = useMemo(() => itens.reduce((s, i) => s + i.preco, 0), [itens]);
const remover = useCallback((id: number) => { /* ... */ }, []);

// useReducer: estado complexo com ações
type Acao = { tipo: "add"; item: Item } | { tipo: "remove"; id: number };
function reducer(estado: Item[], acao: Acao): Item[] {
  switch (acao.tipo) {
    case "add": return [...estado, acao.item];
    case "remove": return estado.filter((i) => i.id !== acao.id);
  }
}
const [carrinho, dispatch] = useReducer(reducer, []);
dispatch({ tipo: "add", item: novoItem });
```

### 2.7 Context

```tsx
"use client";
import { createContext, useContext, useState } from "react";

type Tema = "claro" | "escuro";
const TemaContext = createContext<{ tema: Tema; alternar: () => void } | null>(null);

export function TemaProvider({ children }: { children: React.ReactNode }) {
  const [tema, setTema] = useState<Tema>("claro");
  const alternar = () => setTema((t) => (t === "claro" ? "escuro" : "claro"));

  // React 19: não precisa mais de <TemaContext.Provider>
  return <TemaContext value={{ tema, alternar }}>{children}</TemaContext>;
}

export function useTema() {
  const ctx = useContext(TemaContext);
  if (!ctx) throw new Error("useTema deve ser usado dentro de TemaProvider");
  return ctx;
}
```

### 2.8 Hooks customizados

```tsx
"use client";
import { useState, useEffect } from "react";

export function useLocalStorage<T>(chave: string, inicial: T) {
  const [valor, setValor] = useState<T>(inicial);

  useEffect(() => {
    const salvo = localStorage.getItem(chave);
    if (salvo) setValor(JSON.parse(salvo));
  }, [chave]);

  useEffect(() => {
    localStorage.setItem(chave, JSON.stringify(valor));
  }, [chave, valor]);

  return [valor, setValor] as const;
}
```

### 2.9 Novidades do React 19 que você usará no Next.js

```tsx
// ref como prop normal (não precisa mais de forwardRef)
function Campo({ ref, ...props }: React.ComponentProps<"input">) {
  return <input ref={ref} {...props} />;
}

// use(): lê uma Promise ou Context (pode ser chamado condicionalmente)
import { use } from "react";
function Comentarios({ promessa }: { promessa: Promise<Comentario[]> }) {
  const comentarios = use(promessa);   // suspende até resolver
  return <ul>{comentarios.map((c) => <li key={c.id}>{c.texto}</li>)}</ul>;
}

// Actions: useActionState, useFormStatus, useOptimistic (Módulo 7)

// Metadados direto no componente
function Pagina() {
  return (
    <>
      <title>Minha página</title>
      <meta name="description" content="..." />
    </>
  );
}
```

### Exercícios do Módulo 2
1. Crie um componente `ListaDeTarefas` com adicionar, concluir e remover (estado imutável).
2. Extraia a lógica para um hook `useTarefas`.
3. Crie um `TemaProvider` e um botão que alterna entre claro e escuro.

---

# PARTE 2: NEXT.JS

## Módulo 3 · Primeiros passos com Next.js

### 3.1 O que é o Next.js

Framework **full-stack** para React que fornece:

| Recurso | O que oferece |
|---------|---------------|
| Roteamento | Baseado em pastas e arquivos (App Router) |
| Renderização | Servidor, estático, incremental e streaming |
| Dados | Busca de dados no servidor, Server Actions, Route Handlers |
| Otimização | Imagens, fontes, scripts, code splitting automático |
| Build | Turbopack (padrão no Next.js 16) |
| Deploy | Vercel, Node.js, Docker, exportação estática |

**Por que usar Next.js em vez de React puro?** SEO e desempenho inicial (HTML renderizado no servidor), backend integrado, roteamento pronto e otimizações automáticas.

### 3.2 Requisitos e criação do projeto

- **Node.js 20.9 ou superior** (o Node 18 não é mais suportado no Next.js 16).

```bash
npx create-next-app@latest meu-app
cd meu-app
npm run dev
```

O assistente pergunta: TypeScript, linter (ESLint ou Biome), Tailwind CSS, diretório `src/`, App Router, React Compiler e alias de importação (`@/*`). Para o treinamento, escolha **TypeScript, ESLint, Tailwind, `src/` e App Router**.

Acesse http://localhost:3000.

### 3.3 Scripts

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint"
  }
}
```

> **Mudança no Next.js 16**: o comando `next lint` foi removido. Use o ESLint (ou Biome) diretamente. O **Turbopack** agora é o bundler padrão em `dev` e `build`.

### 3.4 Estrutura de pastas

```
meu-app/
├── public/                  ← arquivos estáticos servidos na raiz (/logo.png)
├── src/
│   ├── app/
│   │   ├── layout.tsx       ← layout raiz (obrigatório)
│   │   ├── page.tsx         ← rota "/"
│   │   ├── globals.css
│   │   └── favicon.ico
│   ├── components/          ← componentes reutilizáveis
│   └── lib/                 ← utilitários, acesso a dados
├── next.config.ts           ← configuração do Next.js
├── proxy.ts                 ← (opcional) interceptação de requisições
├── .env.local               ← variáveis de ambiente (não versionar)
├── tsconfig.json
└── package.json
```

### 3.5 Primeira página e layout raiz

```tsx
// src/app/layout.tsx
import type { Metadata } from "next";
import "./globals.css";

export const metadata: Metadata = {
  title: { default: "Minha Loja", template: "%s | Minha Loja" },
  description: "Loja construída com Next.js",
};

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="pt-BR">
      <body>
        <header>Minha Loja</header>
        <main>{children}</main>
        <footer>© 2026</footer>
      </body>
    </html>
  );
}
```

```tsx
// src/app/page.tsx  →  rota "/"
export default function Home() {
  return <h1>Olá, Next.js!</h1>;
}
```

### 3.6 Configuração (`next.config.ts`)

```ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  reactStrictMode: true,
  images: {
    remotePatterns: [{ protocol: "https", hostname: "images.exemplo.com" }],
  },
  // cacheComponents: true,   // Módulo 11
  // reactCompiler: true,     // memoização automática (requer babel-plugin-react-compiler)
};

export default nextConfig;
```

### 3.7 Variáveis de ambiente

```bash
# .env.local
DATABASE_URL="postgresql://..."          # só no servidor
SESSION_SECRET="troque-por-um-segredo-longo"
NEXT_PUBLIC_API_URL="https://api.exemplo.com"   # exposta ao navegador
```

```ts
const url = process.env.DATABASE_URL;                 // servidor
const api = process.env.NEXT_PUBLIC_API_URL;          // servidor e cliente
```

> Apenas variáveis com prefixo `NEXT_PUBLIC_` vão para o código do navegador. Nunca coloque segredos nelas.

---

## Módulo 4 · Roteamento (App Router)

### 4.1 Rotas por pastas

Cada **pasta** dentro de `app/` é um segmento de URL. O arquivo `page.tsx` torna o segmento acessível publicamente.

```
app/
├── page.tsx                    →  /
├── sobre/page.tsx              →  /sobre
├── blog/
│   ├── page.tsx                →  /blog
│   └── [slug]/page.tsx         →  /blog/qualquer-coisa
└── produtos/
    └── [categoria]/[id]/page.tsx   →  /produtos/telas/42
```

### 4.2 Arquivos especiais

| Arquivo | Função |
|---------|--------|
| `layout.tsx` | UI compartilhada que **persiste** entre navegações |
| `page.tsx` | UI única de uma rota |
| `template.tsx` | Como layout, mas **recriado** a cada navegação |
| `loading.tsx` | Fallback de carregamento (Suspense automático) |
| `error.tsx` | Fronteira de erro (deve ser Client Component) |
| `not-found.tsx` | UI de 404 |
| `route.ts` | Endpoint de API |
| `default.tsx` | Fallback de *parallel routes* |
| `global-error.tsx` | Erro no layout raiz |

### 4.3 Rotas dinâmicas

```tsx
// app/blog/[slug]/page.tsx
export default async function PostPage({
  params,
}: {
  params: Promise<{ slug: string }>;
}) {
  const { slug } = await params;      // params é uma Promise (assíncrono)
  return <h1>Post: {slug}</h1>;
}
```

> **Importante (Next.js 15+)**: `params`, `searchParams`, `cookies()` e `headers()` são **assíncronos**. Sempre use `await`.

```tsx
// Query string: /produtos?ordem=preco&pagina=2
export default async function Produtos({
  searchParams,
}: {
  searchParams: Promise<{ ordem?: string; pagina?: string }>;
}) {
  const { ordem = "nome", pagina = "1" } = await searchParams;
  // ...
}
```

**Catch-all e opcional**

```
app/docs/[...slug]/page.tsx      → /docs/a, /docs/a/b/c     (slug: string[])
app/docs/[[...slug]]/page.tsx    → também /docs              (slug opcional)
```

> **Dica**: a partir do Next.js 15.5, existem tipos auxiliares globais gerados automaticamente (`PageProps<'/blog/[slug]'>`, `LayoutProps`) com o comando `next typegen`, que evitam digitar esses tipos manualmente.

### 4.4 Layouts aninhados

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex">
      <aside>Menu do painel</aside>
      <section className="flex-1">{children}</section>
    </div>
  );
}
```

O layout do dashboard é **aninhado** dentro do layout raiz e não é remontado ao navegar entre `/dashboard/a` e `/dashboard/b`.

### 4.5 Grupos de rotas e pastas privadas

```
app/
├── (marketing)/            ← parênteses: organiza SEM afetar a URL
│   ├── layout.tsx          ← layout só do grupo
│   ├── page.tsx            →  /
│   └── sobre/page.tsx      →  /sobre
├── (app)/
│   ├── layout.tsx
│   └── dashboard/page.tsx  →  /dashboard
└── _componentes/           ← prefixo _ : pasta privada, fora do roteamento
```

### 4.6 Navegação

```tsx
import Link from "next/link";

<Link href="/blog">Blog</Link>
<Link href={`/blog/${post.slug}`} prefetch={false}>Ler</Link>
<Link href={{ pathname: "/produtos", query: { ordem: "preco" } }}>Ordenar</Link>
```

Navegação programática e hooks (**Client Components**):

```tsx
"use client";
import { useRouter, usePathname, useSearchParams, useParams } from "next/navigation";

export function Filtro() {
  const router = useRouter();
  const pathname = usePathname();          // "/produtos"
  const searchParams = useSearchParams();  // lê ?ordem=preco
  const params = useParams<{ slug: string }>();

  function aplicar(ordem: string) {
    const novos = new URLSearchParams(searchParams.toString());
    novos.set("ordem", ordem);
    router.push(`${pathname}?${novos.toString()}`);
  }

  return <button onClick={() => aplicar("preco")}>Ordenar por preço</button>;
}
```

No servidor:

```tsx
import { redirect, notFound, permanentRedirect } from "next/navigation";

const produto = await buscar(id);
if (!produto) notFound();                 // renderiza not-found.tsx (404)
if (produto.movido) redirect("/novo");    // 307
```

> `useSearchParams` em uma página estática exige um `<Suspense>` ao redor do componente que o utiliza.

### 4.7 Estados de carregamento, erro e 404

```tsx
// app/produtos/loading.tsx: exibido enquanto page.tsx carrega
export default function Loading() {
  return <div className="animate-pulse">Carregando produtos...</div>;
}
```

```tsx
// app/produtos/error.tsx: precisa ser Client Component
"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div>
      <h2>Algo deu errado</h2>
      <button onClick={() => reset()}>Tentar novamente</button>
    </div>
  );
}
```

```tsx
// app/not-found.tsx
import Link from "next/link";

export default function NotFound() {
  return (
    <div>
      <h2>Página não encontrada</h2>
      <Link href="/">Voltar ao início</Link>
    </div>
  );
}
```

### Exercícios do Módulo 4
1. Crie `/blog` e `/blog/[slug]` com um layout próprio para o blog.
2. Implemente uma página `/produtos` com filtros via query string (`?categoria=&ordem=`).
3. Use um grupo `(marketing)` e outro `(app)` com layouts diferentes.

---

## Módulo 5 · Server Components e Client Components

Este é **o conceito mais importante** do Next.js moderno.

### 5.1 Server Components (padrão)

No App Router, **todo componente é Server Component por padrão**. Ele roda **apenas no servidor**, e o navegador recebe somente o resultado.

```tsx
// app/produtos/page.tsx  (Server Component)
import { db } from "@/lib/db";

export default async function ProdutosPage() {
  const produtos = await db.produto.findMany();   // acesso direto ao banco

  return (
    <ul>
      {produtos.map((p) => (
        <li key={p.id}>{p.nome}</li>
      ))}
    </ul>
  );
}
```

**Vantagens**
- Acesso direto a banco, arquivos e segredos (nada disso vai para o navegador).
- **Zero JavaScript** enviado para esse componente (bundle menor).
- Componentes podem ser `async` e usar `await` diretamente.
- Melhor SEO e carregamento inicial.

**Limitações**: não podem usar `useState`, `useEffect`, eventos (`onClick`) nem APIs do navegador (`window`, `localStorage`).

### 5.2 Client Components

Adicione `"use client"` **no topo do arquivo** para marcá-lo (e tudo que ele importa) como parte do bundle do cliente.

```tsx
"use client";

import { useState } from "react";

export function BotaoCurtir({ inicial }: { inicial: number }) {
  const [curtidas, setCurtidas] = useState(inicial);
  return <button onClick={() => setCurtidas(curtidas + 1)}>👍 {curtidas}</button>;
}
```

> Client Components **também são renderizados no servidor** na primeira carga (SSR) e depois sofrem *hidratação* no navegador. "Client" quer dizer "disponível no cliente", e não "somente no cliente".

### 5.3 Quando usar cada um

| Preciso de... | Use |
|---------------|-----|
| Buscar dados, acessar banco/segredos | **Server** |
| Reduzir JavaScript enviado | **Server** |
| `useState`, `useEffect`, `useReducer` | **Client** |
| Eventos (`onClick`, `onChange`) | **Client** |
| APIs do navegador (`window`, `localStorage`, geolocalização) | **Client** |
| Context providers | **Client** |
| Bibliotecas que dependem de hooks/estado | **Client** |

**Regra prática**: mantenha tudo como Server Component e crie Client Components **pequenos e nas folhas da árvore**, só onde há interatividade.

### 5.4 Composição: Server dentro de Client

Um Client Component **não pode importar** um Server Component, mas pode **receber um como `children`** (ou outra prop).

```tsx
// components/Modal.tsx
"use client";
import { useState } from "react";

export function Modal({ children }: { children: React.ReactNode }) {
  const [aberto, setAberto] = useState(false);
  return (
    <>
      <button onClick={() => setAberto(true)}>Abrir</button>
      {aberto && <div className="modal">{children}</div>}
    </>
  );
}

// app/page.tsx (Server Component)
import { Modal } from "@/components/Modal";
import { ConteudoPesado } from "@/components/ConteudoPesado"; // Server Component

export default function Page() {
  return (
    <Modal>
      <ConteudoPesado />   {/* renderizado no servidor, passado como children */}
    </Modal>
  );
}
```

### 5.5 Passando dados do servidor para o cliente

As props de um Client Component precisam ser **serializáveis** (strings, números, objetos simples, arrays, `Date`, Promises, Server Actions). Funções comuns e instâncias de classe **não** podem cruzar a fronteira.

```tsx
// Server Component
import { Carrinho } from "./Carrinho"; // "use client"

export default async function Page() {
  const itens = await buscarItens();
  return <Carrinho itensIniciais={itens} />;     // OK: dado serializável
}
```

**Passar uma Promise e consumir com `use()`** (sem bloquear a renderização):

```tsx
// Server Component
export default function Page() {
  const comentarios = buscarComentarios();      // sem await
  return (
    <Suspense fallback={<p>Carregando...</p>}>
      <ListaComentarios promessa={comentarios} />
    </Suspense>
  );
}

// "use client"
export function ListaComentarios({ promessa }: { promessa: Promise<Comentario[]> }) {
  const comentarios = use(promessa);
  return <ul>{comentarios.map((c) => <li key={c.id}>{c.texto}</li>)}</ul>;
}
```

### 5.6 Proteção de código exclusivo do servidor

```bash
npm install server-only
```

```ts
// lib/dados.ts
import "server-only";   // gera erro de build se algum Client Component importar este módulo

export async function buscarSegredos() {
  return fetch("https://api.interna", {
    headers: { Authorization: `Bearer ${process.env.API_TOKEN}` },
  });
}
```

### 5.7 Context providers e bibliotecas de terceiros

Providers precisam ser Client Components. Coloque-os o mais **fundo** possível e envolva só o necessário.

```tsx
// app/providers.tsx
"use client";
import { TemaProvider } from "@/components/TemaProvider";

export function Providers({ children }: { children: React.ReactNode }) {
  return <TemaProvider>{children}</TemaProvider>;
}

// app/layout.tsx (Server Component)
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="pt-BR">
      <body>
        <Providers>{children}</Providers>   {/* children continuam sendo Server Components */}
      </body>
    </html>
  );
}
```

Para bibliotecas que usam hooks mas não declaram `"use client"`, crie um *wrapper*:

```tsx
"use client";
export { Carousel } from "biblioteca-de-carrossel";
```

### Exercícios do Módulo 5
1. Crie uma página que lista produtos (Server Component) com um botão "Favoritar" (Client Component) em cada item.
2. Crie um `Modal` client que receba conteúdo de servidor via `children`.
3. Proteja um módulo de acesso ao banco com `server-only` e comprove que importá-lo num Client Component quebra o build.

---

## Módulo 6 · Busca de dados, renderização e streaming

### 6.1 Buscando dados em Server Components

```tsx
// lib/api.ts
export type Produto = { id: number; nome: string; preco: number; categoria: string };

export async function listarProdutos(): Promise<Produto[]> {
  const res = await fetch("https://api.exemplo.com/produtos");
  if (!res.ok) throw new Error("Falha ao buscar produtos");   // cai no error.tsx
  return res.json();
}

// app/produtos/page.tsx
import { listarProdutos } from "@/lib/api";

export default async function ProdutosPage() {
  const produtos = await listarProdutos();
  return (
    <ul>
      {produtos.map((p) => (
        <li key={p.id}>{p.nome}: R$ {p.preco.toFixed(2)}</li>
      ))}
    </ul>
  );
}
```

Também é possível consultar **diretamente o banco** ou usar um ORM no Server Component. Não é necessário criar uma API só para o seu próprio front-end.

### 6.2 Evitando "waterfalls": requisições em paralelo

```tsx
// ❌ Sequencial: o segundo só começa depois do primeiro
const usuario = await buscarUsuario(id);
const pedidos = await buscarPedidos(id);

// ✅ Paralelo
const [usuario, pedidos] = await Promise.all([buscarUsuario(id), buscarPedidos(id)]);
```

### 6.3 Streaming com `Suspense`

Em vez de esperar **tudo** ficar pronto, envie o HTML aos poucos: a parte rápida aparece logo e as lentas "entram" depois.

```tsx
import { Suspense } from "react";

export default function Dashboard() {
  return (
    <div>
      <h1>Painel</h1>                         {/* aparece imediatamente */}

      <Suspense fallback={<Skeleton />}>
        <Faturamento />                       {/* componente async lento */}
      </Suspense>

      <Suspense fallback={<Skeleton />}>
        <UltimosPedidos />                    {/* independente do anterior */}
      </Suspense>
    </div>
  );
}

async function Faturamento() {
  const dados = await calcularFaturamento();   // 2 segundos
  return <p>Total: R$ {dados.total}</p>;
}
```

`loading.tsx` é um atalho que envolve automaticamente o `page.tsx` com `<Suspense>`.

### 6.4 Estratégias de renderização

| Estratégia | Quando renderiza | Uso típico |
|-----------|------------------|-----------|
| **Estática (SSG)** | No *build* | Blog, documentação, páginas de marketing |
| **Dinâmica (SSR)** | A cada requisição | Dados por usuário, painel, busca |
| **Incremental (ISR)** | No build + revalidação periódica/sob demanda | Catálogo, notícias |
| **PPR / Cache Components** | Casca estática + partes dinâmicas em streaming | Páginas mistas (Módulo 11) |

Uma rota se torna **dinâmica** quando usa *APIs de runtime*: `cookies()`, `headers()`, `searchParams`, `connection()`, ou busca com `cache: "no-store"`.

```tsx
import { cookies } from "next/headers";

export default async function Perfil() {
  const cookieStore = await cookies();               // torna a rota dinâmica
  const tema = cookieStore.get("tema")?.value ?? "claro";
  return <p>Tema: {tema}</p>;
}
```

### 6.5 Geração estática de rotas dinâmicas

```tsx
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await listarPosts();
  return posts.map((p) => ({ slug: p.slug }));      // gera /blog/a, /blog/b... no build
}

export default async function Post({ params }: { params: Promise<{ slug: string }> }) {
  const { slug } = await params;
  const post = await buscarPost(slug);
  if (!post) notFound();
  return <article><h1>{post.titulo}</h1>{/* ... */}</article>;
}
```

### 6.6 Controle de cache em `fetch` (modelo clássico)

Desde o Next.js 15, `fetch` **não é cacheado por padrão**. Você opta explicitamente:

```ts
// Cache "para sempre" (até revalidar)
await fetch(url, { cache: "force-cache" });

// Revalida a cada hora (ISR)
await fetch(url, { next: { revalidate: 3600 } });

// Etiqueta para revalidar sob demanda
await fetch(url, { next: { tags: ["produtos"] } });

// Nunca cacheia
await fetch(url, { cache: "no-store" });
```

Configurações no nível da rota (modelo clássico, sem Cache Components):

```tsx
export const revalidate = 60;           // ISR: revalida a cada 60s
export const dynamic = "force-dynamic"; // força renderização dinâmica
export const dynamicParams = false;     // 404 para params fora do generateStaticParams
```

> O **Módulo 11** apresenta o novo modelo de cache do Next.js 16 (`"use cache"`), que substitui essas opções quando `cacheComponents` está habilitado.

### 6.7 Deduplicação com `React.cache`

Se vários componentes chamam a mesma função com os mesmos argumentos **na mesma requisição**, envolva-a em `cache` para executar uma única vez.

```ts
import { cache } from "react";
import { db } from "@/lib/db";

export const buscarUsuario = cache(async (id: string) => {
  return db.usuario.findUnique({ where: { id } });
});
// generateMetadata e a página podem chamar buscarUsuario(id) sem duplicar a consulta
```

> `React.cache` deduplica dentro de **uma** renderização. Não confunda com o cache persistente do Next.js (`"use cache"`).

### 6.8 Busca de dados no cliente

Para dados que dependem de interação do usuário (busca instantânea, *infinite scroll*, polling), use no cliente uma biblioteca como **TanStack Query** ou **SWR**:

```tsx
"use client";
import useSWR from "swr";

const fetcher = (url: string) => fetch(url).then((r) => r.json());

export function BuscaInstantanea({ termo }: { termo: string }) {
  const { data, error, isLoading } = useSWR(termo ? `/api/busca?q=${termo}` : null, fetcher);

  if (isLoading) return <p>Buscando...</p>;
  if (error) return <p>Erro na busca.</p>;
  return <ul>{data?.map((p: Produto) => <li key={p.id}>{p.nome}</li>)}</ul>;
}
```

### Exercícios do Módulo 6
1. Faça um dashboard com 3 widgets independentes, cada um em seu `<Suspense>` com *skeleton*.
2. Gere estaticamente `/blog/[slug]` com `generateStaticParams`.
3. Meça (com `console.time`) a diferença entre requisições sequenciais e com `Promise.all`.

---

## Módulo 7 · Server Actions e formulários

### 7.1 O que são Server Actions

Funções assíncronas que **executam no servidor**, mas podem ser chamadas diretamente de formulários e componentes de cliente, sem criar manualmente uma rota de API.

```ts
// app/actions.ts
"use server";   // todas as funções exportadas deste arquivo são Server Actions

export async function criarTarefa(formData: FormData) {
  const titulo = formData.get("titulo") as string;
  await db.tarefa.create({ data: { titulo } });
  revalidatePath("/tarefas");
}
```

```tsx
// app/tarefas/page.tsx (Server Component)
import { criarTarefa } from "../actions";

export default function Page() {
  return (
    <form action={criarTarefa}>
      <input name="titulo" required />
      <button type="submit">Adicionar</button>
    </form>
  );
}
```

Esse formulário funciona **mesmo sem JavaScript** habilitado no navegador (*progressive enhancement*).

### 7.2 Validação com Zod + estado do formulário

```bash
npm install zod
```

```ts
// app/produtos/actions.ts
"use server";

import { z } from "zod";                       // Zod 4
import { redirect } from "next/navigation";
import { updateTag } from "next/cache";
import { db } from "@/lib/db";
import { verifySession } from "@/lib/dal";

const ProdutoSchema = z.object({
  nome: z.string().min(2, "Nome deve ter ao menos 2 caracteres"),
  preco: z.coerce.number().positive("Preço deve ser positivo"),
  categoria: z.string().min(1, "Escolha uma categoria"),
});

export type ProdutoFormState =
  | {
      errors?: Partial<Record<"nome" | "preco" | "categoria", string[]>>;
      message?: string;
    }
  | undefined;

export async function criarProduto(
  _prev: ProdutoFormState,
  formData: FormData
): Promise<ProdutoFormState> {
  // 1) Autorização: Server Actions são endpoints públicos! Sempre verifique.
  await verifySession();

  // 2) Validação no servidor (nunca confie apenas na validação do cliente)
  const parsed = ProdutoSchema.safeParse(Object.fromEntries(formData));
  if (!parsed.success) {
    return { errors: z.flattenError(parsed.error).fieldErrors };
  }

  // 3) Persistência
  try {
    await db.produto.create({ data: parsed.data });
  } catch {
    return { message: "Não foi possível salvar o produto." };
  }

  // 4) Invalidar cache e redirecionar (redirect fica FORA do try/catch)
  updateTag("produtos");
  redirect("/produtos");
}
```

```tsx
// app/produtos/novo/FormProduto.tsx
"use client";

import { useActionState } from "react";
import { criarProduto } from "../actions";

export function FormProduto() {
  const [state, action, pending] = useActionState(criarProduto, undefined);

  return (
    <form action={action} className="space-y-3">
      <div>
        <input name="nome" placeholder="Nome" />
        {state?.errors?.nome && <p className="text-red-600">{state.errors.nome[0]}</p>}
      </div>

      <div>
        <input name="preco" type="number" step="0.01" placeholder="Preço" />
        {state?.errors?.preco && <p className="text-red-600">{state.errors.preco[0]}</p>}
      </div>

      <select name="categoria" defaultValue="">
        <option value="" disabled>Categoria...</option>
        <option value="telas">Telas</option>
        <option value="perifericos">Periféricos</option>
      </select>

      {state?.message && <p className="text-red-600">{state.message}</p>}

      <button type="submit" disabled={pending}>
        {pending ? "Salvando..." : "Salvar"}
      </button>
    </form>
  );
}
```

### 7.3 `useFormStatus`: botão de envio reutilizável

```tsx
"use client";
import { useFormStatus } from "react-dom";

export function BotaoEnviar({ children }: { children: React.ReactNode }) {
  const { pending } = useFormStatus();    // deve estar DENTRO de um <form>
  return <button disabled={pending}>{pending ? "Enviando..." : children}</button>;
}
```

### 7.4 Atualizações otimistas com `useOptimistic`

```tsx
"use client";
import { useOptimistic } from "react";
import { adicionarComentario } from "./actions";

export function Comentarios({ iniciais }: { iniciais: Comentario[] }) {
  const [otimistas, adicionarOtimista] = useOptimistic(
    iniciais,
    (estado, novo: Comentario) => [...estado, novo]
  );

  async function enviar(formData: FormData) {
    const texto = formData.get("texto") as string;
    adicionarOtimista({ id: Date.now(), texto, enviando: true });  // aparece na hora
    await adicionarComentario(texto);                              // confirma no servidor
  }

  return (
    <>
      <ul>
        {otimistas.map((c) => (
          <li key={c.id} style={{ opacity: c.enviando ? 0.5 : 1 }}>{c.texto}</li>
        ))}
      </ul>
      <form action={enviar}>
        <input name="texto" />
        <button>Comentar</button>
      </form>
    </>
  );
}
```

### 7.5 Chamando Server Actions fora de formulários

```tsx
"use client";
import { useTransition } from "react";
import { removerProduto } from "./actions";

export function BotaoRemover({ id }: { id: number }) {
  const [isPending, startTransition] = useTransition();

  return (
    <button
      disabled={isPending}
      onClick={() => startTransition(async () => { await removerProduto(id); })}
    >
      {isPending ? "Removendo..." : "Remover"}
    </button>
  );
}
```

Ou vincule argumentos com `bind` em um formulário no servidor:

```tsx
const removerComId = removerProduto.bind(null, produto.id);
<form action={removerComId}><button>Remover</button></form>
```

### 7.6 Revalidação após mutações

```ts
import { revalidatePath, revalidateTag, updateTag, refresh } from "next/cache";

revalidatePath("/produtos");                  // revalida uma rota
revalidatePath("/produtos/[id]", "page");     // padrão dinâmico
updateTag("produtos");                        // expira e atualiza NA HORA (read-your-writes; só em Server Actions)
revalidateTag("produtos", "max");             // stale-while-revalidate: serve o antigo e atualiza em segundo plano
refresh();                                    // atualiza dados não cacheados da página atual
```

### 7.7 Segurança em Server Actions

> **Trate toda Server Action como um endpoint HTTP público.** Qualquer cliente pode chamá-la.

- **Autentique e autorize** dentro de cada action (não confie que "o botão só aparece para admins").
- **Valide** todos os dados no servidor.
- **Não vaze** dados sensíveis no retorno.
- O Next.js cria IDs criptografados e não previsíveis para as actions e as elimina do bundle se não usadas, mas isso **não substitui** a autorização.
- Verifique o *ownership* do recurso: `where: { id, userId: sessao.userId }`.

### Exercícios do Módulo 7
1. Crie um CRUD de tarefas usando apenas Server Actions e formulários.
2. Adicione validação com Zod e mensagens de erro por campo.
3. Implemente "curtir" com `useOptimistic` e reverta em caso de erro.

---

## Módulo 8 · Route Handlers (APIs)

Use Route Handlers para **endpoints HTTP**: webhooks, integrações com terceiros, consumo por apps mobile ou clientes externos. Para mutações do seu próprio front-end, prefira Server Actions.

### 8.1 CRUD básico

```ts
// app/api/produtos/route.ts
import { NextRequest, NextResponse } from "next/server";
import { z } from "zod";
import { db } from "@/lib/db";
import { ProdutoSchema } from "@/lib/schemas";   // o mesmo schema Zod do Módulo 7

export async function GET(request: NextRequest) {
  const categoria = request.nextUrl.searchParams.get("categoria");

  const produtos = await db.produto.findMany({
    where: categoria ? { categoria } : undefined,
  });

  return NextResponse.json(produtos);
}

export async function POST(request: Request) {
  const body = await request.json();

  const parsed = ProdutoSchema.safeParse(body);
  if (!parsed.success) {
    return NextResponse.json({ errors: z.flattenError(parsed.error).fieldErrors }, { status: 400 });
  }

  const criado = await db.produto.create({ data: parsed.data });
  return NextResponse.json(criado, { status: 201 });
}
```

```ts
// app/api/produtos/[id]/route.ts
import { NextResponse } from "next/server";

type Ctx = { params: Promise<{ id: string }> };

export async function GET(_req: Request, { params }: Ctx) {
  const { id } = await params;
  const produto = await db.produto.findUnique({ where: { id: Number(id) } });
  if (!produto) return NextResponse.json({ error: "Não encontrado" }, { status: 404 });
  return NextResponse.json(produto);
}

export async function DELETE(_req: Request, { params }: Ctx) {
  const { id } = await params;
  await db.produto.delete({ where: { id: Number(id) } });
  return new NextResponse(null, { status: 204 });
}
```

Métodos suportados: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `HEAD`, `OPTIONS`.

> Handlers `GET` **não são cacheados por padrão** (desde o Next.js 15).

### 8.2 Cookies, headers e redirecionamentos

```ts
import { cookies, headers } from "next/headers";

export async function GET() {
  const h = await headers();
  const agente = h.get("user-agent");

  const c = await cookies();
  c.set("visitou", "1", { httpOnly: true, maxAge: 60 * 60 * 24 });

  return Response.json({ agente });
}
```

### 8.3 Webhook com verificação de assinatura

```ts
// app/api/webhooks/pagamento/route.ts
import crypto from "node:crypto";

export async function POST(request: Request) {
  const corpo = await request.text();                       // corpo BRUTO, necessário para a assinatura
  const assinatura = request.headers.get("x-signature") ?? "";

  const esperada = crypto
    .createHmac("sha256", process.env.WEBHOOK_SECRET!)
    .update(corpo)
    .digest("hex");

  const valido =
    assinatura.length === esperada.length &&
    crypto.timingSafeEqual(Buffer.from(assinatura), Buffer.from(esperada));

  if (!valido) return new Response("Assinatura inválida", { status: 401 });

  const evento = JSON.parse(corpo);
  // processar evento...
  return new Response("ok");
}
```

### 8.4 Streaming de resposta (Server-Sent Events)

```ts
// app/api/stream/route.ts
export async function GET() {
  const encoder = new TextEncoder();

  const stream = new ReadableStream({
    async start(controller) {
      for (let i = 1; i <= 5; i++) {
        controller.enqueue(encoder.encode(`data: evento ${i}\n\n`));
        await new Promise((r) => setTimeout(r, 1000));
      }
      controller.close();
    },
  });

  return new Response(stream, {
    headers: { "Content-Type": "text/event-stream", "Cache-Control": "no-cache" },
  });
}
```

### 8.5 CORS

```ts
const cors = {
  "Access-Control-Allow-Origin": "https://meu-front.com",
  "Access-Control-Allow-Methods": "GET,POST,OPTIONS",
  "Access-Control-Allow-Headers": "Content-Type, Authorization",
};

export async function OPTIONS() {
  return new Response(null, { status: 204, headers: cors });
}

export async function GET() {
  return Response.json({ ok: true }, { headers: cors });
}
```

### Exercícios do Módulo 8
1. Implemente `/api/tarefas` (GET/POST) e `/api/tarefas/[id]` (GET/PATCH/DELETE) com validação.
2. Crie um endpoint de webhook que valide a assinatura HMAC.
3. Crie um endpoint SSE que envie a hora atual a cada segundo e consuma-o com `EventSource`.

---

## Módulo 9 · Estilização, imagens, fontes e SEO

### 9.1 Estilização

**Tailwind CSS** (opção padrão do `create-next-app`):

```css
/* app/globals.css (Tailwind v4) */
@import "tailwindcss";
```

```tsx
export function Botao({ children }: { children: React.ReactNode }) {
  return (
    <button className="rounded-lg bg-blue-600 px-4 py-2 font-medium text-white
                       transition hover:bg-blue-700 disabled:opacity-50">
      {children}
    </button>
  );
}
```

**CSS Modules** (escopo local por arquivo):

```css
/* Botao.module.css */
.botao { padding: 8px 16px; border-radius: 8px; background: #2563eb; color: #fff; }
```

```tsx
import styles from "./Botao.module.css";
<button className={styles.botao}>Clique</button>
```

> **CSS-in-JS em runtime** (styled-components, Emotion) exige configuração extra e Client Components. Prefira Tailwind, CSS Modules ou bibliotecas com extração em build.

Utilitário comum para combinar classes condicionais:

```ts
// lib/utils.ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export const cn = (...inputs: ClassValue[]) => twMerge(clsx(inputs));
```

### 9.2 Componente `Image`

```tsx
import Image from "next/image";
import capa from "@/public/capa.jpg";   // import estático: dimensões automáticas

// Imagem local
<Image src={capa} alt="Capa do produto" priority placeholder="blur" />

// Imagem remota (declare o host em next.config.ts → images.remotePatterns)
<Image src="https://images.exemplo.com/p/1.jpg" alt="Produto" width={600} height={400} />

// Preenchendo o container (pai com position: relative)
<div className="relative h-64 w-full">
  <Image src={url} alt="Banner" fill sizes="(max-width: 768px) 100vw, 50vw" className="object-cover" />
</div>
```

Benefícios: formatos modernos (WebP/AVIF), redimensionamento por dispositivo, *lazy loading* por padrão e prevenção de *layout shift*. Use `priority` apenas na imagem principal visível ao carregar (LCP).

> `images.domains` está **depreciado**. Use `images.remotePatterns`.

### 9.3 Fontes

```tsx
// app/layout.tsx
import { Inter, Roboto_Mono } from "next/font/google";

const inter = Inter({ subsets: ["latin"], variable: "--font-inter", display: "swap" });
const mono = Roboto_Mono({ subsets: ["latin"], variable: "--font-mono" });

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="pt-BR" className={`${inter.variable} ${mono.variable}`}>
      <body className="font-sans">{children}</body>
    </html>
  );
}
```

As fontes são baixadas **no build** e servidas do seu próprio domínio (sem requisições ao Google em runtime).

### 9.4 Scripts de terceiros

```tsx
import Script from "next/script";

<Script src="https://www.googletagmanager.com/gtag/js?id=G-XXXX" strategy="afterInteractive" />
```

### 9.5 Metadados e SEO

```tsx
// Estático
export const metadata: Metadata = {
  title: "Produtos",
  description: "Catálogo completo",
  openGraph: { title: "Produtos", images: ["/og.png"] },
};

// Dinâmico
export async function generateMetadata({
  params,
}: {
  params: Promise<{ slug: string }>;
}): Promise<Metadata> {
  const { slug } = await params;
  const post = await buscarPost(slug);   // deduplicado se usar React.cache

  return {
    title: post?.titulo ?? "Post não encontrado",
    description: post?.resumo,
    alternates: { canonical: `/blog/${slug}` },
    openGraph: { title: post?.titulo, images: [post?.capa ?? "/og.png"] },
  };
}
```

```ts
// app/sitemap.ts
import type { MetadataRoute } from "next";

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const posts = await listarPosts();
  return [
    { url: "https://meusite.com", lastModified: new Date() },
    ...posts.map((p) => ({
      url: `https://meusite.com/blog/${p.slug}`,
      lastModified: p.atualizadoEm,
    })),
  ];
}

// app/robots.ts
export default function robots(): MetadataRoute.Robots {
  return {
    rules: { userAgent: "*", allow: "/", disallow: "/dashboard/" },
    sitemap: "https://meusite.com/sitemap.xml",
  };
}
```

**Imagem Open Graph gerada dinamicamente**

```tsx
// app/blog/[slug]/opengraph-image.tsx
import { ImageResponse } from "next/og";

export const size = { width: 1200, height: 630 };
export const contentType = "image/png";

export default async function OG({ params }: { params: Promise<{ slug: string }> }) {
  const { slug } = await params;
  const post = await buscarPost(slug);

  return new ImageResponse(
    (
      <div style={{ fontSize: 64, background: "#111", color: "#fff", width: "100%", height: "100%",
                    display: "flex", alignItems: "center", justifyContent: "center", padding: 60 }}>
        {post?.titulo}
      </div>
    ),
    size
  );
}
```

**Dados estruturados (JSON-LD)**

```tsx
const jsonLd = {
  "@context": "https://schema.org",
  "@type": "Article",
  headline: post.titulo,
  datePublished: post.publicadoEm,
};

<script
  type="application/ld+json"
  dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd).replace(/</g, "\\u003c") }}
/>
```

### Exercícios do Módulo 9
1. Aplique `next/font` e um tema com Tailwind em todo o site.
2. Gere `sitemap.xml`, `robots.txt` e uma imagem OG dinâmica para posts.
3. Rode o Lighthouse antes e depois de trocar `<img>` por `<Image>` e compare o LCP.

---

## Módulo 10 · Banco de dados e autenticação

### 10.1 Acesso a dados com ORM

Next.js não impõe um ORM. As opções mais usadas são **Prisma** e **Drizzle**.

```prisma
// prisma/schema.prisma
model Usuario {
  id        String    @id @default(cuid())
  email     String    @unique
  nome      String
  senhaHash String
  papel     Papel     @default(USER)
  produtos  Produto[]
  criadoEm  DateTime  @default(now())
}

model Produto {
  id        Int      @id @default(autoincrement())
  nome      String
  preco     Decimal  @db.Decimal(10, 2)
  categoria String
  donoId    String
  dono      Usuario  @relation(fields: [donoId], references: [id])
  criadoEm  DateTime @default(now())
}

enum Papel { USER ADMIN }
```

```ts
// lib/db.ts: instância única (evita múltiplas conexões durante o hot reload em dev)
import { PrismaClient } from "@prisma/client";

const globalForPrisma = globalThis as unknown as { prisma?: PrismaClient };

export const db = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = db;
```

> A configuração do cliente Prisma (geração, *driver adapters*) mudou em versões recentes. Consulte a documentação da versão que você instalar. Em ambientes *serverless*, use *connection pooling* (PgBouncer, Prisma Accelerate ou o pooler do provedor).

### 10.2 Autenticação: visão geral

| Abordagem | Quando usar |
|-----------|-------------|
| **Bibliotecas** (Auth.js, Better Auth) | Maioria dos projetos: login social, e-mail/senha, adaptadores de banco |
| **Serviços gerenciados** (Clerk, Auth0, Supabase Auth) | Quando se quer terceirizar a complexidade |
| **Sessão própria** (cookie + JWT/DB) | Controle total; ótimo para entender os conceitos |

Os conceitos principais são os mesmos: **autenticação** (quem é você), **gerenciamento de sessão** (lembrar de você entre requisições) e **autorização** (o que você pode fazer).

### 10.3 Sessão própria com cookie JWT (`jose`)

```bash
npm install jose bcryptjs server-only
```

```ts
// lib/session.ts
import "server-only";
import { SignJWT, jwtVerify } from "jose";
import { cookies } from "next/headers";

const chave = new TextEncoder().encode(process.env.SESSION_SECRET);
const DURACAO_MS = 7 * 24 * 60 * 60 * 1000;

type SessionPayload = { userId: string; papel: "USER" | "ADMIN"; expiresAt: Date };

export async function encrypt(payload: SessionPayload) {
  return new SignJWT(payload)
    .setProtectedHeader({ alg: "HS256" })
    .setIssuedAt()
    .setExpirationTime("7d")
    .sign(chave);
}

export async function decrypt(token: string | undefined = "") {
  try {
    const { payload } = await jwtVerify(token, chave, { algorithms: ["HS256"] });
    return payload as unknown as SessionPayload;
  } catch {
    return null;
  }
}

export async function createSession(userId: string, papel: "USER" | "ADMIN") {
  const expiresAt = new Date(Date.now() + DURACAO_MS);
  const token = await encrypt({ userId, papel, expiresAt });

  (await cookies()).set("session", token, {
    httpOnly: true,         // inacessível ao JavaScript (mitiga XSS)
    secure: true,           // somente HTTPS
    sameSite: "lax",        // mitiga CSRF
    expires: expiresAt,
    path: "/",
  });
}

export async function deleteSession() {
  (await cookies()).delete("session");
}
```

### 10.4 Cadastro, login e logout

```ts
// app/auth/actions.ts
"use server";

import bcrypt from "bcryptjs";
import { redirect } from "next/navigation";
import { z } from "zod";
import { db } from "@/lib/db";
import { createSession, deleteSession } from "@/lib/session";

const CadastroSchema = z.object({
  nome: z.string().min(2),
  email: z.email("E-mail inválido"),
  senha: z.string().min(8, "Mínimo de 8 caracteres"),
});

export async function cadastrar(_prev: unknown, formData: FormData) {
  const parsed = CadastroSchema.safeParse(Object.fromEntries(formData));
  if (!parsed.success) return { errors: z.flattenError(parsed.error).fieldErrors };

  const { nome, email, senha } = parsed.data;

  const existente = await db.usuario.findUnique({ where: { email } });
  if (existente) return { message: "E-mail já cadastrado." };

  const senhaHash = await bcrypt.hash(senha, 12);
  const user = await db.usuario.create({ data: { nome, email, senhaHash } });

  await createSession(user.id, user.papel);
  redirect("/dashboard");
}

export async function login(_prev: unknown, formData: FormData) {
  const email = String(formData.get("email") ?? "");
  const senha = String(formData.get("senha") ?? "");

  const user = await db.usuario.findUnique({ where: { email } });
  const ok = user && (await bcrypt.compare(senha, user.senhaHash));

  // Mensagem genérica: não revele se o e-mail existe
  if (!user || !ok) return { message: "E-mail ou senha inválidos." };

  await createSession(user.id, user.papel);
  redirect("/dashboard");
}

export async function logout() {
  await deleteSession();
  redirect("/login");
}
```

### 10.5 Data Access Layer (DAL): onde a autorização realmente acontece

Centralize a verificação de sessão e o acesso a dados em uma camada própria. **Cada acesso a dado sensível** passa por ela.

```ts
// lib/dal.ts
import "server-only";
import { cache } from "react";
import { cookies } from "next/headers";
import { redirect } from "next/navigation";
import { decrypt } from "./session";
import { db } from "./db";

export const verifySession = cache(async () => {
  const token = (await cookies()).get("session")?.value;
  const sessao = await decrypt(token);

  if (!sessao?.userId) redirect("/login");

  return { userId: sessao.userId, papel: sessao.papel };
});

export const getUsuarioAtual = cache(async () => {
  const { userId } = await verifySession();
  return db.usuario.findUnique({
    where: { id: userId },
    select: { id: true, nome: true, email: true },   // nunca retorne senhaHash
  });
});

export async function listarMeusProdutos() {
  const { userId } = await verifySession();
  return db.produto.findMany({ where: { donoId: userId } });   // escopo do usuário
}

export async function exigirAdmin() {
  const sessao = await verifySession();
  if (sessao.papel !== "ADMIN") throw new Error("Não autorizado");
  return sessao;
}
```

```tsx
// app/dashboard/page.tsx
import { getUsuarioAtual, listarMeusProdutos } from "@/lib/dal";

export default async function Dashboard() {
  const [usuario, produtos] = await Promise.all([getUsuarioAtual(), listarMeusProdutos()]);
  return <h1>Olá, {usuario?.nome}. Você tem {produtos.length} produtos.</h1>;
}
```

### 10.6 `proxy.ts`: verificação otimista e rápida

No Next.js 16, o antigo `middleware.ts` passou a se chamar **`proxy.ts`** (a função exportada é `proxy`, e ele roda no runtime Node.js). Use-o para redirecionamentos rápidos, sem consultar o banco.

```ts
// proxy.ts (na raiz do projeto ou em src/)
import { NextResponse, type NextRequest } from "next/server";
// Dica: mantenha a verificação do JWT (decrypt) em um módulo sem "server-only" (ex.: lib/jwt.ts)
// e reutilize-o em lib/session.ts e aqui.
import { decrypt } from "@/lib/jwt";

const rotasProtegidas = ["/dashboard", "/admin"];
const rotasPublicas = ["/login", "/cadastro"];

export async function proxy(request: NextRequest) {
  const { pathname } = request.nextUrl;
  const sessao = await decrypt(request.cookies.get("session")?.value);

  const protegida = rotasProtegidas.some((r) => pathname.startsWith(r));
  const publica = rotasPublicas.includes(pathname);

  if (protegida && !sessao) {
    return NextResponse.redirect(new URL("/login", request.url));
  }
  if (publica && sessao) {
    return NextResponse.redirect(new URL("/dashboard", request.url));
  }
  return NextResponse.next();
}

export const config = {
  matcher: ["/((?!api|_next/static|_next/image|.*\\.png$).*)"],
};
```

> **Princípio de defesa em profundidade**: o `proxy.ts` é apenas uma camada **otimista** de UX. A autorização **definitiva** deve estar no DAL, nas Server Actions e nos Route Handlers. Já houve vulnerabilidades em que o middleware podia ser contornado, e quem verificava a sessão apenas nele ficou exposto.
>
> Se você está migrando: renomeie `middleware.ts` para `proxy.ts` e a função `middleware` para `proxy` (existe um *codemod*: `npx @next/codemod@canary upgrade latest`).

### 10.7 Autorização por papel (RBAC)

```ts
// Server Action protegida
"use server";
export async function removerUsuario(id: string) {
  await exigirAdmin();
  await db.usuario.delete({ where: { id } });
  updateTag("usuarios");
}
```

```tsx
// Renderização condicional (somente UX; a segurança está na action!)
const { papel } = await verifySession();
{papel === "ADMIN" && <PainelAdmin />}
```

### 10.8 Checklist de segurança de autenticação

- [ ] Senhas com **bcrypt/argon2** (nunca MD5/SHA simples).
- [ ] Cookies `httpOnly`, `secure`, `sameSite`.
- [ ] Mensagens de erro genéricas no login.
- [ ] **Rate limiting** em login e cadastro (ex.: Upstash Ratelimit).
- [ ] Autorização verificada em **toda** Server Action, Route Handler e consulta (DAL).
- [ ] Segredos em variáveis de ambiente; rotação periódica.
- [ ] Verificação de e-mail, recuperação de senha segura (tokens de uso único com expiração).
- [ ] Considere MFA/passkeys e login social para produção.
- [ ] Mantenha `next` e dependências **atualizados** (correções de segurança são frequentes).

### Exercícios do Módulo 10
1. Implemente cadastro, login e logout com sessão em cookie.
2. Crie o DAL e proteja `/dashboard` em `proxy.ts` **e** na página.
3. Adicione o papel `ADMIN` e uma action que só administradores podem executar.
4. (Desafio) Reimplemente a autenticação com Auth.js ou Better Auth e compare.

---

## Módulo 11 · Cache e Cache Components

O cache é a parte do Next.js que mais evoluiu. Existem **dois modelos**:

| | Modelo clássico (Next.js 14/15) | **Cache Components (Next.js 16)** |
|---|---|---|
| Ativação | padrão | `cacheComponents: true` |
| Filosofia | Cache implícito + configurações de segmento | **Tudo dinâmico por padrão; cache explícito (opt-in)** |
| Mecanismo | `fetch` options, `revalidate`, `dynamic`, `unstable_cache` | Diretiva **`"use cache"`**, `cacheLife`, `cacheTag` |
| Renderização parcial | `experimental.ppr` | Embutida: casca estática + trechos em streaming |

### 11.1 Ativando

```ts
// next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

### 11.2 O modelo mental: três tipos de conteúdo

```tsx
import { Suspense } from "react";

export default function ProdutoPage() {
  return (
    <>
      <Cabecalho />                        {/* 1) ESTÁTICO: sem I/O, entra na casca pré-renderizada */}

      <DetalhesDoProduto />                {/* 2) CACHEADO: 'use cache', também entra na casca */}

      <Suspense fallback={<p>Carregando...</p>}>
        <RecomendacoesParaVoce />          {/* 3) DINÂMICO: depende do usuário; faz streaming */}
      </Suspense>
    </>
  );
}
```

- **Estático**: computação pura, sem acesso a dados ou APIs de runtime.
- **Cacheado**: resultado reaproveitado entre requisições (`"use cache"`).
- **Dinâmico**: depende da requisição (`cookies()`, `headers()`, `searchParams`) ou de dados não cacheados. **Precisa estar dentro de `<Suspense>`**, senão o Next.js avisa com um erro explicativo em desenvolvimento.

O navegador recebe a **casca estática instantaneamente** (de um CDN) e as partes dinâmicas chegam em *streaming*.

### 11.3 A diretiva `"use cache"`

Pode ser aplicada em três níveis:

```tsx
// 1) Função de dados
import { cacheLife, cacheTag } from "next/cache";

export async function listarProdutos() {
  "use cache";
  cacheLife("hours");            // perfil de duração
  cacheTag("produtos");          // etiqueta para invalidação
  return db.produto.findMany();
}

// 2) Componente
export async function Destaques() {
  "use cache";
  cacheLife("minutes");
  const itens = await buscarDestaques();
  return <Carrossel itens={itens} />;
}

// 3) Arquivo inteiro: todas as exports assíncronas são cacheadas
"use cache";
export default async function Page() { /* ... */ }
```

As **entradas** (argumentos e variáveis capturadas) fazem parte da chave de cache. Cada combinação diferente gera uma entrada própria:

```ts
export async function buscarProduto(id: number) {
  "use cache";
  cacheTag(`produto-${id}`);
  return db.produto.findUnique({ where: { id } });
}
```

### 11.4 Perfis de duração (`cacheLife`)

Perfis prontos: `"seconds"`, `"minutes"`, `"hours"`, `"days"`, `"weeks"`, `"max"`. Também é possível criar perfis próprios:

```ts
// next.config.ts
const nextConfig: NextConfig = {
  cacheComponents: true,
  cacheLife: {
    catalogo: {
      stale: 300,        // 5 min: tempo que o cliente pode usar sem consultar o servidor
      revalidate: 900,   // 15 min: após isso, atualiza em segundo plano
      expire: 86400,     // 1 dia: após isso, expira de vez
    },
  },
};
```

```ts
cacheLife("catalogo");
```

### 11.5 Dados de runtime e `"use cache"`

Dentro de uma função/componente cacheado você **não pode** ler `cookies()`, `headers()` ou `searchParams`. Leia **fora** e passe como argumento:

```tsx
import { cookies } from "next/headers";
import { Suspense } from "react";

export default function Page() {
  return (
    <Suspense fallback={<p>...</p>}>
      <Preco />
    </Suspense>
  );
}

async function Preco() {
  const moeda = (await cookies()).get("moeda")?.value ?? "BRL";   // runtime: fora do cache
  return <PrecoCacheado moeda={moeda} />;
}

async function PrecoCacheado({ moeda }: { moeda: string }) {
  "use cache";                                                    // moeda faz parte da chave
  const preco = await converterPreco(moeda);
  return <p>{preco}</p>;
}
```

### 11.6 Invalidação

```ts
"use server";
import { updateTag, revalidateTag, revalidatePath, refresh } from "next/cache";

export async function atualizarProduto(id: number, dados: Dados) {
  await db.produto.update({ where: { id }, data: dados });

  updateTag(`produto-${id}`);        // read-your-writes: o próprio usuário já vê o dado novo
  updateTag("produtos");
}

export async function importarCatalogo() {
  await executarImportacao();
  revalidateTag("produtos", "max");  // stale-while-revalidate: serve o antigo e atualiza ao fundo
}
```

| Função | Comportamento | Onde usar |
|--------|---------------|-----------|
| `updateTag(tag)` | Expira imediatamente; próxima leitura espera o dado novo | Server Actions |
| `revalidateTag(tag, perfil)` | Serve o conteúdo antigo e revalida em segundo plano | Actions e Route Handlers |
| `revalidatePath(path)` | Invalida por rota | Actions e Route Handlers |
| `refresh()` | Atualiza apenas o que **não** é cacheado | Server Actions |

### 11.7 Forçando dinamismo e valores não determinísticos

```tsx
import { connection } from "next/server";

async function Aleatorio() {
  await connection();                 // "aguarde a requisição real": torna o trecho dinâmico
  const n = Math.random();            // Math.random/Date.now exigem esse tratamento
  return <p>{n}</p>;
}
```

### 11.8 Migrando do modelo clássico

| Antes | Agora (com `cacheComponents`) |
|-------|-------------------------------|
| `export const dynamic = "force-dynamic"` | Removido: é o padrão; use `<Suspense>` |
| `export const dynamic = "force-static"` | `"use cache"` |
| `export const revalidate = 60` | `cacheLife({ revalidate: 60 })` ou um perfil |
| `fetch(url, { next: { tags } })` | `"use cache"` + `cacheTag()` |
| `unstable_cache(fn)` | `"use cache"` na função |
| `experimental.ppr` | Já faz parte do Cache Components |

### 11.9 Camadas de cache (visão geral)

| Camada | Escopo | O que faz |
|--------|--------|-----------|
| Memoização de requisição (`React.cache`) | Uma renderização | Evita chamadas duplicadas |
| **`"use cache"`** | Servidor, entre requisições | Armazena resultados de funções/componentes |
| Casca estática (static shell) | Build/CDN | HTML pré-renderizado servido instantaneamente |
| **Router Cache** | Navegador | Guarda segmentos visitados/prefetch para navegação instantânea |

> No Next.js 16, a navegação foi otimizada com **deduplicação de layouts** e **prefetch incremental**: layouts compartilhados são baixados uma única vez, e só os trechos que faltam são buscados.

### Exercícios do Módulo 11
1. Habilite `cacheComponents` e crie uma página com casca estática, um trecho cacheado e um trecho dinâmico em `<Suspense>`.
2. Cache o catálogo por 1 hora e invalide com `updateTag` quando um produto for editado.
3. Provoque o erro de "dado dinâmico fora de Suspense" e corrija-o.

---

## Módulo 12 · Recursos avançados

### 12.1 Parallel Routes e Intercepting Routes (modal com URL própria)

```
app/
├── layout.tsx                       ← recebe { children, modal }
├── page.tsx                         ← galeria
├── @modal/
│   ├── default.tsx                  ← return null (obrigatório em cada slot)
│   └── (.)foto/[id]/page.tsx        ← INTERCEPTA /foto/[id] durante a navegação
└── foto/[id]/page.tsx               ← página completa (acesso direto / F5 / compartilhar)
```

```tsx
// app/layout.tsx
export default function Layout({
  children,
  modal,
}: {
  children: React.ReactNode;
  modal: React.ReactNode;     // slot @modal
}) {
  return (
    <html lang="pt-BR">
      <body>
        {children}
        {modal}
      </body>
    </html>
  );
}
```

```tsx
// app/@modal/(.)foto/[id]/page.tsx
import { Modal } from "@/components/Modal";

export default async function FotoModal({ params }: { params: Promise<{ id: string }> }) {
  const { id } = await params;
  return (
    <Modal>   {/* Client Component que chama router.back() ao fechar */}
      <Foto id={id} />
    </Modal>
  );
}
```

Convenções de interceptação: `(.)` mesmo nível, `(..)` um nível acima, `(..)(..)` dois níveis, `(...)` a partir da raiz de `app`.

**Resultado**: clicar numa foto da galeria abre um modal (URL `/foto/12`), mas abrir o mesmo link diretamente mostra a página completa, o comportamento do Instagram/Pinterest.

### 12.2 `after()`: trabalho após a resposta

Executa tarefas (logs, analytics, e-mail) **depois** de enviar a resposta, sem atrasar o usuário.

```ts
import { after } from "next/server";

export async function POST(request: Request) {
  const dados = await request.json();
  const pedido = await criarPedido(dados);

  after(async () => {
    await enviarEmailConfirmacao(pedido);
    await registrarAnalytics("pedido_criado", pedido.id);
  });

  return Response.json(pedido, { status: 201 });
}
```

### 12.3 Internacionalização (i18n)

O App Router não traz roteamento de idiomas pronto. O padrão é um segmento `[lang]` + detecção no `proxy.ts`.

```
app/
└── [lang]/
    ├── layout.tsx
    ├── page.tsx
    └── dictionaries/
        ├── pt-BR.json
        └── en.json
```

```ts
// proxy.ts
import { NextResponse, type NextRequest } from "next/server";

const locales = ["pt-BR", "en"];
const padrao = "pt-BR";

function detectar(request: NextRequest) {
  const preferido = request.headers.get("accept-language")?.split(",")[0]?.trim().toLowerCase();
  return locales.find((l) => l.toLowerCase() === preferido) ?? padrao;
}

export function proxy(request: NextRequest) {
  const { pathname } = request.nextUrl;
  const temLocale = locales.some((l) => pathname === `/${l}` || pathname.startsWith(`/${l}/`));
  if (temLocale) return NextResponse.next();

  request.nextUrl.pathname = `/${detectar(request)}${pathname}`;
  return NextResponse.redirect(request.nextUrl);
}

export const config = { matcher: ["/((?!_next|api|.*\\..*).*)"] };
```

```ts
// app/[lang]/dictionaries.ts
import "server-only";

const dicionarios = {
  "pt-BR": () => import("./dictionaries/pt-BR.json").then((m) => m.default),
  en: () => import("./dictionaries/en.json").then((m) => m.default),
};

export type Locale = keyof typeof dicionarios;
export const getDictionary = async (lang: Locale) => dicionarios[lang]();
```

```tsx
// app/[lang]/page.tsx
export default async function Home({ params }: { params: Promise<{ lang: Locale }> }) {
  const { lang } = await params;
  const t = await getDictionary(lang);
  return <h1>{t.home.titulo}</h1>;
}
```

Em produção, considere a biblioteca **next-intl**, que cuida de plurais, formatação de datas/números e tipagem.

### 12.4 Cabeçalhos de segurança, redirecionamentos e rewrites

```ts
// next.config.ts
const nextConfig: NextConfig = {
  async headers() {
    return [
      {
        source: "/(.*)",
        headers: [
          { key: "X-Frame-Options", value: "DENY" },
          { key: "X-Content-Type-Options", value: "nosniff" },
          { key: "Referrer-Policy", value: "strict-origin-when-cross-origin" },
          { key: "Strict-Transport-Security", value: "max-age=63072000; includeSubDomains; preload" },
        ],
      },
    ];
  },

  async redirects() {
    return [{ source: "/antigo/:slug", destination: "/blog/:slug", permanent: true }];
  },

  async rewrites() {
    // proxy para uma API externa, escondendo a URL real
    return [{ source: "/api-externa/:path*", destination: "https://api.exemplo.com/:path*" }];
  },
};
```

Para uma **Content-Security-Policy** com *nonce* por requisição, gere o nonce no `proxy.ts` e propague-o por cabeçalhos (veja o guia de CSP na documentação oficial).

### 12.5 React Compiler

Memoiza automaticamente componentes e valores, e você deixa de escrever `useMemo`, `useCallback` e `memo` à mão na maioria dos casos.

```bash
npm install -D babel-plugin-react-compiler
```

```ts
// next.config.ts
const nextConfig: NextConfig = {
  reactCompiler: true,
};
```

### 12.6 Rotas tipadas

```ts
// next.config.ts
const nextConfig: NextConfig = { typedRoutes: true };
```

```tsx
<Link href="/blog/meu-post">OK</Link>
<Link href="/rota-que-nao-existe">Erro de compilação</Link>
```

### 12.7 Instrumentação e observabilidade

```ts
// instrumentation.ts (raiz ou src/)
import { registerOTel } from "@vercel/otel";

export function register() {
  registerOTel({ serviceName: "meu-app" });   // traces OpenTelemetry automáticos
}

// Captura erros do servidor (integre com Sentry, Datadog etc.)
export async function onRequestError(
  err: unknown,
  request: { path: string; method: string },
  context: { routerKind: string; routePath: string; routeType: string }
) {
  console.error("Erro no servidor", { err, request, context });
}
```

### 12.7.1 Tratamento de erros esperados vs. inesperados

```ts
// Erros ESPERADOS (validação, regra de negócio): retorne como valor, não lance exceção
export async function pagar(pedidoId: string) {
  const pedido = await buscar(pedidoId);
  if (pedido.status === "pago") return { ok: false as const, erro: "Pedido já pago" };
  // ...
  return { ok: true as const };
}

// Erros INESPERADOS (bugs, indisponibilidade): lance exceção e deixe o error.tsx tratar
```

### 12.8 Tempo real

Funções *serverless* não mantêm WebSockets abertos. Opções:

| Necessidade | Solução |
|-------------|---------|
| Servidor → cliente (notificações, progresso) | **SSE** via Route Handler |
| Bidirecional / chat | Serviço gerenciado (Pusher, Ably, Supabase Realtime) ou servidor Node dedicado com WebSocket |
| Atualizações periódicas | Polling com SWR/TanStack Query (`refreshInterval`) |

### 12.9 Outros tópicos para aprofundamento

| Tema | O que estudar |
|------|---------------|
| IA | Vercel AI SDK, streaming de respostas de LLMs com Route Handlers e Server Actions |
| Monorepo | Turborepo, pacotes compartilhados de UI e configuração |
| Headless CMS | Sanity, Contentful, Payload (integrado ao Next.js) |
| E-commerce | Stripe Checkout, webhooks, carrinho com cookies |
| Feature flags | Edge Config, LaunchDarkly, Vercel Flags |
| Adapters | Build Adapters API para plataformas de deploy customizadas |

---

## Módulo 13 · Performance e otimização

### 13.1 Métricas que importam (Core Web Vitals)

| Métrica | Mede | Meta |
|---------|------|------|
| **LCP**: Largest Contentful Paint | Velocidade de carregamento do maior elemento | ≤ 2,5 s |
| **INP**: Interaction to Next Paint | Responsividade às interações | ≤ 200 ms |
| **CLS**: Cumulative Layout Shift | Estabilidade visual | ≤ 0,1 |

```tsx
// Coletando no cliente
"use client";
import { useReportWebVitals } from "next/web-vitals";

export function WebVitals() {
  useReportWebVitals((metrica) => {
    navigator.sendBeacon("/api/vitals", JSON.stringify(metrica));
  });
  return null;
}
```

### 13.2 Checklist de otimização

**Menos JavaScript no cliente**
- Mantenha componentes como Server Components e use `"use client"` apenas nas folhas.
- Importe só o que usa; evite bibliotecas pesadas (prefira `date-fns` a `moment`).
- Configure `experimental.optimizePackageImports` para bibliotecas com muitos *exports*.

**Carregamento sob demanda**

```tsx
import dynamic from "next/dynamic";

const Grafico = dynamic(() => import("@/components/Grafico"), {
  loading: () => <p>Carregando gráfico...</p>,
});
// Em Client Components, também é possível usar { ssr: false } para widgets dependentes do navegador
```

**Ativos**
- `next/image` com `sizes` corretos e `priority` só no LCP.
- `next/font` para evitar *layout shift* e requisições externas.
- `next/script` com `strategy="lazyOnload"` para scripts não críticos.

**Dados**
- Requisições em paralelo (`Promise.all`) e streaming com `<Suspense>`.
- Cache explícito (`"use cache"`) para dados compartilhados.
- No banco: índices, paginação, seleção só dos campos necessários, *connection pooling*.

**Rede e navegação**
- `<Link>` faz *prefetch* automático dos links visíveis em produção.
- Use CDN para ativos e páginas estáticas.

### 13.3 Diagnóstico

```bash
npm run build         # tabela de rotas e tamanho dos bundles
```

- **Lighthouse** e **PageSpeed Insights** para métricas de laboratório.
- **Chrome DevTools → Performance** para investigar INP.
- Um analisador de bundle (por exemplo, `@next/bundle-analyzer`; verifique na documentação a opção compatível com o Turbopack da sua versão) para encontrar dependências pesadas.
- **React DevTools Profiler** para renderizações desnecessárias.
- **Dados reais de usuários** (RUM): Vercel Speed Insights, Sentry, Datadog.

### Exercícios do Módulo 13
1. Rode o Lighthouse numa página sua e liste três melhorias com maior impacto.
2. Carregue um componente pesado com `dynamic()` e compare o tamanho do bundle.
3. Reduza o JavaScript da página movendo `"use client"` para componentes menores.

---

## Módulo 14 · Testes

### 14.1 Estratégia

```
        /\        E2E (Playwright): fluxos críticos, Server Components async
       /──\       Integração: Route Handlers, Server Actions, DAL
      /────\      Unitários (Vitest + Testing Library): componentes, hooks, utilitários
```

> **Limitação importante**: Vitest/Jest **ainda não** suportam bem Server Components **assíncronos**. Teste-os com **E2E**. Componentes síncronos, Client Components, hooks e funções puras funcionam normalmente em testes unitários.

### 14.2 Vitest + React Testing Library

```bash
npm install -D vitest @vitejs/plugin-react jsdom vite-tsconfig-paths \
  @testing-library/react @testing-library/dom @testing-library/jest-dom @testing-library/user-event
```

```ts
// vitest.config.mts
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [tsconfigPaths(), react()],
  test: {
    environment: "jsdom",
    setupFiles: ["./vitest.setup.ts"],
  },
});
```

```ts
// vitest.setup.ts
import "@testing-library/jest-dom/vitest";
```

```json
{ "scripts": { "test": "vitest", "test:run": "vitest run" } }
```

**Teste de componente**

```tsx
// components/Contador.test.tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { expect, test } from "vitest";
import { Contador } from "./Contador";

test("incrementa ao clicar", async () => {
  const user = userEvent.setup();
  render(<Contador />);

  await user.click(screen.getByRole("button", { name: "+1" }));

  expect(screen.getByText("Valor: 1")).toBeInTheDocument();
});
```

### 14.3 Testando Server Actions

```ts
// app/produtos/actions.test.ts
import { describe, it, expect, vi, beforeEach } from "vitest";

vi.mock("@/lib/dal", () => ({
  verifySession: vi.fn().mockResolvedValue({ userId: "u1", papel: "USER" }),
}));
vi.mock("@/lib/db", () => ({ db: { produto: { create: vi.fn() } } }));
vi.mock("next/cache", () => ({ updateTag: vi.fn() }));
vi.mock("next/navigation", () => ({ redirect: vi.fn() }));

import { criarProduto } from "./actions";
import { db } from "@/lib/db";

function form(dados: Record<string, string>) {
  const fd = new FormData();
  Object.entries(dados).forEach(([k, v]) => fd.append(k, v));
  return fd;
}

describe("criarProduto", () => {
  beforeEach(() => vi.clearAllMocks());

  it("retorna erros quando os dados são inválidos", async () => {
    const r = await criarProduto(undefined, form({ nome: "", preco: "-1", categoria: "" }));

    expect(r?.errors?.nome).toBeDefined();
    expect(r?.errors?.preco).toBeDefined();
    expect(db.produto.create).not.toHaveBeenCalled();
  });

  it("cria o produto quando válido", async () => {
    await criarProduto(undefined, form({ nome: "Mouse", preco: "50", categoria: "perifericos" }));

    expect(db.produto.create).toHaveBeenCalledWith({
      data: { nome: "Mouse", preco: 50, categoria: "perifericos" },
    });
  });
});
```

### 14.4 Testando Route Handlers

```ts
// app/api/produtos/route.test.ts
import { NextRequest } from "next/server";
import { expect, it, vi } from "vitest";

vi.mock("@/lib/db", () => ({
  db: { produto: { findMany: vi.fn().mockResolvedValue([{ id: 1, nome: "Mouse" }]) } },
}));

import { GET } from "./route";

it("lista produtos", async () => {
  const res = await GET(new NextRequest("http://localhost/api/produtos?categoria=perifericos"));

  expect(res.status).toBe(200);
  expect(await res.json()).toEqual([{ id: 1, nome: "Mouse" }]);
});
```

### 14.5 E2E com Playwright

```bash
npm init playwright@latest
```

```ts
// playwright.config.ts
import { defineConfig } from "@playwright/test";

export default defineConfig({
  testDir: "./e2e",
  use: { baseURL: "http://localhost:3000" },
  webServer: {
    command: "npm run build && npm run start",   // teste contra o build de produção
    url: "http://localhost:3000",
    reuseExistingServer: !process.env.CI,
  },
});
```

```ts
// e2e/login.spec.ts
import { test, expect } from "@playwright/test";

test("usuário faz login e vê o dashboard", async ({ page }) => {
  await page.goto("/login");
  await page.getByLabel("E-mail").fill("ana@exemplo.com");
  await page.getByLabel("Senha").fill("senha-segura-123");
  await page.getByRole("button", { name: "Entrar" }).click();

  await expect(page).toHaveURL("/dashboard");
  await expect(page.getByRole("heading", { name: /olá, ana/i })).toBeVisible();
});

test("rota protegida redireciona anônimos", async ({ page }) => {
  await page.goto("/dashboard");
  await expect(page).toHaveURL(/\/login/);
});
```

### 14.6 Boas práticas

- Teste **comportamento** (o que o usuário vê/faz), não detalhes de implementação.
- Prefira seletores acessíveis (`getByRole`, `getByLabel`).
- Use bancos de teste isolados (containers) nos testes de integração e E2E.
- Rode lint, checagem de tipos (`tsc --noEmit`), testes e build no CI.

---

## Módulo 15 · Deploy e produção

### 15.1 Opções de hospedagem

| Opção | Quando usar |
|-------|-------------|
| **Vercel** | Caminho mais simples: *git push*, preview por PR, CDN e funções gerenciadas |
| **Node.js (VPS/PaaS)** | `next build` + `next start`: controle total (Railway, Render, Fly.io) |
| **Docker/Kubernetes** | Ambientes corporativos e multi-região |
| **Exportação estática** | `output: "export"`: site 100% estático, sem recursos de servidor |
| **Serverless na nuvem** | AWS/Cloudflare/Netlify via adaptadores da plataforma |

### 15.2 Docker com `standalone`

```ts
// next.config.ts
const nextConfig: NextConfig = { output: "standalone" };   // gera um servidor mínimo com só o necessário
```

```dockerfile
FROM node:22-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

FROM node:22-alpine AS build
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
ENV NEXT_TELEMETRY_DISABLED=1
RUN npm run build

FROM node:22-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production PORT=3000 HOSTNAME=0.0.0.0 NEXT_TELEMETRY_DISABLED=1
RUN addgroup -S app && adduser -S app -G app

COPY --from=build /app/public ./public
COPY --from=build --chown=app:app /app/.next/standalone ./
COPY --from=build --chown=app:app /app/.next/static ./.next/static

USER app
EXPOSE 3000
CMD ["node", "server.js"]
```

```yaml
# docker-compose.yml
services:
  web:
    build: .
    ports: ["3000:3000"]
    environment:
      DATABASE_URL: postgresql://postgres:segredo@db:5432/loja
      SESSION_SECRET: ${SESSION_SECRET}
    depends_on: [db]
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_PASSWORD: segredo
      POSTGRES_DB: loja
```

### 15.3 Pontos de atenção ao hospedar por conta própria

- **`NEXT_PUBLIC_*` é embutida no build**. Para mudar o valor, reconstrua a imagem.
- **Várias instâncias**: o cache em disco local não é compartilhado. Configure um *cache handler* compartilhado (Redis, S3 etc.), mantenha o mesmo **Build ID** entre instâncias e defina uma chave fixa `NEXT_SERVER_ACTIONS_ENCRYPTION_KEY` para que as Server Actions funcionem em todas.
- **Proxy reverso (Nginx)**: desative o *buffering* para que o *streaming* funcione (cabeçalho `X-Accel-Buffering: no`).
- **Imagens**: `next/image` usa `sharp` (instalado automaticamente em produção).
- **Health check**:

```ts
// app/api/health/route.ts: handlers GET não são cacheados por padrão
export async function GET() {
  return Response.json({ status: "ok", uptime: process.uptime() });
}
```

### 15.4 CI/CD com GitHub Actions

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
      - run: npm run lint
      - run: npx tsc --noEmit
      - run: npm run test:run
      - run: npm run build
        env:
          SESSION_SECRET: ci-segredo-de-teste
      - run: npx playwright install --with-deps chromium
      - run: npx playwright test
```

### 15.5 Checklist de produção

- [ ] `next` e dependências atualizados; auditoria (`npm audit`, Dependabot).
- [ ] Variáveis de ambiente validadas na inicialização (ex.: schema Zod).
- [ ] Segredos fora do repositório; `.env*` no `.gitignore`.
- [ ] Autorização em DAL, Server Actions e Route Handlers (não só no `proxy.ts`).
- [ ] Cabeçalhos de segurança e CSP configurados.
- [ ] `error.tsx`, `not-found.tsx` e `global-error.tsx` implementados.
- [ ] Monitoramento de erros (Sentry etc.) e *traces* (OpenTelemetry).
- [ ] Metadados, `sitemap.ts`, `robots.ts` e imagens OG.
- [ ] Core Web Vitals medidos em dados reais.
- [ ] Estratégia de cache definida e invalidação testada.
- [ ] *Rate limiting* em rotas sensíveis (login, formulários, APIs públicas).
- [ ] Backups do banco e plano de *rollback* de deploy.
- [ ] Testes (unitários, integração e E2E) rodando no CI.

---

## Anexo A · Pages Router (legado)

Muitos projetos existentes usam o **Pages Router** (`pages/`). Ele continua suportado, e é possível **migrar aos poucos**, pois `pages/` e `app/` podem coexistir.

```tsx
// pages/blog/[slug].tsx
import type { GetStaticPaths, GetStaticProps } from "next";

export const getStaticPaths: GetStaticPaths = async () => {
  const posts = await listarPosts();
  return { paths: posts.map((p) => ({ params: { slug: p.slug } })), fallback: "blocking" };
};

export const getStaticProps: GetStaticProps = async ({ params }) => {
  const post = await buscarPost(params!.slug as string);
  if (!post) return { notFound: true };
  return { props: { post }, revalidate: 60 };   // ISR
};

export default function Post({ post }: { post: Post }) {
  return <article><h1>{post.titulo}</h1></article>;
}
```

| Pages Router | App Router |
|--------------|-----------|
| `getStaticProps` | Server Component com dados no build / `"use cache"` |
| `getServerSideProps` | Server Component dinâmico (usa `cookies()`, `headers()`...) |
| `getStaticPaths` | `generateStaticParams` |
| `pages/api/*.ts` | `app/**/route.ts` |
| `_app.tsx` / `_document.tsx` | `app/layout.tsx` |
| `next/head` | `metadata` / `generateMetadata` |
| `next/router` | `next/navigation` |
| `middleware.ts` | `proxy.ts` |

**Migração incremental**: crie `app/` ao lado de `pages/`, mova rotas uma a uma (uma rota não pode existir nos dois), e comece pelas páginas mais simples.

---

## Plano de estudos sugerido

| Etapa | Conteúdo | Duração sugerida |
|-------|----------|------------------|
| 1 | Módulo 1: JavaScript moderno e TypeScript | 2 semanas |
| 2 | Módulo 2: React essencial | 3 semanas |
| 3 | Módulos 3 e 4: Primeiros passos e roteamento | 2 semanas |
| 4 | Módulo 5: Server e Client Components | 1 semana |
| 5 | Módulo 6: Dados, renderização e streaming | 2 semanas |
| 6 | Módulos 7 e 8: Server Actions e Route Handlers | 2 semanas |
| 7 | Módulo 9: Estilo, imagens, fontes e SEO | 1 semana |
| 8 | Módulo 10: Banco de dados e autenticação | 3 semanas |
| 9 | Módulo 11: Cache e Cache Components | 1 semana |
| 10 | Módulos 12 e 13: Avançado e performance | 3 semanas |
| 11 | Módulos 14 e 15: Testes e deploy | 2 semanas |

### Projeto final sugerido: **Loja virtual com painel administrativo**

Requisitos para aplicar tudo o que foi aprendido:

1. **Catálogo público**: listagem com filtros e paginação via query string, página de produto com `generateMetadata`, `sitemap.ts` e `"use cache"` com invalidação por tag.
2. **Autenticação**: cadastro, login e logout com sessão em cookie; DAL e `proxy.ts`; papéis `USER` e `ADMIN`.
3. **Carrinho e pedidos**: Server Actions com validação Zod, atualização otimista (`useOptimistic`) e checagem de estoque.
4. **Painel administrativo**: CRUD de produtos (com upload de imagem), listagem de pedidos e métricas em streaming com `<Suspense>`.
5. **Integrações**: pagamento (Stripe ou simulado) com webhook assinado em Route Handler e envio de e-mail via `after()`.
6. **UX avançada**: modal de pré-visualização com *parallel* e *intercepting routes*; `loading.tsx` e `error.tsx` em todas as seções.
7. **i18n** (desafio): português e inglês com `[lang]`.
8. **Qualidade**: testes unitários (Vitest), testes de Server Actions e E2E (Playwright) do fluxo de compra.
9. **Operação**: Dockerfile `standalone`, CI no GitHub Actions, monitoramento de erros e Core Web Vitals acima das metas.
10. **Documentação**: README com decisões de arquitetura e justificativas de cache.

---

## Referências recomendadas

- **Documentação oficial**: https://nextjs.org/docs · https://react.dev · https://www.typescriptlang.org/docs
- **Curso gratuito oficial**: https://nextjs.org/learn (projeto guiado com App Router)
- **Blog do Next.js**: notas de cada versão e guias de migração (consulte sempre ao atualizar)
- **Performance**: https://web.dev (Core Web Vitals)
- **Segurança**: OWASP Top 10 e o guia de autenticação da documentação do Next.js
- **Livros e cursos**: *Learning React* (Alex Banks e Eve Porcello), *Total TypeScript* (Matt Pocock), Patterns.dev
- **Prática**: reproduza projetos reais (clone de uma loja, de um blog com CMS, de um SaaS) e contribua com projetos open source.

> **Aviso sobre versões**: o Next.js evolui rápido, e APIs de cache e roteamento mudaram bastante entre as versões 13, 14, 15 e 16. Ao seguir um tutorial, confirme **a versão** utilizada e compare com a documentação atual.
>
> Dica final: **construa, quebre e refatore**. Cada módulo deste material ganha sentido de verdade quando aplicado em um projeto seu.

