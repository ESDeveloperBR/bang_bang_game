# Bang Bang — The Bounty Hunter
### Manual de jogo e laboratório de gráficos/áudio para a ES32Lab

**Desenvolvimento:** ES Developer  
**Placa:** ES Developer **ES32Lab**, com ESP32 e TFT colorido de **160 × 128 pixels**  
**Versão do projeto de referência:** **1.2.4** (obtida da seção `[release]` de `platformio.ini`)  
**Documentação:** 11 de novembro de 2026

> **Aviso sobre a versão 1.2.4:** foi relatado um problema de **reinicializações sucessivas** após a inclusão da partição Core Dump. Sua causa ainda não está confirmada. Portanto, esta documentação descreve os recursos implementados nos arquivos do projeto, **não certifica a estabilidade da versão**. Consulte “Diagnóstico” antes de distribuir o firmware.

## 1. Bem-vindo ao Velho Oeste!

Você é um caçador de recompensas que precisa sobreviver a confrontos cada vez mais rápidos, acertar capangas que aparecem em diferentes pontos da tela e derrotar cinco chefões. A jornada passa pelo **Saloon**, **Banco**, **Estação Ferroviária**, **Mina de Jack Cascavel** e **Mansão de Rosetta de Prata**.

Cada fase apresenta cenários próprios, personagens e cartazes de procurados. Ao vencer o último duelo, você desbloqueia um encerramento cinematográfico com a despedida do cowboy, a frase **“Você escreveu sua lenda no Velho Oeste. Agora, é hora de criar sua própria aventura!”** e a provocação **“THE END?”**.

Por trás do jogo, existe um propósito educacional: **testar como a ES32Lab lida com imagens RGB565, animações compostas em tempo real, som digital, memória Flash e interfaces gráficas elaboradas**.

## 2. O que é preciso

- **Placa ES32Lab** com seu TFT integrado e teclado analógico de cinco direções/ações.
- Cabo USB compatível e computador com **PlatformIO** para instalar o programa.
- Bibliotecas oficiais **ES32Lab** e **TFT_eSPI_ES32Lab** já instaladas no ambiente de compilação.
- **Opcional, para a melhor experiência sonora:** fone de ouvido ou caixa de som com plugue **P2**.
- Para desenvolvimento, Monitor Serial em **115200 baud**.

**Não é necessário Wi-Fi para jogar.** O projeto atual também não faz atualização OTA por Wi-Fi.

## 3. Como começar

O fluxo de apresentação é inspirado nos arcades clássicos:

1. A tela exibe a **marca ES Developer**, com transição de desaparecimento (*fade-out*) para preto.
2. Começa a **abertura cinematográfica** do Bang Bang: vários enquadramentos e uma cena de disparo.
3. Se já existirem pontuações salvas, o **ranking** aparece automaticamente por até **10 segundos**. Pressione **CENTRO** se quiser pular.
4. Surge o **menu principal** com **INICIAR**, **RANKING** e **CONFIGURAR**.
5. Se o áudio estiver em BUZZER, o jogo pode apresentar um aviso oferecendo a experiência DAC. Você pode manter BUZZER.
6. Escolha **INICIAR**, selecione a dificuldade e confirme. O primeiro cartaz de procurado aparece antes do confronto.

A abertura pode ser pulada com CENTRO. Se o ranking estiver vazio, ele não interrompe o início do jogo.

### Os cinco botões

O teclado da ES32Lab reúne as ações **CIMA**, **BAIXO**, **ESQUERDA**, **DIREITA** e **CENTRO**.

