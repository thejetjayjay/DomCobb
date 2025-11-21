# Instruções: Excluir Pastas do Git Subtree Push

Este documento contém instruções claras para excluir pastas específicas (como `Outputs/`) do `git subtree push` para o repositório público DomCobb, mantendo-as apenas no repositório privado Agents.

---

## Contexto

- **Repositório Privado (Agents)**: Contém todas as pastas, incluindo `Outputs/` e outras pastas sensíveis
- **Repositório Público (DomCobb)**: Deve receber apenas o conteúdo da pasta `DomCobb`, **excluindo** pastas específicas como `Outputs/`

---

## Método Recomendado: Usar Git Filter com Subtree

### Opção 1: Script Automatizado (Recomendado)

Crie um script que faz o push excluindo as pastas desejadas:

#### **Windows PowerShell** (`git-subtree-push-domcobb.ps1`)

```powershell
# Script para fazer git subtree push excluindo pastas específicas
# Uso: .\git-subtree-push-domcobb.ps1

# Pastas a excluir do push público
$excludeFolders = @("Outputs", "Internal Files")

# Verificar se estamos no diretório raiz do repositório
if (-not (Test-Path ".git")) {
    Write-Host "Erro: Execute este script no diretório raiz do repositório" -ForegroundColor Red
    exit 1
}

# Verificar se o remote domcobb existe
$remotes = git remote
if ($remotes -notcontains "domcobb") {
    Write-Host "Erro: Remote 'domcobb' não configurado" -ForegroundColor Red
    Write-Host "Configure com: git remote add domcobb <url>" -ForegroundColor Yellow
    exit 1
}

Write-Host "Preparando subtree push excluindo: $($excludeFolders -join ', ')" -ForegroundColor Cyan

# Criar uma branch temporária para o filtro
$tempBranch = "temp-subtree-filter-$(Get-Date -Format 'yyyyMMdd-HHmmss')"
git checkout -b $tempBranch

try {
    # Aplicar filtro para remover as pastas excluídas
    foreach ($folder in $excludeFolders) {
        Write-Host "Removendo pasta: $folder" -ForegroundColor Yellow
        git filter-branch --force --index-filter `
            "git rm -rf --cached --ignore-unmatch '$folder'" `
            --prune-empty --tag-name-filter cat -- --all
        
        # Limpar referências antigas
        git for-each-ref --format="%(refname)" refs/original/ | ForEach-Object { git update-ref -d $_ }
    }
    
    # Fazer o subtree push
    Write-Host "Fazendo subtree push para domcobb..." -ForegroundColor Cyan
    git subtree push --prefix=DomCobb domcobb main
    
    Write-Host "Subtree push concluído com sucesso!" -ForegroundColor Green
}
finally {
    # Voltar para a branch main e limpar
    git checkout main
    git branch -D $tempBranch
    git reflog expire --expire=now --all
    git gc --prune=now --aggressive
}
```

#### **Linux/Mac Bash** (`git-subtree-push-domcobb.sh`)

```bash
#!/bin/bash
# Script para fazer git subtree push excluindo pastas específicas
# Uso: ./git-subtree-push-domcobb.sh

# Pastas a excluir do push público
EXCLUDE_FOLDERS=("Outputs" "Internal Files")

# Verificar se estamos no diretório raiz
if [ ! -d ".git" ]; then
    echo "Erro: Execute este script no diretório raiz do repositório"
    exit 1
fi

# Verificar se o remote domcobb existe
if ! git remote | grep -q "domcobb"; then
    echo "Erro: Remote 'domcobb' não configurado"
    echo "Configure com: git remote add domcobb <url>"
    exit 1
fi

echo "Preparando subtree push excluindo: ${EXCLUDE_FOLDERS[*]}"

# Criar branch temporária
TEMP_BRANCH="temp-subtree-filter-$(date +%Y%m%d-%H%M%S)"
git checkout -b "$TEMP_BRANCH"

# Aplicar filtro para remover pastas excluídas
for folder in "${EXCLUDE_FOLDERS[@]}"; do
    echo "Removendo pasta: $folder"
    git filter-branch --force --index-filter \
        "git rm -rf --cached --ignore-unmatch '$folder'" \
        --prune-empty --tag-name-filter cat -- --all
    
    # Limpar referências antigas
    git for-each-ref --format="%(refname)" refs/original/ | xargs -n 1 git update-ref -d
done

# Fazer subtree push
echo "Fazendo subtree push para domcobb..."
git subtree push --prefix=DomCobb domcobb main

# Voltar para main e limpar
git checkout main
git branch -D "$TEMP_BRANCH"
git reflog expire --expire=now --all
git gc --prune=now --aggressive

echo "Subtree push concluído com sucesso!"
```

