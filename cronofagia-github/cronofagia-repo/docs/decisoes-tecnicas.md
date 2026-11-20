# Cronofagia — Decisões técnicas

## 2026-11-17 — Migração para 3D
- **Engine:** Three.js (r170) no navegador. Mantém o alvo Web + Android (Capacitor) e o cross-play do GDD.
- **Câmera:** isométrica de cima (inclinação ~56°). Preserva os controles twin-stick do GDD (PC e touch).
- **Arquitetura:** a simulação continua em 2D (plano x,y em unidades de jogo) e é apenas renderizada em 3D (1 unidade = 1/30 m). Isso deixa a lógica pronta para virar o servidor autoritativo da Fase 3.
- **Personagens:** modelos glTF rigados Mixamo (Soldier, Michelle, Xbot) dos exemplos do three.js. Michelle usa as animações do Xbot via retarget em espaço de mundo. Jogadores = humanos; anomalias (inimigos) = manequins Xbot com articulações emissivas.
- **Indicador de vida:** o polígono que perde lados (12 → 6 → 3) virou um anel-relógio no chão, sob o personagem.
- **Modelos:** comprimidos com meshopt + WebP (8,3 MB → 1,7 MB), servidos como base64 em `models.js`.
- **Pendências:** os modelos atuais são placeholders da Mixamo e precisam ser substituídos por personagens próprios antes do lançamento.

## 2026-11-20 — Jogabilidade (modo Arena solo)
Referências de gênero: Hades e Vampire Survivors (escolha de melhorias entre ondas), Risk of Rain (escalada), Devil May Cry (nota de estilo).

- **Ciclos (ondas):** a partida é dividida em Ciclos com número fixo de inimigos (5 + 2n; ciclos de chefe: 3 + n). Ao limpar um ciclo: +5s, o relógio para e o jogador escolhe uma Engrenagem. Novos tipos entram progressivamente (Sentinela no 2, Inverso no 3, Ancorado no 4, Parasita no 6). A partir do Ciclo 7 surgem inimigos de elite (coroa dourada, +50% de relógio, +25% de velocidade).
- **Engrenagens (melhorias):** 1 de 3 cartas, com raridade (comum, rara, épica, lendária). Trocar as opções custa 3s. São 16 engrenagens: dano, custo, roubo de tempo, velocidade, recarga, crítico, +15s, projétil extra, Ressonância longa, +1,5s por abate, perfuração (inclusive do escudo do Ancorado), onda de choque ao sofrer dano, aura de lentidão, raio em cadeia, Sobrecarga em 45s e Segunda Dívida.
- **Ataques anunciados:**
  - Efêmero para e "arma" por 0,45s antes de explodir em área.
  - Ancorado trava a direção e marca a área do golpe no chão (0,6s); o jogador pode contornar para atacar pelas costas.
  - Sentinela mostra uma linha de mira por 0,75s antes do disparo.
  - Parasita leva 0,6s para se prender antes de drenar.
- **O Colecionador (chefe, a cada 5 ciclos):** começa com 30s e chega a no máximo 150s. Absorve o tempo de todo inimigo que expira, conforme o GDD.
  - **Ataques em rodízio:** onda de choque com duas brechas seguras (verdes, anunciadas antes), anel de 12 orbes e invocação de Efêmeros. A invocação cria o dilema: matar os Efêmeros antes que expirem e alimentem o chefe.
- **Estilo e pontuação:** nota D → C → B → A → S → Ω (Cronófago). Sobe com acertos, abates e combos; decai com o tempo e cai quase pela metade ao sofrer dano. Multiplica a pontuação. O recorde passou a ser por pontuação e melhor ciclo.
- **Feedback:** orbes de tempo voam do inimigo abatido até o jogador; setas na borda da tela mostram inimigos fora de vista.
- **Controles:** a troca PC/touch é automática pela última entrada usada (mouse ou teclado = PC; toque = touch).
- **Pendências:** balancear números com jogadores reais (quantidade por ciclo, valores das engrenagens, vida do chefe). Faltam meta-progressão entre partidas (Areia Eterna do GDD) e os modos multiplayer.

## 2026-11-20 — Arena "O Mostrador" (Relógio-Arena)
Conceito: a arena é o mostrador de um relógio colossal flutuando sobre o "abismo do tempo".

- **Chão:** mostrador com algarismos romanos, marcas de minuto, roseta de engrenagem no centro, espiral sutil, submostradores de cronógrafo nos cantos e o lema *TEMPUS EDAX RERUM* ("o tempo, devorador das coisas", Ovídio), escolhido por ecoar o nome Cronofagia.
- **Ponteiros no chão:** mostram o relógio do próprio jogador; o de segundos anda para trás, como uma contagem regressiva.
- **Plataforma flutuante:** aro de bronze com marcas a cada metro (5 em 5 destacadas) e faixa luminosa sob a borda.
- **Abismo:** 10 engrenagens colossais girando sob a arena e destroços de relógio flutuando (instanciados para desempenho).
- **Pilares → torres-relógio:** mostrador no topo com ponteiros sincronizados ao relógio do jogador e engrenagens laterais girando.
- **Ampulhetas nos 4 cantos:** a areia de cima representa o tempo do jogador (escala até 90s).
- **Estados refletidos no cenário:**
  - **Cor do mostrador:** dourado (normal), ciano (Sobrecarga), vermelho (Agonia), laranja (Dívida Temporal), violeta (Inversão de Fluxo).
  - **Pulso:** o mostrador pulsa a cada segundo.
  - **Inversão de Fluxo:** engrenagens, ponteiros e destroços giram ao contrário e mais rápido.