| Contexto | Como usar |
|---|---|
| Menu principal | CIMA/BAIXO escolhem uma opção; CENTRO confirma. |
| Escolha da dificuldade | CIMA/BAIXO percorrem Fácil/Médio/Difícil; CENTRO inicia. |
| Cartaz de procurado | CENTRO avança para os capangas. |
| Confrontos | Pressione a direção correspondente à posição do capanga; o botão CENTRO também é uma posição de tiro. |
| Duelo contra chefão | Execute a sequência de setas mostrada no topo e finalize com **CENTRO** dentro do tempo. |
| Conclusão da fase | CENTRO segue para o confronto seguinte. |
| Perda de vida | Aguarde a tela de resultado e pressione uma tecla quando liberado. |
| Falta de munição | CENTRO confirma a retomada, quando ainda houver vidas. |
| CONTINUE | ESQUERDA seleciona SIM; DIREITA seleciona NÃO; CENTRO confirma. |
| Cadastro no ranking | CIMA/BAIXO mudam o caractere, ESQUERDA/DIREITA mudam a posição, CENTRO confirma cada uma das três posições. |
| Ranking automático | CENTRO pula antes do limite de 10 segundos. |
| Ranking aberto pelo menu | Permanece até pressionar CENTRO ou ESQUERDA. |
| Tela do QR Code | CENTRO reinicia a apresentação do jogo. |

**Dica:** para registrar dois toques iguais consecutivos, **solte a tecla entre os pressionamentos**. O jogo interpreta o começo de um novo pressionamento.

## 4. Como jogar: tiro, munição e vidas

O objetivo das etapas de capangas é reagir à posição do inimigo **antes que ele atire**. Pode aparecer um rival em cima, embaixo, à esquerda, à direita ou no centro, conforme o cenário.

- Você começa com **3 vidas**.
- Usa dois revólveres com **6 balas cada**, totalizando **12 cartuchos** por etapa.
- Cada pressionamento de tiro gasta **uma bala**. O programa escolhe automaticamente um dos revólveres que ainda tenha munição.
- **Tiro errado:** gasta bala, mas não elimina uma vida imediatamente.
- **Tempo de reação esgotado:** o capanga atira no herói e uma vida é perdida.
- **Munição esgotada antes de terminar os capangas:** surge a tela “SEM BALAS! -1 VIDA”. A etapa pode ser retomada com munição restaurada se você ainda tiver vidas.
- **A última bala matou o último capanga?** Ótimo! Essa eliminação conta como vitória, sem penalidade artificial por “não sobrar bala”.
- **Concluir a etapa de capangas:** concede **+1 vida** antes do duelo contra o chefão.

O HUD nos quatro cantos reúne informações importantes: identificação de fase/vidas, **12 cartuchos gráficos**, dinheiro/recompensa acumulada e progresso de rivais derrotados.

### Sistema de recompensas

O dinheiro exibido na tela também representa sua **pontuação**:

| Ação | Recompensa |
|---|---:|
| Novo capanga derrotado | **+$100** |
| Etapa perfeita de capangas | **+$200**, uma única vez |
| Chefão da fase 1 | **+$500** |
| Chefão da fase 2 | **+$750** |
| Chefão da fase 3 | **+$1.000** |
| Chefão da fase 4 | **+$1.250** |
| Chefão da fase 5 | **+$1.500** |
| Bônus de rapidez no duelo | Até **+$300** |
| Bônus ao concluir o jogo | **+$200 por vida restante** |

Há proteção contra ganhar dinheiro repetidamente pelo mesmo grupo de capangas quando uma etapa é reiniciada.

### Três dificuldades

**Fácil:** dá mais tempo para reagir. Em determinadas falhas de duelo, o CONTINUE retorna ao checkpoint do chefão.

**Médio:** tempos mais curtos e penalidades de recomeço mais exigentes.

**Difícil:** menor margem de reação, sequência de comandos mais longa e menor tolerância a atrasos.

A dificuldade influencia a quantidade de setas do duelo, os intervalos entre aparições e o tempo disponível. **O disparo final de todo duelo é sempre CENTRO.**

### Se perder todas as vidas

Se houver pelo menos **$1.000**, o jogo oferece **CONTINUE**, com custo de **$1.000 por uma nova vida**. Pode aceitar novamente quando tiver saldo suficiente. Se recusar ou não houver dinheiro, aparecerá **GAME OVER**.

