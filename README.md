# GDrive macOS style

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
| Dados | linhas `tr[role=row][data-id]` da lista nativa; tipo vem do `aria-label`, ícones são clonados. Em Computadores, cards `div[role=gridcell][data-id]` |
| Navegação | `history.replaceState` + `popstate` faz o Drive abrir a pasta sem recarregar |
| Ações | cliques, menu de contexto e teclas são disparados na linha nativa correspondente |
| Camadas | colunas em `z-index: 5` — acima da lista (contexto 0), abaixo dos menus do Drive (8) |

## Proteção contra mudanças do Google

A extensão depende do HTML interno do Drive. Em **Meu Drive e nas pastas**, ela confere o tempo todo se o que precisa ainda existe.
Se algo falhar, as colunas se **desligam sozinhas**, aparece um aviso e a lista normal do Drive volta. Clicar em **Colunas** tenta de novo.

| Checagem | Desliga quando |
|---|---|
| Área de arquivos (`div[role=main]`) | some por 15 s |
| Botões Lista/Grade (`button[data-test-id="list-layout-button"]`) | somem por 8 s (sem eles não haveria como desligar as colunas) |
| Linhas da lista (`tr[role=row][data-id]`) | há itens na tela, mas nenhum nesse formato, por 6 s |
| Navegação por URL | o Drive não abre duas pastas seguidas |
| Erros da própria extensão | 5 erros em 20 s |

O motivo fica no console (`[GDrive macOS style]`) e em `<html data-gdf-health="…">` (`ok` quando está tudo bem).

## Autoteste

Abra **https://drive.google.com/drive/my-drive#gdf-teste**. Em poucos segundos a extensão abre Meu Drive, liga as colunas,
entra numa pasta e numa subpasta, e mostra num painel o que passou (✓) e o que quebrou (✕), etapa por etapa.
Se as colunas estavam desligadas, ela volta a desligá-las no fim.

Para automatizar: dispare `document.dispatchEvent(new Event('gdf-selftest'))` na página e espere
`<html data-gdf-test>` virar `ok` ou `falhou`; o relatório completo fica em `data-gdf-test-report` (JSON).

## Limitações

- Depende do HTML interno do Drive; quando o Google muda, a proteção acima desliga as colunas e o autoteste mostra o que precisa de ajuste.
- Cada coluna nova leva o tempo do Drive abrir a pasta (~0,5 s).
- Arrastar itens entre colunas não está disponível — use Mover (⌥⌘M) ou o menu.
- A seção "Pessoal/Início" continua na visualização nativa.
- Em Computadores o Drive não mostra os botões Lista/Grade, então o botão Colunas também não aparece lá: as colunas seguem ligadas ou desligadas conforme o resto do Drive.
