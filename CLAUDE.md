# Realtime Generative Visual Engine

Base modular de visuais generativos em TouchDesigner (2025.33230, macOS). Uso: tempo real, multitela Windows, gravação, sync multimáquinas.

## Como trabalhar com o usuário
- Responder sempre em português, curto e direto. Sem preâmbulo, sem elogio, sem resumir o que ele disse, sem dicas ou alternativas não pedidas. Se a pergunta for ambígua, fazer uma pergunta objetiva.
- Não dizer que o usuário rodou algo que não dá para confirmar.
- Antes de mexer no TD, ler `td_get_hints('bootstrap')` (MCP `twozero_td`). O TD pode travar; se travar, pedir ao usuário para olhar a janela. Nunca matar o TD sem perguntar.
- Nunca usar `project.save()` sem cuidado (pode abrir diálogo e travar). O usuário salva pelo TD (File > Save As se o caminho mudou).
- Commit e push só quando o usuário pedir. Terminar commits com `Co-Authored-By: Claude Sonnet 5.5 <noreply@anthropic.com>`.

## Arquivos
- `Realtime-Generative-Visual-Engine.toe`: projeto principal. Os `.N.toe` numerados e `Backup/` são ignorados pelo git.
- Pastas de assets: `Audio/`, `Chan/`, `Geo/`, `Image/`, `Movie/`. Caminhos de arquivo sempre relativos à pasta do projeto.
- Repositório: https://github.com/fazolin/Realtime-Generative-Visual-Engine (branch `main`).
- Roadmap e tarefas: GitHub Project https://github.com/users/fazolin/projects/5 (board, colunas Todo / In Progress / Done, swimlanes por campo **Macro**). Não há ROADMAP em arquivo.

## Macros (campo Macro do board)
Audio IN, Pixel Map, Geração de conteúdo, Preview 3D, Output, UI. Modulação, presets e gravação entram como features dentro deles. Cada feature é um card separado com o campo Macro preenchido.

Features já definidas em **Output**: Saída NDI, Multitelas Windows, Sync multimáquinas.
Em andamento: Audio IN e Análise de áudio (12 bandas).

## Estrutura no TouchDesigner
Dentro de `/project1` há um baseCOMP por macro: `AUDIO`, `OUTPUT`, `CONFIGS`, `CONTENT`, `PREVIEW`, `UI`.

### AUDIO
- Entrada: `audio_device` (audiodeviceinCHOP) e `audio_file` (audiofileinCHOP) entram no `source_select` (switchCHOP). Depois disso vem mono, espectro e 12 bandas com normalização por envelope (o usuário reorganizou essa parte depois; ver a rede real antes de mexer).
- Parâmetros custom na página **Audio Input** do `AUDIO`:
  - `Source` (Audio Device / Media File): comanda o índice do `source_select`.
  - `Device`: dispositivo de áudio (lista fixa, copiada do menu do `audio_device`; não atualiza sozinha).
  - `File`: arquivo de áudio.
- Normalização: cada banda dividida pelo seu envelope (seguidor de pico com decaimento lento, feedbackCHOP no laço, piso 0,005 para não dividir por zero).

## Convenções
- Um módulo = um baseCOMP com parâmetros custom na raiz, sem caminhos absolutos, sem `op.store`.
- Operadores com tamanho único (130x90), em grade de 200x130, fluxo da esquerda para a direita, sem fios passando por cima de outros operadores. Placas (annotateCOMP) só como título de bloco.
- Nomes semânticos (`audio_out`, `bands_norm`), nunca `chop1`.
- Sem operadores de teste sobrando (remover probes ao terminar).
- Bandas de áudio: 12 bandas em escala logarítmica a partir de `audiospectrumCHOP` (144 amostras, split de 12 em 12 e média via `analyzeCHOP`).

## Pendências conhecidas
- O arquivo padrão do `File` aponta para um mp3 de exemplo da instalação do TD (caminho absoluto); trocar por um arquivo em `Audio/`.
- Lista de dispositivos é estática; considerar botão Refresh.
- Captura de tela do macOS sem permissão para o TD (`td_get_screen_screenshot` falha).
