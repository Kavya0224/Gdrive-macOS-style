# Melhorias futuras — GDrive macOS style

Lista levantada em 02/11/2026, em ordem de prioridade sugerida.

## 1. Proteção contra mudanças do Google (prioridade)

A extensão depende do HTML interno do Drive, que o Google muda sem aviso.

- [x] **Checagem ao carregar:** verificar se os elementos esperados existem (`button[data-test-id="list-layout-button"]`, `div[role="main"]`, `tr[role="row"][data-id]`). Se algo sumir, desligar as colunas sozinho com um aviso, em vez de travar o Drive.
- [x] **Teste automático simples:** abrir o Drive, ligar as colunas, navegar duas pastas. Serve para saber em segundos o que quebrou quando o Drive mudar.

  Feito: a checagem roda só em Meu Drive e nas pastas (em Computadores o Drive não tem os botões Lista/Grade). Autoteste em `…/drive/my-drive#gdf-teste`; detalhes no README.

## 2. Arrastar e soltar de volta

- [ ] **Mover entre colunas:** soltar um item numa pasta viraria "recortar (⌘X) → abrir pasta de destino → colar (⌘V)" na lista nativa. Antes, testar se o Drive tem recortar/colar, usando um arquivo descartável.
- [ ] **Enviar arquivos arrastados do computador:** soltar arquivos numa coluna e repassar para a lista nativa daquela pasta.

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

## 6. Distribuição

- [ ] Publicar na Chrome Web Store (pode ser como não listada) para ter atualização automática em vez de recarregar a pasta à mão.
