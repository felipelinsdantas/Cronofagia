# Game Design Document (GDD 3.0): *Cronofagia: Paradoxo Coletivo*

**Edição Multiplataforma (Web + Android / Cross-Play Nativo)**

---

## 1. Visão Geral do Projeto

* **Título:** Cronofagia: Paradoxo Coletivo
* **Gênero:** Ação Tática Multiplayer / Extraction & PvPvE de Economia Temporal
* **Plataformas Alvo:** Web (Navegadores Desktop/Mobile) e Android (`.apk` / `.aab` empacotado via Capacitor/TWA), com **Cross-Play total** no mesmo servidor.
* **Escala de Jogadores:** Sessões de **2 a 12 jogadores** em tempo real.
* **Conceito Central:** Não existe barra de vida, mana ou munição. O único recurso vital é o **cronômetro regressivo** do jogador. Cada habilidade consome segundos reais da sua existência, e a única forma de sobreviver é abater inimigos ou rivais para absorver o tempo restante deles, cooperando ou traindo outros jogadores em partidas online.

---

## 2. Arquitetura Multiplataforma e Controles Híbridos

O cliente detecta automaticamente o dispositivo (`pointer: coarse` / suporte a toque vs. mouse/teclado) e adapta a interface e a assistência de mira sem alterar as regras matemáticas da partida, garantindo equilíbrio competitivo entre PC e Android.

### Mapeamento de Controles por Plataforma

| Ação | PC (Teclado + Mouse) | Android (Touchscreen Twin-Stick) | Custo / Regra |
| --- | --- | --- | --- |
| **Mover** | `W, A, S, D` ou Setas | **Joystick Esquerdo Flutuante** (aparece onde o polegar esquerdo tocar na metade esquerda da tela). | `0s` (apenas o fluxo natural de `1.0s/s`). |
| **Mirar e Atacar (Primária)** | Cursor do Mouse + **Clique Esquerdo** | **Joystick Direito de Disparo:** arrastar exibe a linha de mira; **soltar o dedo executa o ataque**. | `-1.0s` a `-2.5s` (conforme classe). |
| **Habilidade Tática (Dash / Suporte)** | **Clique Direito** ou `Shift` | **Botão Dedicado Tático** (arco superior direito); usa a direção da mira ou do movimento atual. | `-2.0s` a `-5.0s`. |
| **Habilidade Suprema** | Tecla `E` ou `Espaço` | **Botão Dedicado Suprema** (arco lateral direito) com indicador radial de custo mínimo. | `-6.0s` a `-8.0s`. |
| **Doar Tempo (Co-op)** | Tecla `F` mirando em aliado | **Toque rápido** sobre o retrato do aliado no HUD ou botão contextual próximo. | Transfere `5.0s` instantâneos. |

### Ajustes Específicos para Android (UX Mobile)

1. **Assistência de Mira (*Soft Lock-On*):** No modo touch, ataques corpo a corpo e projéteis possuem um cone de magnetismo angular de `15°` em direção ao alvo mais próximo na linha de mira, compensando a menor precisão do dedo em comparação ao mouse.
2. **Orientação e Área Segura (*Safe Area*):** Trava obrigatória em modo paisagem (*Landscape*) com margens dinâmicas (`env(safe-area-inset-*)`) para não esconder o cronômetro ou botões atrás de câmeras frontais (*notches*) e barras de gestos do Android.
3. **Feedback Háptico (Vibration API / Capacitor Haptics):**
* Pulso curto (`15ms`) ao acertar um golpe e drenar tempo.
* Pulso duplo (`40ms`) ao sofrer dano ou entrar em **Dívida Temporal**.
* Batimento contínuo sutil quando o relógio cai abaixo de `15.0s`.


4. **Modo Economia de Bateria:** Opção nas configurações para travar o renderizador em `60 FPS` ou `30 FPS` e reduzir partículas ambientais em aparelhos Android de entrada.

---

## 3. Modos de Jogo Multiplayer

### A. *Fenda do Relógio* (Extração PvPvE — 8 a 12 jogadores em trios ou solo)