---

### Opção 2: Comando Manual (Mais Simples, Menos Automatizado)

Se preferir fazer manualmente, siga estes passos:

#### **Passo a Passo Manual**

1. **Criar uma branch temporária:**
   ```bash
   git checkout -b temp-subtree-exclude
   ```

2. **Remover as pastas do índice (sem deletar do disco):**
   ```bash
   # Para cada pasta a excluir:
   git rm -r --cached Outputs
   git rm -r --cached "Internal Files"
   ```

3. **Fazer commit das remoções:**
   ```bash
   git commit -m "chore: exclude Outputs and Internal Files from subtree push"
   ```

4. **Fazer o subtree push:**
   ```bash
   git subtree push --prefix=DomCobb domcobb main
   ```

5. **Voltar para main e limpar:**
   ```bash
   git checkout main
   git branch -D temp-subtree-exclude
   ```

**⚠️ Atenção:** Este método manual cria commits temporários que podem poluir o histórico. Use apenas se necessário.

---

### Opção 3: Usar Git Sparse-Checkout (Mais Avançado)

Para uma solução mais permanente, configure sparse-checkout:

```bash
# Habilitar sparse-checkout
git config core.sparseCheckout true

# Criar arquivo de configuração
echo "DomCobb/*" > .git/info/sparse-checkout
echo "!DomCobb/Outputs" >> .git/info/sparse-checkout
echo "!DomCobb/Internal Files" >> .git/info/sparse-checkout

# Aplicar sparse-checkout
git read-tree -mu HEAD

# Fazer subtree push
git subtree push --prefix=DomCobb domcobb main
```

**⚠️ Nota:** Este método afeta o checkout local. Use com cuidado.

---

## Método Mais Prático: Atualizar Git Agent

A melhor solução é atualizar o **Git Agent** para fazer isso automaticamente. O Git Agent já detecta o projeto DomCobb e executa o subtree push. Basta adicionar a lógica de exclusão.

### Instruções para Atualizar o Git Agent

No arquivo `Outputs/Git Agent.md`, na seção **"9) Special handling for DomCobb project"**, adicione antes do `git subtree push`:

```markdown
- Before executing `git subtree push`, temporarily remove excluded folders from the index:
  - Execute: `git rm -r --cached Outputs` (and any other folders to exclude)
  - This removes them from the index without deleting from disk
  - After the subtree push, restore them with: `git reset HEAD Outputs` (and other folders)
```

Ou use um script auxiliar que o Git Agent pode chamar.

---

## Adicionar Novas Pastas à Lista de Exclusão

Para adicionar novas pastas à exclusão no futuro:

1. **Se usando o script:** Edite a variável `$excludeFolders` (PowerShell) ou `EXCLUDE_FOLDERS` (Bash) e adicione o nome da nova pasta
2. **Se usando método manual:** Adicione `git rm -r --cached "NomeDaPasta"` antes do subtree push
3. **Se atualizar o Git Agent:** Adicione a pasta na lista de exclusão no código do agente

---

## Verificação

Após fazer o subtree push, verifique se as pastas foram excluídas:

```bash
# Verificar o que foi enviado para domcobb
git ls-tree -r domcobb/main --name-only | grep -E "(Outputs|Internal Files)"

# Se não retornar nada, as pastas foram excluídas corretamente
```

---

## Resumo Rápido

**Para uso imediato:**
1. Use o script automatizado (Opção 1) - mais seguro e reutilizável
2. Ou faça manualmente (Opção 2) - mais rápido, mas menos seguro

**Para uso futuro:**
1. Atualize o Git Agent para fazer isso automaticamente
2. Adicione novas pastas à lista de exclusão conforme necessário

---

## Exemplo de Uso do Script

```powershell
# Windows PowerShell
.\git-subtree-push-domcobb.ps1
```

```bash
# Linux/Mac
chmod +x git-subtree-push-domcobb.sh
./git-subtree-push-domcobb.sh
```

---

## Notas Importantes

- ⚠️ **Sempre teste em uma branch temporária primeiro**
- ⚠️ **As pastas são removidas apenas do índice Git, não do disco**
- ⚠️ **O histórico do repositório privado não é afetado**
- ⚠️ **O repositório público DomCobb receberá apenas o conteúdo sem as pastas excluídas**
- ✅ **As pastas continuam existindo no repositório privado Agents**

---

**Última atualização:** 2025-01-20  
**Criado para:** Phase 6 - Upload to GitHub

