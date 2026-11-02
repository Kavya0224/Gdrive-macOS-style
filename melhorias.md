# Melhorias futuras — GDrive macOS style

Lista levantada em 02/11/2026, em ordem de prioridade sugerida.

## 1. Proteção contra mudanças do Google (prioridade)

A extensão depende do HTML interno do Drive, que o Google muda sem aviso.

- [x] **Checagem ao carregar:** verificar se os elementos esperados existem (`button[data-test-id="list-layout-button"]`, `div[role="main"]`, `tr[role="row"][data-id]`). Se algo sumir, desligar as colunas sozinho com um aviso, em vez de travar o Drive.
- [x] **Teste automático simples:** abrir o Drive, ligar as colunas, navegar duas pastas. Serve para saber em segundos o que quebrou quando o Drive mudar.

  Feito: a checagem roda só em Meu Drive e nas pastas (em Computadores o Drive não tem os botões Lista/Grade). Autoteste em `…/drive/my-drive#gdf-teste`; detalhes no README.

## 2. Arrastar e soltar de volta

- [x] **Mover entre colunas:** soltar um item numa pasta viraria "recortar (⌘X) → abrir pasta de destino → colar (⌘V)" na lista nativa. Antes, testar se o Drive tem recortar/colar, usando um arquivo descartável.

  Feito, por outro caminho. Testado com arquivos descartáveis: o Drive **não tem recortar** (⌘C → ⌘V só faz uma cópia).
  O arraste da lista nativa só move para pastas visíveis na mesma lista, e em outra pasta ele cria um **atalho** em vez de mover.
  A extensão usa a janela **Mover** (⌥⌘M): cola o endereço da pasta de destino na busca da janela e clica em "Mover".
  Funciona para qualquer pasta das colunas, com seleção múltipla, "mola" (parar em cima abre a pasta) e o "Desfazer" do Drive.
- [x] **Enviar arquivos arrastados do computador:** soltar arquivos numa coluna e repassar para a lista nativa daquela pasta.

  Feito: a lista nativa abre a pasta de destino enquanto os arquivos passam por cima, e o "drop" é repassado a ela.
  Testado com arquivos criados por script (coluna, pasta de outra coluna e soltura imediata).
  **Falta testar arrastando do Finder de verdade**, principalmente uma pasta inteira.

## 3. Mais velocidade

- [ ] Guardar o conteúdo das pastas visitadas enquanto a aba está aberta: depois de recarregar a página, as colunas aparecem na hora e se atualizam em segundo plano.
- [ ] Carregar antes a pasta selecionada quando o usuário para em cima dela com as setas.

## 4. Recursos do Finder que faltam

- [ ] **Voltar/avançar:** ⌘[ e ⌘] para o histórico de pastas.
- [ ] **"Mostrar no local" na busca:** montar as colunas com o caminho até o arquivo encontrado.
- [ ] **Pré-visualização mais rica:**
  - [ ] tocar vídeo e áudio no próprio painel
  - [ ] rolar páginas de PDF
  - [ ] trazer mais dados do painel ⓘ do Drive (local, quem tem acesso, data de criação)
- [ ] **Barra de caminho no rodapé**, como a do Finder, com a quantidade de itens.

## 5. Validar o que ainda não foi testado

- [ ] Tema escuro
- [ ] Drives compartilhados
- [ ] Atalhos que apontam para pastas
- [ ] Pasta vazia
- [ ] Drive em inglês
- [ ] Segunda conta logada (endereço `/u/1/`)
- [x] **Computadores aparece vazio nas colunas:** nessa seção o Drive não usa as linhas `tr[role="row"][data-id]` nem tem os botões Lista/Grade, então as colunas não acham nenhum item. Ver como o Drive monta essa lista e ler os itens dali (ou deixar Computadores na visualização nativa).

  Feito: em Computadores as colunas leem os cards `div[role="gridcell"][data-id]`; cada computador abre como pasta. Abrindo uma pasta de computador pela URL, o caminho começa na coluna "Computadores" (breadcrumb com `data-view="…computers…"`). Testado: lista, abrir o computador, menu de contexto e caminho completo de `Documents`.
- [ ] Teclas que alteram arquivos: Delete/⌘⌫ (lixeira), ⌘C/⌘X/⌘V. Testar com arquivos descartáveis.
  (O menu do Drive mostra "Fazer uma cópia: ⌘C, depois ⌘V" e não tem recortar; no teste com teclas simuladas, ⌘X → ⌘V não moveu nada.)
- [ ] **Caminho ao abrir pela URL:** ao carregar a página direto numa subpasta, às vezes só aparece a coluna da pasta,
  sem as ancestrais. Ligar e desligar as colunas monta o caminho. Visto em 02/11/2026 durante os testes do item 2.

## 6. Distribuição

- [ ] Publicar na Chrome Web Store (pode ser como não listada) para ter atualização automática em vez de recarregar a pasta à mão.