* **Objetivo:** Sobreviver por até `10:00` minutos, acumular tempo abatendo anomalias e rivais, e ativar uma das **3 Cápsulas de Extração**.
* **Mecânica de Extração:** Ativar uma cápsula exige pagar `45.0s` do seu relógio. Durante os `8.0s` de carregamento, um sinal sonoro e visual alerta todos os jogadores num raio de `1200px`, transformando a extração no clímax da partida.

### B. *Sincronia* (Cooperativo PvE — 2 a 4 jogadores)

* **Objetivo:** Superar 15 câmaras procedurais gerenciando saldos individuais (`40.0s`) e o **Relógio Matriz** compartilhado do esquadrão (`120.0s`).
* **Regra de Resgate:** Ao zerar o relógio, o jogador entra em *Estase Cadavérica* e passa a drenar `3.0s/s` do Relógio Matriz até que um aliado gaste `10.0s` próprios para reanimá-lo.

### C. *Último Segundo* (Arena PvP FFA — 4 a 8 jogadores)

* **Objetivo:** Partida rápida de `3 a 5 minutos` (ideal para sessões curtas no celular). Todos iniciam com `45.0s`; vence o último jogador restante na arena.

---

## 4. Arquétipos (Classes de Relojoaria)

| Arquétipo | Relógio Base | Primária (Clique Esq. / Soltar Dedo) | Tática (Clique Dir. / Botão 1) | Suprema (`E` / Botão 2) |
| --- | --- | --- | --- | --- |
| **Ceifador** (Assassino) | `55.0s` | **Lâmina Curta** (`-1.2s`): Corte rápido em arco. | **Salto de Fase** (`-2.0s`): Dash intangível. | **Execução** (`-6.0s`): Se abater o alvo, rouba `120%` do tempo dele. |
| **Cronomante** (Suporte) | `65.0s` | **Pulso de Atraso** (`-1.5s`): Aumenta em `+1.5s` o custo das próximas 2 ações do alvo. | **Transfusão** (`-5.0s`): Doa `5.0s` a um aliado ou drena `3.5s` de um rival. | **Cúpula de Âmbar** (`-8.0s`): Congela o relógio de todos dentro da área por `4.0s`. |
| **Bastião** (Tanque) | `80.0s` | **Martelo Pendular** (`-2.5s`): Golpe pesado com repulsão. | **Escudo de Inércia** (`-1.0s/s`): Converte `50%` dos impactos bloqueados em tempo. | **Ancoragem** (`-7.0s`): Puxa inimigos próximos e aplica `60%` de lentidão. |
| **Paradoxo** (Especialista) | `50.0s` | **Agulha Dupla** (`-1.0s`): Disparo rápido de médio alcance. | **Rebobinar** (`-4.5s`): Volta à posição e ao saldo de `3.0s` atrás (máx. `+10s`). | **Clonagem Eco** (`-6.5s`): Invoca um fantasma que repete suas ações recentes. |

---

## 5. Mecânicas Sistêmicas e Estados Temporais

### Estados do Jogador

* **Sobrecarga (`> 60.0s`):** Velocidade `+25%` e custo da habilidade primária reduzido pela metade.
* **Agonia (`<= 15.0s`):** Tela dessaturada, som de batimento cardíaco e `+40%` de alcance nos ataques.
* **Dívida Temporal (`0.0s` a `-10.0s`):** Uma vez por partida, ao atingir zero, o jogador ganha `5.0s` de sobrevida em saldo negativo. Se conseguir um abate e voltar a ficar positivo antes dos 5s acabarem, sobrevive; caso contrário, é eliminado.

### Dinâmicas de Partida

* **Ressonância de Abate:** Abates consecutivos em menos de `2.5s` aplicam multiplicadores (`1.25x` → `1.5x` → `2.0x`) sobre o tempo absorvido.
* **Inversão de Fluxo (Evento Climático de 15s):** Durante o evento, usar habilidades *adiciona* tempo e ficar imóvel *drena* `4.0s/s`.

---

## 6. Bestiário (Entidades PvE)