A câmera percorre a lápide e **somente depois** mostra a pontuação final, evitando texto sobre a animação em movimento.

## 5. Ranking estilo arcade

O ranking é persistente, **mesmo após desligar a placa**. Existem três categorias lado a lado na mesma tela:

- **FÁCIL — TOP 5**
- **MÉDIO — TOP 5**
- **DIFÍCIL — TOP 5**

Se sua pontuação entrar nas cinco melhores do nível escolhido, você poderá registrar **três letras ou dígitos**, como nos fliperamas antigos. Não há necessidade de teclado externo:

1. Use **CIMA/BAIXO** para escolher um caractere de `A` a `Z` ou `0` a `9`.
2. Use **ESQUERDA/DIREITA** para navegar entre as três posições.
3. Pressione **CENTRO para confirmar** o caractere selecionado.
4. Apenas depois de **confirmar as três posições** o resultado é gravado.

O ranking aparece automaticamente após a abertura **se houver pelo menos uma pontuação registrada** e após a partida em situações previstas no jogo. Nessa exibição automática, a espera máxima é de **10 segundos**; CENTRO fecha antecipadamente. Pelo menu **RANKING**, você pode examinar os resultados sem limite de tempo.

Os dados são armazenados na memória **NVS**, usando `Preferences`. Não dependem de cartão SD, de LittleFS nem de conexão com a internet.

## 6. Final cinematográfico e recompensa de criação

Ao vencer os cinco chefões, o jogo apresenta um pequeno filme composto **dentro do próprio ESP32**:

1. A cena de Rosetta derrotada reutiliza a arte correspondente à personagem do duelo.
2. O xerife entrega a recompensa.
3. O cowboy parte da cidade sobre o cavalo; fundo e personagem se movimentam em **camadas independentes**.
4. No pôr do sol, o cowboy e o cavalo vão **diminuindo gradualmente em tempo real**, como se desaparecessem na estrada.
5. A paisagem congela, surge a frase sobre criar uma nova aventura e a tela termina em **“THE END?”**.

Depois, o jogo registra seu resultado no ranking quando aplicável e mostra um convite:

**“Você venceu o desafio. Agora, que tal criar o seu?”**

Ao pressionar CENTRO, aparece o **QR Code para descontos da ES Developer**. Aponte a câmera do celular e abra:

**https://www.esdeveloper.com.br/cupons-de-desconto**

O QR fica **na tela por tempo indeterminado**, sem contar segundos e sem redesenhar continuamente. Quando quiser continuar, pressione CENTRO para retornar ao início da apresentação. A tela promocional é reservada a quem **concluiu o jogo**.

## 7. Som: BUZZER ou DAC?

O Bang Bang possui dois modos com objetivos diferentes.

| Modo | O que você ouve | Recomendação |
|---|---|---|
| **BUZZER** | Efeitos de disparo. **Sem música de fundo.** | Funciona sem fones nem caixa externa; escolha padrão no primeiro uso. |
| **DAC** | Trilha sonora digital e efeitos de disparo. | Melhor experiência; use fone/caixa com conector P2. |

O jogo utiliza três músicas de teste diferentes (abertura/duelo, capangas e menus; a música de menu é reutilizada temporariamente no final) e um efeito digital de disparo. O modo DAC trabalha com **áudio PCM mono 8 bits, 20 kHz**, gerado pelo DAC interno do ESP32.

### Se ouvir um zumbido ao ativar DAC

**Isso pode acontecer quando o P2 está vazio.** A ES32Lab possui um circuito que comuta automaticamente o sinal:

- **Fone ou caixa conectada à saída P2:** o buzzer é desativado pelo próprio circuito e o áudio vai ao P2.
- **P2 sem dispositivo:** o sinal DAC pode ser ouvido como zumbido agudo no buzzer.

