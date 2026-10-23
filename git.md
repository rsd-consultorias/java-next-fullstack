# 🚀 Guia Prático do Git: Resolução de Situações Corriqueiras

Este guia foi elaborado para servir como consulta rápida e ambiente de treino para os problemas mais comuns enfrentados no dia a dia de desenvolvimento utilizando Git.

---

## 📑 Sumário
1. [Desfazer Alterações Locais (Antes do Commit)](#1-desfazer-alterações-locais-antes-do-commit)
2. [Alterar a Mensagem ou Conteúdo do Último Commit](#2-alterar-a-mensagem-ou-conteúdo-do-último-commit)
3. [Reverter um Commit Já Publicado (Remote)](#3-reverter-um-commit-já-publicado-remote)
4. [Salvar Trabalho Provisoriamente (Git Stash)](#4-salvar-trabalho-provisoriamente-git-stash)
5. [Pegar Apenas Um Commit Especifico de Outra Branch (Cherry-pick)](#5-pegar-apenas-um-commit-especifico-de-outra-branch-cherry-pick)
6. [Resolvendo Conflitos de Merge](#6-resolvendo-conflitos-de-merge)
7. [Limpar Branches Locais Antigas](#7-limpar-branches-locais-antigas)

---

## 1. Desfazer Alterações Locais (Antes do Commit)

### 🎯 Cenário
Você alterou arquivos no código, mas percebeu que fez um teste sem sentido ou modificou algo errado e quer voltar exatamente ao estado anterior.

### 🧪 Exercício para Treinar
1. Crie ou modifique um arquivo:
   ```bash
   echo "Texto temporario" >> teste.txt
   ```
2. Verifique o status:
   ```bash
   git status
   ```

### 🛠️ Como Resolver

#### A. Se os arquivos NÃO foram adicionados no staging (não deu `git add`)
Para descartar alterações em um arquivo específico:
```bash
git checkout -- teste.txt
# ou no Git mais recente:
git restore teste.txt
```

Para descartar alterações de **todos** os arquivos modificados:
```bash
git restore .
```

#### B. Se os arquivos JÁ FORAM adicionados no staging (`git add`)
Primeiro remova do staging, depois descarte as mudanças:
```bash
# Unstage do arquivo
git restore --staged teste.txt

# Descartar alterações do arquivo
git restore teste.txt
```

---

## 2. Alterar a Mensagem ou Conteúdo do Último Commit

### 🎯 Cenário
Você fez um commit, mas digitou a mensagem errada (ex: erro de digitação) ou esqueceu de incluir um arquivo importante.

### 🧪 Exercício para Treinar
1. Crie um arquivo e commite:
   ```bash
   echo "Conteudo inicial" > app.js
   git add app.js
   git commit -m "feat: adiciona ap.js" # Mensagem com erro de digitação
   ```

### 🛠️ Como Resolver

#### A. Para corrigir apenas a mensagem
```bash
git commit --amend -m "feat: adiciona app.js"
```

#### B. Para incluir um arquivo esquecido no mesmo commit
```bash
# Altere ou crie o arquivo esquecido
echo "const config = {};" > config.js

# Adicione ao staging
git add config.js

# Refaça o último commit incorporando a nova mudança
git commit --amend --no-edit
```
> ⚠️ **Atenção:** Só utilize `--amend` se o commit ainda **não** tiver sido enviado para o repositório remoto (`git push`).

---

## 3. Reverter um Commit Já Publicado (Remote)

### 🎯 Cenário
Um commit com bug foi enviado para o repositório remoto (GitHub/GitLab) e você precisa desfazer essa alteração sem apagar o histórico público da equipe.

### 🧪 Exercício para Treinar
1. Crie um commit "ruim":
   ```bash
   echo "bug aqui" > bug.txt
   git add bug.txt
   git commit -m "feat: adiciona bug"
   ```

### 🛠️ Como Resolver
O comando `git revert` cria um **novo commit** que faz exatamente o inverso do commit selecionado:

1. Identifique o hash do commit a ser desfeito:
   ```bash
   git log --oneline
   ```
2. Execute o revert usando o hash:
   ```bash
   git revert <hash-do-commit>
   ```
3. O Git abrirá um editor para confirmar a mensagem do commit de reversão. Salve e feche.
4. Envie a alteração para o remoto:
   ```bash
   git push origin main
   ```

---

## 4. Salvar Trabalho Provisoriamente (Git Stash)

### 🎯 Cenário
Você está trabalhando no meio de uma funcionalidade na branch `feature-A`, mas surge uma urgência para corrigir um bug na branch `main`. Você não quer commit a metade do trabalho feito.

### 🧪 Exercício para Treinar
1. Crie uma alteração qualquer sem commit:
   ```bash
   echo "trabalho em andamento..." >> rascunho.txt
   ```

### 🛠️ Como Resolver

1. **Guarde as alterações na "pilha" (stash):**
   ```bash
   git stash -u
   # A flag -u inclui arquivos não rastreados (untracked)
   ```
   *Agora seu diretório de trabalho está limpo para você mudar de branch.*

2. **Mude de branch e resolva a urgência:**
   ```bash
   git checkout main
   # (faça o trabalho urgente...)
   ```

3. **Volte para sua branch de trabalho:**
   ```bash
   git checkout feature-A
   ```

4. **Recupere suas alterações salvas:**
   ```bash
   git stash pop
   ```

---

## 5. Pegar Apenas Um Commit Especifico de Outra Branch (Cherry-pick)

### 🎯 Cenário
Seu colega fez a correção de um bug na branch `bugfix` que você precisa urgentemente na sua branch `feature`, mas você não quer trazer todos os commits da branch dele.

### 🧪 Exercício para Treinar
1. Crie uma branch temporária e faça um commit nela:
   ```bash
   git checkout -b branch-auxiliar
   echo "correcao importante" > fix.txt
   git add fix.txt
   git commit -m "fix: resolve problema X"
   ```
2. Pegue o hash deste commit no `git log --oneline` (ex: `a1b2c3d`).
3. Volte para a sua branch principal:
   ```bash
   git checkout main
   ```

### 🛠️ Como Resolver
Aplique o commit desejado na sua branch atual com o comando:

```bash
git cherry-pick <hash-do-commit>
```

---

## 6. Resolvendo Conflitos de Merge

### 🎯 Cenário
Duas pessoas alteraram a mesma linha do mesmo arquivo em branches diferentes, e o Git não consegue decidir qual versão manter.

### 🧪 Exercício para Treinar
1. Na branch `main`, crie um arquivo e comite:
   ```bash
   echo "Linha original" > arquivo.txt
   git add arquivo.txt
   git commit -m "docs: cria arquivo base"
   ```
2. Crie a `branch-A`, mude o arquivo e comite:
   ```bash
   git checkout -b branch-A
   echo "Texto da Branch A" > arquivo.txt
   git commit -am "feat: altera arquivo na branch A"
   ```
3. Volte para a `main`, crie a `branch-B` a partir da `main`, mude a mesma linha e comite:
   ```bash
   git checkout main
   git checkout -b branch-B
   echo "Texto da Branch B" > arquivo.txt
   git commit -am "feat: altera arquivo na branch B"
   ```
4. Tente fazer o merge da `branch-A` na `branch-B`:
   ```bash
   git merge branch-A
   ```
   *(O Git avisará sobre o conflito)*

### 🛠️ Como Resolver

1. Abra o arquivo com conflito (`arquivo.txt`). Você verá demarcações como:
   ```text
   <<<<<<< HEAD
   Texto da Branch B
   =======
   Texto da Branch A
   >>>>>>> branch-A
   ```

2. Edite o arquivo manualmente deixando apenas a versão final desejada (removendo as linhas do Git `<<<<<<<`, `=======`, `>>>>>>>`).

3. Salve o arquivo.

4. Marque o conflito como resolvido e conclua o merge:
   ```bash
   git add arquivo.txt
   git commit -m "fix: resolve conflito de merge entre branch-A e branch-B"
   ```

---

## 7. Limpar Branches Locais Antigas

### 🎯 Cenário
Com o tempo, o acúmulo de branches locais já unificadas (merged) pode poluir seu ambiente local.

### 🧪 Exercício para Treinar
1. Crie uma branch temporária:
   ```bash
   git checkout -b feature-antiga
   git checkout main
   ```

### 🛠️ Como Resolver

#### A. Deletar uma branch local que já passou por merge
```bash
git branch -d feature-antiga
```

#### B. Forçar a exclusão de uma branch local (mesmo sem merge)
```bash
git branch -D feature-antiga
```

#### C. Limpar referências locais a branches remotas que foram apagadas no servidor
```bash
git fetch --prune
```