| Inimigo | Relógio | Comportamento | Papel Tático |
| --- | --- | --- | --- |
| **Efêmero** (Ciano) | `6.0s` | Corre para explodir (`-5.0s`). | Abate rápido gera lucro imediato; se expirar sozinho, não dá nada. |
| **Sentinela** (Branco) | `12.0s` | Dispara orbes lentos à distância (`-4.0s`). | Controla corredores e exige uso de cobertura ou Dash. |
| **Ancorado** (Âmbar) | `22.0s` | Blindado na frente; vulnerável nas costas. | Reserva estratégica de tempo para recuperar saldo. |
| **Parasita** (Magenta) | `10.0s` | Drena `2.5s/s` contínuos via linha de visão. | Alvo prioritário; obriga reposicionamento imediato. |
| **Inverso** (Violeta) | `1.0s` ↑ | Seu relógio **sobe** até `20.0s`, ficando maior e mais forte. | Dilema entre matar cedo (pouco tempo) ou esperar engordar. |
| **O Colecionador** (Chefe) | `30s`–`150s` | Absorve o tempo de monstros que expiram no mapa. | Objetivo global disputado por todos os jogadores da sessão. |

---

## 7. Direção Visual, UI Responsiva e Áudio

* **Estética Geométrica de Alto Contraste:** Fundo grafite (`#121418`) com silhuetas neon bem definidas para garantir legibilidade perfeita tanto em monitores grandes quanto em telas de celular de `6 polegadas` sob luz externa.
* **Avatar como Indicador de Vida:** O polígono do jogador perde lados fisicamente conforme o tempo cai (`12 lados` em `60s+` → `6 lados` em `30s` → `3 lados` em `15s`).
* **HUD Escalável:** Números grandes em fonte monoespaçada, cronômetros flutuantes legíveis acima dos inimigos e ausência de textos longos durante o combate.
* **Áudio Procedural (Web Audio API):** Funciona nativamente no navegador e no Android sem pacotes pesados de MP3, gerando tique-taques dinâmicos, impactos metálicos e alertas sonoros em tempo real.

---

## 8. Arquitetura Técnica (Web + Android + Multiplayer)

### Stack Tecnológica Unificada

* **Frontend Único:** HTML5 Canvas 2D (ou PixiJS leve) + JavaScript modular (`ES Modules`), empacotado com Vite.
* **Build Android:** **Capacitor** gerando `.apk` / `.aab` com acesso nativo a vibração háptica, tela cheia imersiva e prevenção de bloqueio de tela (`KeepAwake`).
* **Backend Autoritativo:** Node.js + WebSockets (`ws` ou `Colyseus`) rodando simulação a `30 ticks/s`.

### Resiliência para Redes Móveis (4G/5G) e Cross-Play

1. **Protocolo Binário Compacto:** Pacotes de estado empacotados em `ArrayBuffer` (`< 15 KB/s`), economizando dados móveis e reduzindo latência.
2. **Buffer de Interpolação Adaptativo:** O cliente ajusta automaticamente o atraso de renderização remota entre `80ms` (Wi-Fi/Cabo) e `150ms` (4G/5G com oscilação), evitando engasgos visuais.
3. **Reconexão Rápida de Sessão (`Session Token`):** Se um jogador no Android minimizar o app para responder uma notificação ou alternar entre Wi-Fi e dados móveis, o cliente reconecta automaticamente à mesma partida por até `20 segundos` sem perder seu personagem.
4. **Identidade Multiplataforma:** Login opcional via Conta Google / Código de Recuperação para sincronizar **Areia Eterna** (moeda cosmética) e desbloqueios entre o navegador do PC e o aplicativo Android.

---

## 9. Roadmap de Produção

1. **Fase 1 — Protótipo Híbrido Jogável (Singleplayer):** Arena base em HTML5 Canvas com detecção automática PC/Touch, joysticks virtuais, assistência de mira mobile, 4 arquétipos e Dívida Temporal.
2. **Fase 2 — Empacotamento Android Inicial:** Configuração do Capacitor para gerar o primeiro `.apk` de teste, validar FPS em celulares intermediários e calibrar ergonomia dos botões virtuais.
3. **Fase 3 — Multiplayer Autoritativo & Rede Móvel:** Servidor Node.js WebSocket com predição de movimento, interpolação adaptativa para 4G/5G e Modo Arena (*Último Segundo*).
4. **Fase 4 — Modos Extração/Co-op & Lançamento:** Adição do mapa maior com névoa de guerra, *O Colecionador*, login unificado e publicação simultânea na Web e Google Play.