Se você informar que o som está incomodando, o jogo **para o DAC** e volta temporariamente ao **BUZZER**. Ele perguntará se você dispõe de fone ou caixa para conectar; a resposta padrão será **NÃO**. Para continuar em DAC, selecione SIM, conecte o equipamento ao P2 e confirme. **Não é necessário retirar jumpers.**

A preferência de áudio é gravada na NVS para as próximas utilizações. Use volume moderado nos fones.

## 8. Por que o Bang Bang é também um laboratório de desempenho?

Este jogo não usa apenas telas estáticas: sua finalidade é **testar os limites práticos dos recursos gráficos e de áudio do ESP32 da ES32Lab**.

### 8.1. Alta qualidade visual com RGB565

As artes foram preparadas a partir de imagens de produção de resolução mais alta, convertidas para o TFT de **160 × 128**. No firmware, o formato RGB565 armazena cada pixel em **16 bits** (5 para vermelho, 6 para verde, 5 para azul).

**Exemplo real:** um quadro RGB565 bruto de 160 × 128 usa `160 × 128 × 2 = 40.960 bytes`, ou aproximadamente **40 KiB**. Guardar muitos quadros inteiros consome Flash rapidamente.

Para economizar memória sem perder a identidade visual, o jogo também explora:

- Panoramas maiores do que a tela, exibindo apenas uma **janela (viewport)** de 160 × 128.
- **Sprites e máscaras de transparência**, que preservam o cenário por trás dos personagens.
- **Composição de camadas** sobre o fundo, sem precisar criar dezenas de versões prontas da mesma cena.
- Quadros em **paleta de 8 bits**, convertidos para RGB565 durante a renderização.
- **RLE sem perdas** na marca inicial da ES Developer.
- **Textos dinâmicos e contornos** aplicados em cima de menus RGB565, sem duplicar a arte para cada destaque.

### 8.2. Zoom calculado no próprio ESP32

No pôr do sol, em vez de armazenar dezenas de imagens do cavalo em tamanhos diferentes, o programa mantém **duas poses** e calcula seu tamanho a cada quadro, usando redimensionamento *nearest-neighbor*.

Também movimenta o animal pelo cenário e ajusta a altura para manter os cascos próximos ao chão. Isso permite testar **custo de processamento por pixel, taxa de atualização e consumo de RAM**, preservando espaço de Flash.

O projeto usa **faixas de oito linhas de RGB565**, equivalentes a cerca de **2.560 bytes de buffer**, para parte da renderização cinematográfica; não precisa manter todos os quadros descompactados na RAM ao mesmo tempo.

### 8.3. Áudio digital sem bloquear os comandos

O som DAC é enviado pelo **I2S com DMA ao DAC interno**, em uma **tarefa FreeRTOS separada**. Assim, o jogo pode misturar áudio e tiros e continuar recebendo teclas, em vez de esperar cada música terminar para atualizar o display.

Durante o disparo, a música diminui de volume temporariamente (*ducking*) para realçar o efeito. O BUZZER opera em outra rotina, tocando notas para simular o tiro, sem trilhas musicais.

### 8.4. O que é medido no DEBUG

Quando a compilação é feita com DEBUG, os testes de encerramento mostram pelo Monitor Serial:

- **FPS** aproximado, com janela de medição por segundo.
- **Tempo médio de renderização** em microssegundos.
- **Maior tempo de renderização** da janela.
- **RAM/heap livre** e mínimo observado.
- **Etapa exata** da animação em execução.

Esses valores ajudam a estudar gargalos de tela e CPU. **Não existe um FPS garantido:** os resultados precisam ser medidos na ES32Lab real.

## 9. DEBUG: recursos para professores, makers e desenvolvedores

A versão pública analisada traz o DEBUG **desativado**. Para estudar os gráficos, altere no `platformio.ini`:

```ini
-D BANGBANG_DEBUG_SCREEN=1
```

Depois recompile e grave pelo PlatformIO. Para voltar ao jogo normal, use `0`.

O laboratório DEBUG contém:

- **20 páginas de cenário** (cinco cenários × quatro modos): todos os rivais, variantes de estado, gabarito e editor de posição XY.
- **Página 21 — FINAL:** reproduz o encerramento sem completar a história.
- **Página 22 — IMAGENS:** permite inspecionar os dados gráficos individualmente.

No editor XY, é possível mover o personagem **um pixel por vez**. Pressionar CENTRO por **2 segundos** gera relatório de coordenadas no Monitor Serial. Esses valores são temporários, apenas na RAM, até o desenvolvedor atualizá-los no código.

A galeria da versão 1.2.4 possui **91 posições numeradas**, das quais **87 contêm recursos ativos** e as posições **2, 4, 5 e 14 estão reservadas/removidas**. Os arquivos de diagnóstico podem ter comentários com contagens antigas; consulte o código para os números atualizados.

### Comandos úteis pelo Monitor Serial no DEBUG

| Comando | O que faz |
|---|---|
| `FINAL` | Exibe o final completo. |
| `CAVALO` | Testa diretamente a saída da cidade com camadas. |
| `SOL` | Testa diretamente o zoom do cavalo no pôr do sol. |
| `IMG 6` | Mostra o item numerado 6 da galeria. |
| `IMAGENS` ou `IMG` | Abre o visualizador gráfico. |
| `RANKING` | Mostra o ranking. |
| `LOGO` | Mostra a logo de abertura. |
| `CUPOM` | Exibe o convite de desconto. |
| `QR` | Exibe o QR Code. |

Na galeria, ESQUERDA/DIREITA trocam a imagem; CIMA/BAIXO percorrem recortes de imagens grandes ou grupos; CENTRO curto mostra/oculta o rótulo, e CENTRO por 2 segundos sai.

## 10. Instalação pelo PlatformIO

O projeto é destinado ao **ESP32 integrado da ES32Lab**, não a uma placa ESP32 genérica. Os arquivos de referência estão distribuídos nas seguintes pastas:

```text
BangBang/
├── platformio.ini             # Ambiente, versão e opções de compilação
├── partitions.csv             # NVS, aplicação e Core Dump
├── README.md                  # Este manual
├── README-IA.md               # Treinamento técnico de manutenção/IA
├── src/
│   ├── main.cpp               # Lógica completa do jogo
│   └── es32lab_bang_bang-thumb.jpg  # Thumbnail oficial; não vai para o firmware
├── include/
│   ├── BangBangIntro.h         # Abertura cinematográfica
│   ├── BangBangStage*.h        # Cenários e coordenadas
│   ├── BangBangBoss*.h         # Artes de chefões
│   ├── BangBangBandits.h       # Personagens e máscaras
│   ├── BangBangUI*.h           # Menus e painéis
│   ├── BangBangEnding*.h       # Encerramento/camadas
│   ├── BangBang*Music*.h       # Amostras PCM
│   ├── BangBangRanking*.h      # Ranking e sua arte
│   └── ...                     # Outros cabeçalhos de sprites e telas
└── assets/                    # PNGs originais de produção (não compilados)
```

**Pré-requisito de compilação da versão analisada:** o `src/main.cpp` exige o arquivo oficial `ES_WifiScanService.h`, que **não está incluído no ZIP 1.2.4 analisado**. É preciso instalá-lo no caminho de includes do projeto; não utilizar uma implementação improvisada. O serviço abre uma janela serial de manutenção no início do `setup()` e, fora dessa janela, não interfere no jogo.

Para compilar:

1. Abra a pasta no VS Code com a extensão PlatformIO.
2. Confirme o ambiente `nodemcu-32s` e as bibliotecas oficiais já instaladas no seu computador.
3. Garanta a presença do cabeçalho oficial do scanner de manutenção.
4. Use **Build** para conferir **RAM e Flash**.
5. Apenas se o binário couber na partição e a compilação concluir, use **Upload** via USB.
6. Para acompanhar os logs, abra o Monitor Serial a 115200.

