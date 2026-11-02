# Drive Finder

Extensão do Chrome que adiciona ao Google Drive uma **visualização em colunas no estilo do Finder**.
Não usa API nem credenciais: lê os itens da própria lista do Drive e repassa seleção, menus e atalhos
para a lista nativa, que continua existindo (escondida) embaixo das colunas.

## Instalação

`chrome://extensions` → ative **Modo do desenvolvedor** → **Carregar sem compactação** → selecione a pasta `extension/`.
Abra o Drive e clique no botão **Colunas** ao lado de Lista/Grade.

## O que faz

- Barra lateral, cabeçalho (com o caminho da pasta) e botão **Novo** do Drive intactos
- Navegação por colunas com mouse e teclado; colunas redimensionáveis (⌥ = todas)
- Pré-visualização (miniatura real) + informações do arquivo
- Barra de seleção, menu de contexto, diálogos e visualizador **nativos do Drive**
- Abrindo uma pasta profunda pela URL, monta o caminho completo a partir do breadcrumb
- Pastas grandes são carregadas por completo automaticamente (o Drive pagina de 50 em 50)
- Funciona em: Meu Drive, pastas, Compartilhados comigo, Recentes, Com estrela, Lixeira, Drives compartilhados, Computadores e busca

## Atalhos

| Tecla | Ação |
|---|---|
| ↑ ↓ ← → | navegar |
| letras | ir para o item pelo nome |
| Espaço | visualizar (visualizador do Drive) |
| ⌘O / ⌘↓ / duplo clique | abrir |
| Enter | renomear (diálogo do Drive) |
| ⌘⌫ / Delete | mover para a lixeira |
| ⌘C / ⌘X / ⌘V | copiar / recortar / colar (do Drive) |
| ⌥⌘ + tecla | atalhos do Drive (⌥⌘A compartilhar, ⌥⌘M mover, ⌥⌘S estrela…) |
| ⌘A, ⌘/Shift + clique | seleção múltipla |

## Como funciona

| Peça | Detalhe |
|---|---|
| Dados | linhas `tr[role=row][data-id]` da lista nativa; tipo vem do `aria-label`, ícones são clonados |
| Navegação | `history.replaceState` + `popstate` faz o Drive abrir a pasta sem recarregar |
| Ações | cliques, menu de contexto e teclas são disparados na linha nativa correspondente |
| Camadas | colunas em `z-index: 5` — acima da lista (contexto 0), abaixo dos menus do Drive (8) |

## Limitações

- Depende do HTML interno do Drive (`data-test-id="list-layout-button"`, `div[role=main]`, `tr[data-id]`); se o Google mudar, os seletores precisam de ajuste.
- Cada coluna nova leva o tempo do Drive abrir a pasta (~0,5 s).
- Arrastar itens entre colunas não está disponível — use Mover (⌥⌘M) ou o menu.
- A seção "Pessoal/Início" continua na visualização nativa.