## 2026-11-20 — Realismo visual
- **Materiais PBR reais:** texturas CC0 do Poly Haven (polyhaven.com), redimensionadas e embutidas em `tex.js` (~2,6 MB):
  - Chão: lajes de pedra (marble_01) escurecidas, com acabamento polido (clearcoat). As incrustações do mostrador são ouro metálico de verdade (metalnessMap).
  - Torres: castle_wall_slates.
  - Bronze com pátina: normal e rugosidade de rusty_metal_02.
  - Estrutura da plataforma: metal_plate.
- **Iluminação baseada em imagem:** HDRI CC0 bell_tower (Poly Haven), com reflexos reais em metal, pele e roupas.
- **Pós-processamento:** oclusão de ambiente GTAO (só em desktop e fora do modo "Gráficos leves"), vinheta leve e sombras mais nítidas.
- **Personagens:**
  - **Animação procedural por camadas:** rotações de ossos em eixos do mundo, independentes do esqueleto. As pernas seguem o movimento e o tronco e a cabeça giram para a mira; o andar fica de costas quando necessário. Há inclinação ao correr e reação ao levar golpes.
  - **Poses de ataque por classe:** corte em arco (Ceifador), martelada (Bastião), braço estendido (Cronomante) e dois braços (Paradoxo).
  - **Poses dos inimigos ligadas aos avisos:** Ancorado e Colecionador erguem os braços, Sentinela aponta, Efêmero se curva, Parasita estende os braços.
  - **Morte:** o corpo tomba para trás, levanta poeira e se desfaz.
  - **Sombra de contato** sob os personagens, poeira dos passos e inimigos com superfície de obsidiana polida.
- **Limite conhecido:** realismo de verdade em rostos e corpos exige modelos e animações de ataque próprios (Mixamo baixado pelo dono do projeto ou um artista 3D).

## 2026-11-20 — Arenas selecionáveis
O jogador escolhe a arena no menu. A escolha fica salva no navegador. Cada arena é um grupo de cena com obstáculos, iluminação, HDRI, névoa, exposição e modificador próprios. As três novas são baixadas só quando escolhidas (`arena_*.js`), e as texturas e HDRIs são CC0 do Poly Haven.

| Arena | Tema do tempo | Modificador |
| --- | --- | --- |
| **O Mostrador** | Relógio colossal sobre o abismo | Nenhum (clássica) |
| **Areias de Cronos** | Relógio de sol em ruínas no deserto | O sol segue o seu relógio |
| **O Instante Congelado** | Praça noturna com a chuva parada no ar | Inimigos 20% e tiros inimigos 25% mais lentos |
| **Horizonte de Eventos** | Plataforma orbitando um buraco negro | Zona central de dilatação |

- **Areias de Cronos:**
  - **Cenário:** dunas, praça-relógio de sol em arenito com algarismos romanos e o lema *VULNERANT OMNES, ULTIMA NECAT* ("todas ferem, a última mata", inscrição clássica de relógios de sol). Há colunas quebradas e obeliscos com ponta de bronze, muros em ruínas, ampulheta colossal semi-enterrada, esfera armilar e vento carregando areia.
  - **Sol:** acompanha o tempo do jogador. Com muito tempo, o sol fica alto e as sombras curtas; perto do zero, vem o pôr do sol avermelhado. O jogador é o ponteiro do relógio de sol.
  - **Recursos:** texturas coast_sand_05 e sandstone_blocks_08; HDRI dikhololo_sunset.
- **O Instante Congelado:**
  - **Cenário:** calçamento de pedras molhado com poças espelhadas e prédios com janelas acesas. Uma torre de relógio parado em 11:59, letreiros de neon, postes, colunas de anúncio e chafarizes com a água congelada no ar. Há um raio congelado no céu.
  - **Chuva parada:** ~1.800 gotas suspensas no ar. Dentro de uma bolha de 4 m ao redor do jogador elas caem e respingam: o tempo só corre perto dele.
  - **Recursos:** textura cobblestone_floor_04; HDRI cobblestone_street_night.
- **Horizonte de Eventos:**
  - **Cenário:** piso hexagonal escuro com bordas violeta. Buraco negro com anel de fótons e disco de acreção girando, 2.600 estrelas, asteroides como obstáculos e destroços orbitando.
  - **Dilatação:** num raio de 6 m do centro, o relógio do jogador corre 40% mais devagar e os inimigos ficam 40% mais lentos. O HUD mostra "DILATAÇÃO TEMPORAL ×0,6".
  - **Recursos:** textura dark_rock_02.
- **Pendências:**
  - **Balanceamento:** modificadores e layouts de obstáculos ainda precisam ser testados por jogadores.
  - **Recordes:** um recorde por arena seria uma boa adição.
  - **Download:** a arena da cidade é a mais pesada (~2,9 MB).