A versão e a data são configuradas **somente na seção `[release]` de `platformio.ini`**. O menu principal consulta a macro de compilação para mostrar a versão; não há número fixo a editar no arquivo principal.

## 11. Memória, Flash, persistência e limitações

O projeto de referência tem **Flash física de 4 MiB**. O arquivo `partitions.csv` distribui o espaço entre **NVS**, inicialização de rádio, grande partição de aplicativo e **16 KiB de Core Dump**. A partição do programa na versão 1.2.4 possui limite de **4.112.384 bytes**.

As artes RGB565 e as três trilhas PCM são o principal motivo de o firmware ocupar tanto espaço. A decisão do projeto é **não remover recursos visuais ou sonoros apenas para criar espaço para OTA**. Portanto, o Bang Bang **não implementa atualizações OTA via Wi-Fi** na configuração atual.

O ranking e a preferência de áudio são salvos em **NVS**, e continuam disponíveis após desligar a placa. **Uma gravação que apague toda a Flash pode apagar esses registros** — faça esse procedimento apenas quando realmente necessário.

**O Core Dump é destinado a diagnosticar falhas graves.** A reserva atual de apenas 16 KiB pode ser pequena para determinados relatórios. Isso é diferente de RAM livre ou de espaço para áudio/imagens.

## 12. Diagnóstico de problemas

| Sintoma | O que verificar |
|---|---|
| Aparece tela branca durante uma animação | Conferir dimensões e dados RGB565, limites dos buffers, galeria DEBUG, transferência de SPI e logs `[T16]`. |
| Cavalo parece flutuar no zoom | Reavaliar alinhamento das patas e coordenada vertical durante a redução de tamanho. |
| Imagem colorida aparece invertida ou com cores erradas | Conferir RGB565 e o `display.setSwapBytes(true)` adotado pelo projeto. |
| Som DAC gera zumbido no buzzer | Conferir se fone/caixa está realmente conectado ao P2; responder NÃO quando não houver dispositivo. |
| Ranking está vazio | Ele só apresenta nomes após o primeiro resultado elegível; dados são separados por dificuldade. |
| Não há Wi-Fi/OTA para instalar pela rede | É esperado: as atualizações do jogo são por USB/PlatformIO. O scanner de manutenção não é OTA. |
| `ES_WifiScanService.h: No such file or directory` | Falta o **cabeçalho oficial obrigatório** do serviço de manutenção no ambiente. |
| Erro `program size greater than maximum allowed` | O binário ultrapassou a partição do aplicativo. **Não** aumentar o limite ignorando o tamanho real da Flash. |
| `esp_core_dump_flash: No core dump partition found!` | A configuração em execução não apresenta partição de Core Dump reconhecida; conferir se a tabela de partições correta foi gravada. |
| `rst:0x3 (SW_RESET)` repetido sem abrir o jogo | Reinicialização por software: causa **não confirmada**. Obter log de boot integral, conferir configuração e imagem de firmware/partições e comparar com versão previamente funcional. |

**Problema conhecido em aberto:** após alterações do particionamento na versão **1.2.4**, foi observado um ciclo de reinicialização. O projeto **não deve ser considerado validado nessa versão** até reproduzir o problema, confirmar a causa e testar uma correção no hardware. Não apagar a memória NVS automaticamente durante o diagnóstico.

## 13. Para continuar criando

O Bang Bang foi concebido para motivar novos projetos. Ao terminar, a frase final pergunta se a aventura realmente acabou — ou se é hora de criar a sua própria.

**“Você escreveu sua lenda no Velho Oeste. Agora, é hora de criar sua própria aventura!”**

**THE END?**

---

**ES Developer · Projeto Bang Bang — The Bounty Hunter · ES32Lab**  
Este manual descreve os recursos **presentes no código-fonte da versão 1.2.4**. As medidas de desempenho definitivas dependem de compilação e teste físico; a documentação de manutenção, com inventário completo de arquivos e detalhes de implementação, está em **`README-IA.md`**.
