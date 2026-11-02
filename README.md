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
- **Arrastar e soltar**: mover itens entre colunas e enviar arquivos arrastados do computador (detalhes abaixo)
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
| ⌘C, depois ⌘V | fazer uma cópia (do Drive; o Drive não tem recortar) |
| ⌥⌘ + tecla | atalhos do Drive (⌥⌘A compartilhar, ⌥⌘M mover, ⌥⌘S estrela…) |
| ⌘A, ⌘/Shift + clique | seleção múltipla |

## Arrastar e soltar

| Gesto | Resultado |
|---|---|
| Arrastar item(ns) para uma pasta de qualquer coluna | move para essa pasta |
| Arrastar para o espaço vazio de uma coluna | move para a pasta daquela coluna |
| Parar ~1 s em cima de uma pasta durante o arraste | abre a pasta (como a "mola" do Finder) |
| Arrastar arquivos do computador para uma coluna ou pasta | envia para essa pasta (upload do Drive) |
| Arrastar item para outro app ou aba | entrega o link do arquivo |

- Arrastar um item selecionado leva a seleção inteira; um item não selecionado vai sozinho.
- Uma pasta não pode ir para dentro dela mesma, e soltar na pasta onde o item já está não faz nada.
- Não vale em Lixeira e Computadores.

**Como o mover funciona:** o Drive não tem recortar/colar (⌘C → ⌘V só faz uma cópia), e o arraste da lista
nativa só move para o que está visível na mesma lista. Em outra pasta ele **cria um atalho** em vez de mover.
Por isso a extensão usa a janela **Mover** do Drive (⌥⌘M): seleciona os itens na lista nativa, abre a janela,
cola o endereço da pasta de destino na busca dela e clica em "Mover" na linha da pasta. A janela trabalha **invisível
e sem bloquear cliques**. Enquanto isso, os itens ficam esmaecidos e uma etiqueta "Movendo…" aparece no rodapé das
colunas: menos de 1 s, ou uns 3–5 s na primeira vez depois de carregar a página. No fim aparece o aviso do Drive
com **Desfazer**. Se o Drive fizer alguma pergunta na janela, ela volta a aparecer.

**Como o envio funciona:** enquanto os arquivos passam sobre as colunas, a lista nativa já abre a pasta de destino
e recebe o `dragenter`/`dragover`. Ao soltar, o `drop` é repassado a ela, e o Drive faz o upload com o painel de
progresso no canto inferior direito. Se `dragover` e `drop` chegarem no mesmo instante, o Drive faz o upload mas
esconde o painel; por isso os dois chegam em momentos diferentes.
Se a pasta ainda não tinha aberto, os arquivos são enviados assim que ela abrir. Nesse caso pastas arrastadas
do computador não vão (o navegador só entrega o conteúdo delas durante o próprio "drop"), e um aviso pede para soltar de novo.

## Como funciona

| Peça | Detalhe |
|---|---|
| Dados | linhas `tr[role=row][data-id]` da lista nativa; tipo vem do `aria-label`, ícones são clonados. Em Computadores, cards `div[role=gridcell][data-id]` |
| Navegação | `history.replaceState` + `popstate` faz o Drive abrir a pasta sem recarregar |
| Ações | cliques, menu de contexto e teclas são disparados na linha nativa correspondente |
| Mover | janela Mover (`iframe` `/picker/minpick`, mesma origem): linhas `tr[data-target-folder-id]` e busca por URL da pasta |
| Upload | `dragenter`/`dragover`/`drop` repassados ao `[role=grid]` da lista nativa |
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
- Mover por arraste depende da janela Mover do Drive; se ela mudar, aparece um aviso e o caminho é usar Mover (⌥⌘M) no menu.
- A seção "Pessoal/Início" continua na visualização nativa.
- Em Computadores o Drive não mostra os botões Lista/Grade, então o botão Colunas também não aparece lá: as colunas seguem ligadas ou desligadas conforme o resto do Drive.
