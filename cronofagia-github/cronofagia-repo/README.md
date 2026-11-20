# Cronofagia: Paradoxo Coletivo

> *Cada segundo é vida. Cada golpe, um roubo.*

Jogo de ação tática em 3D, direto no navegador (PC e celular). Não existe barra de vida, mana nem munição. O único recurso é o seu **cronômetro**: ele cai 1 segundo por segundo, cada habilidade custa segundos e cada acerto **rouba o tempo** do inimigo.

Protótipo do modo **Arena solo**. Os modos multiplayer estão previstos no [GDD](docs/GDD.md).

---

## Como jogar

### Controles

| Ação | PC | Celular (touch) |
| --- | --- | --- |
| Mover | `W A S D` ou setas | Joystick esquerdo (toque em qualquer ponto da metade esquerda) |
| Mirar | Mouse | Joystick direito |
| Ataque primário | Clique esquerdo (segure para repetir) | Solte o joystick direito |
| Habilidade tática | Clique direito ou `Shift` | Botão **TÁT** |
| Suprema | `E` ou `Espaço` | Botão **SUP** |
| Pausa, ajuda e configurações | `Esc` ou `P` | Botões no canto superior esquerdo |

O jogo troca sozinho entre os controles de PC e de touch, de acordo com o último tipo de entrada usado.

### Regras principais
- **Sobrecarga** (acima de 60s): mais velocidade e ataque primário pela metade do custo.
- **Agonia** (15s ou menos): tela dessaturada e +40% de alcance.
- **Dívida Temporal:** ao zerar, você tem 5s para conseguir um abate. Funciona uma vez por partida.
- **Ressonância:** abates seguidos em menos de 2,5s multiplicam o tempo roubado (até 2×).
- **Inversão de Fluxo:** evento em que as habilidades **dão** tempo, mas ficar parado drena 4s/s.
- **Ciclos e Engrenagens:** a partida é dividida em ondas. Ao limpar uma, você ganha +5s e escolhe 1 de 3 melhorias.
- **Ataques anunciados:** os inimigos avisam antes de atacar, com uma área vermelha no chão ou uma linha de mira.
- **O Colecionador:** chefe que aparece a cada 5 ciclos e absorve o tempo de todo inimigo que expira.
- **Nota de estilo:** vai de D até Ω (Cronófago) e multiplica a pontuação.

### Classes
| Classe | Papel | Relógio | Destaque |
| --- | --- | --- | --- |
| Ceifador | Assassino | 55s | Execução rouba 120% do tempo do alvo |
| Cronomante | Suporte | 65s | Cúpula de Âmbar congela relógios |
| Bastião | Tanque | 80s | Escudo que converte impacto em tempo |
| Paradoxo | Especialista | 50s | Rebobinar 3s e clone-eco |

### Arenas
| Arena | Tema | Modificador |
| --- | --- | --- |
| O Mostrador | Relógio colossal sobre o abismo do tempo | Clássica |
| Areias de Cronos | Relógio de sol em ruínas no deserto | O sol se põe conforme seu tempo acaba |
| O Instante Congelado | Praça noturna com a chuva parada no ar | Inimigos e tiros mais lentos |
| Horizonte de Eventos | Plataforma orbitando um buraco negro | Dilatação temporal no centro |

---

## Rodar no seu computador

O jogo é um site estático, sem etapa de build. Ele precisa ser servido por HTTP, porque abrir o `index.html` direto pelo explorador de arquivos (`file://`) não funciona.

Na pasta do projeto, use uma das opções:

```bash
# Python
python -m http.server 8000

# ou Node.js
npx serve .
```

Depois abra `http://localhost:8000` no navegador.

> É necessária conexão com a internet: a biblioteca Three.js é carregada do CDN jsDelivr.

---

## Publicar no GitHub Pages

1. Crie um repositório novo no GitHub, por exemplo `cronofagia`.
2. Envie os arquivos desta pasta para ele:
   ```bash
   git init
   git add .
   git commit -m "Cronofagia: protótipo 3D"
   git branch -M main
   git remote add origin https://github.com/SEU-USUARIO/cronofagia.git
   git push -u origin main
   ```
   Se preferir sem terminal, use **Add file → Upload files** na página do repositório e arraste todos os arquivos. Inclua também a pasta `docs` e o arquivo `.nojekyll`.
3. No repositório, abra **Settings → Pages**.
4. Em **Build and deployment → Source**, escolha **Deploy from a branch**. Selecione a branch `main` e a pasta `/ (root)` e clique em **Save**.
5. Depois de um ou dois minutos, o jogo fica disponível em `https://SEU-USUARIO.github.io/cronofagia/`.

---

## Estrutura dos arquivos

```
index.html            jogo completo (HTML, CSS e JavaScript)
models.js             personagens 3D (glTF comprimido, em base64)
tex.js                texturas e iluminação HDR da arena "O Mostrador"
arena_areias.js       recursos da arena "Areias de Cronos"   (carregado sob demanda)
arena_cidade.js       recursos da arena "O Instante Congelado" (carregado sob demanda)
arena_horizonte.js    recursos da arena "Horizonte de Eventos" (carregado sob demanda)
docs/GDD.md           Game Design Document
docs/decisoes-tecnicas.md  registro das decisões de design e tecnologia
.nojekyll             faz o GitHub Pages servir os arquivos sem processá-los
```

O download total fica em torno de 5 MB para jogar no Mostrador. Cada outra arena baixa de 0,1 MB a 3 MB extras, só quando é escolhida.

---

## Tecnologia

- [Three.js](https://threejs.org/) r170 (renderização 3D WebGL, pós-processamento, GTAO e bloom)
- JavaScript puro em módulo ES, sem frameworks e sem build
- Áudio 100% procedural (Web Audio API)
- Simulação em 2D renderizada em 3D, o que facilita levar a lógica para um servidor multiplayer (Fase 3 do roadmap)

---

## Créditos e licenças de terceiros

| Recurso | Origem | Licença |
| --- | --- | --- |
| Biblioteca Three.js | [mrdoob/three.js](https://github.com/mrdoob/three.js) | MIT |
| Personagens Soldier, Michelle e Xbot | Exemplos do three.js, originalmente da [Adobe Mixamo](https://www.mixamo.com/) | Termos da Adobe Mixamo |
| Texturas PBR e HDRIs (marble_01, castle_wall_slates, rusty_metal_02, metal_plate, coast_sand_05, sandstone_blocks_08, cobblestone_floor_04, dark_rock_02, bell_tower, dikhololo_sunset, cobblestone_street_night) | [Poly Haven](https://polyhaven.com/) | CC0 (domínio público) |

> **Atenção antes de um lançamento comercial:** os três personagens são modelos de exemplo, usados aqui como provisórios. Substitua-os por personagens próprios, ou baixe-os você mesmo da Mixamo com a sua conta, antes de distribuir o jogo comercialmente.

O código do jogo ainda **não tem uma licença definida**. Se quiser que outras pessoas possam reutilizá-lo, adicione um arquivo `LICENSE` (por exemplo MIT). Se quiser manter todos os direitos, deixe como está.

---

## Roadmap (do GDD)

1. ~~Protótipo jogável single-player com controles PC/touch~~ ✔
2. Empacotamento Android com Capacitor (`.apk` / `.aab`)
3. Multiplayer autoritativo (Node.js + WebSockets) e modo **Último Segundo** (PvP)
4. Modos **Fenda do Relógio** (extração PvPvE) e **Sincronia** (co-op), login unificado e publicação
