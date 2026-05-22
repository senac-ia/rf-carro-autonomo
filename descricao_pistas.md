# Design das 18 pistas — versão com progressão pedagógica

## Pedido do usuário (revisado)
- Desenhar 18 pistas com curvas (abertas, fechadas, à esquerda, à direita).
- 3 níveis de dificuldade (6 pistas por nível).
- **Corredor máximo nas fáceis: 4 células**
- **Corredor nas médias: 4 que afunila para 3 (pode aumentar no meio do caminho)**
- **Corredor nas difíceis: pode ir até 2 células**
- **Fáceis devem ter progressão pedagógica** — cada uma introduz um conceito novo que Q-Learning precisa aprender.
- **Médias devem ser mais difíceis** que estão hoje, combinando mais elementos.
- Variabilidade dentro de cada pista (não usar corredor uniforme).

## Formato dos arquivos
- Emojis separados por espaços; linhas separadas por `\n`.
- `🧱` = parede, `⚪` = asfalto, `🟢` = largada, `🏁` = chegada.
- Carro larga voltado para leste; primeira manobra deve ser viável.
- Carro: V_MAX=2.0, vira 30° por ação, LIDAR alcance 10 células.

## Tabela de larguras por nível

| Nível | Corredor mínimo | Corredor máximo | Variação típica |
|---|---|---|---|
| Fácil (01–06) | 3 | **4** | 3↔4 |
| Médio (07–12) | 3 | 4 | 3↔4 com chicanes/curvas combinadas |
| Difícil (13–18) | **2** | 4 | 2↔3↔4 |

## NÍVEL 1 — FÁCIL (progressão pedagógica)

A ideia é que cada pista introduza **uma habilidade nova** que o agente precisa aprender. Q-Learning generaliza pouco entre estados — então a sequência expõe o agente a situações cada vez mais ricas.

### pista_01 — "Reta + curva única à direita" (~30×20)
**Conceito novo:** reagir a uma parede frontal e virar.
Reta corredor 4, curva 90° à direita, vertical corredor 3.
Carro aprende: "quando a LIDAR frontal cai, freio e viro à direita".

### pista_02 — "Reta + curva única à esquerda" (~30×20)
**Conceito novo:** mesma situação mas virando para o LADO OPOSTO.
Layout diferente da 01 (não é espelho): horizontal corredor 3 + vertical corredor 4. Inverte tanto direção quanto larguras.
Carro aprende: virar à esquerda quando convém — generalização da política.

### pista_03 — "S aberto (duas curvas opostas)" (~32×20)
**Conceito novo:** decidir sequência de viradas.
Corredor 4 → afunila para 3 no conector → volta a 4. Curva direita seguida de curva esquerda.
Carro aprende: nem toda parede vira para o mesmo lado; sequenciar ações.

### pista_04 — "Curva contínua (arco amplo)" (~30×22)
**Conceito novo:** curva SUAVE (gradual) em vez de curva fechada (90° abrupto).
Degraus de larguras 3, 4, 3, 4 aproximando arco contínuo de ~120°.
Carro aprende: ajuste de ângulo em pequenos incrementos (sem precisar freiar muito).

### pista_05 — "U-turn (180°)" (~32×22)
**Conceito novo:** voltar para o sentido oposto.
Top corredor 4, lateral direita corredor 3, fundo corredor 4. Carro precisa fazer 180° efetivo.
Carro aprende: orientação espacial não importa, só o que LIDAR mostra à frente.

### pista_06 — "Múltiplas curvas combinadas" (~30×22)
**Conceito novo:** transição entre 3+ direções em sequência.
Três segmentos com larguras 4, 3, 4, conectados por duas curvas de ~45°.
Carro aprende: encadear múltiplas decisões sem reset mental.

## NÍVEL 2 — MÉDIO (mais difícil, curvas combinadas)

Médias agora combinam pelo menos 2 elementos diferentes (chicane + curva fechada, ou U-turn + chicane, etc.) e têm mais curvas que antes.

### pista_07 — "Z + chicane apertada" (~40×28)
**Conceito novo:** combinar curva fechada de 90° com chicane (S de 3 curvas).
2 curvas 90° fechadas + chicane. Retas corredor 4, **chicane afunila para 3**. Total: 5 curvas.
Carro aprende: alternar entre tipos de curva diferentes na mesma pista — modular velocidade entre curva 90° abrupta e chicane mais sutil.

### pista_08 — "Ondas irregulares + final apertado" (~42×26)
**Conceito novo:** sustentar atenção em S-curves repetidas com amplitudes DIFERENTES.
4 ondas com profundidades diferentes (funda/rasa/funda/média) + reta final que **afunila para 3**. Corredor base 4.
Carro aprende: não dá pra decorar amplitude — cada onda exige leitura nova do LIDAR. Quebra qualquer "atalho mental" de Q-Learning.

### pista_09 — "270° circuito + chicane no fundo" (~42×30)
**Conceito novo:** combinar curva longa (orientação muda muito) com chicane local.
3 curvas 90° formando 270° MAIS uma chicane no trecho inferior. Corredor 4 nas retas, 3 nas chicanes.
Carro aprende: alternar entre regimes — "manter alta velocidade na curva longa" vs "frear pra chicane". Planejamento em escalas diferentes.

### pista_10 — "Escada longa com afunilamento" (~48×30)
**Conceito novo:** repetir o mesmo padrão (vira-anda-vira) com larguras alternadas.
6 degraus alternados, corredor alterna 4 (degraus largos) e 3 (degraus estreitos). Comprimentos irregulares: 7, 14, 9, 13, 7, 11.
Carro aprende: a rotina é a mesma mas as larguras mudam — política precisa generalizar entre larguras parecidas.

### pista_11 — "L duplo + chicane dupla" (~42×32)
**Conceito novo:** chicanes encadeadas (sem reta longa entre elas).
Duas L de 90° + DUAS chicanes (S apertados) consecutivas entre elas. Corredor 4 nas retas, 3 nas chicanes. Total: ~8 curvas.
Carro aprende: planejar a saída de uma chicane enquanto entra na próxima — não dá tempo de "estabilizar" entre obstáculos.

### pista_12 — "U-turn + chicane na descida" (~42×32)
**Conceito novo:** chicane em orientação inusitada (carro indo norte→sul, não leste).
Sobe (corredor 3), top do U (corredor 4 — alarga), desce (corredor 3) MAS na descida tem uma chicane embutida. Reta final 4.
Carro aprende: a chicane fica em meio de uma orientação não-padrão — força generalização (LIDAR e ações são as mesmas independente de orientação global).

## NÍVEL 3 — DIFÍCIL (pode ter trechos corredor 2)

### pista_13 — "Zigue-zague irregular com gargalo" (~58×24)
**Conceito novo:** identificar e antecipar uma passagem apertada (corredor 2) num padrão repetitivo.
5 ondas com bridges de larguras variadas: corredor 3 padrão, **uma bridge corredor 2** (passagem apertada) e uma corredor 4 (folga).
Carro aprende: usar os LIDARs laterais (±60°) para detectar gargalo antes de entrar e desacelerar. Quebra a tentação de "manter rotina" do zigue-zague.

### pista_14 — "Duplo U-turn + chicane apertada" (~52×38)
**Conceito novo:** alternar entre curva ampla (raio grande) e curva apertada (corredor 2).
U-turn 1 corredor 4, **chicane corredor 2** (super apertada), U-turn 2 corredor 3 (raio menor).
Carro aprende: modular velocidade rapidamente — alta nos U-turns largos, baixa na chicane apertada. Penaliza muito "uma única velocidade".

### pista_15 — "Espiral até 2 no miolo" (~50×40)
**Conceito novo:** mesmo tipo de curva (90°) mas com corredor cada vez mais estreito.
1.5 voltas em espiral, **corredor 3 nos anéis externos, afunila para 2 no miolo final**.
Carro aprende: a decisão "virar 90° à direita" é a mesma sempre, mas a velocidade segura cai a cada anel. Velocidade não pode ser fixa — é função da largura.

### pista_16 — "5 chicanes com gargalos" (~56×32)
**Conceito novo:** variabilidade dentro do mesmo padrão (chicanes com profundidades e larguras diferentes).
Corredor 3 base, 2 das 5 chicanes têm seções **corredor 2**, alternando profundidades top→mid e top→bot.
Carro aprende: não dá pra decorar "a chicane" — cada uma tem profundidade e largura diferentes. Força política baseada em LIDAR, não em "memória" do estágio.

### pista_17 — "Circuito misto com gargalo" (~52×42)
**Conceito novo:** planejamento de trajetória global em pista longa com obstáculos heterogêneos.
Loop externo corredor 4, **chicane lateral corredor 2**, U-turn interno corredor 3.
Carro aprende: estratégia tem que sobreviver a uma volta completa com múltiplos obstáculos diferentes — chicane forte num lado, curvas amplas no outro.

### pista_18 — "Serpente combinada" (~62×40)
**Conceito novo:** combinação final de TODOS os elementos aprendidos.
Corredor base 4, várias seções 3, **uma seção corredor 2** (passagem forçada). Combina U-turn, chicane tripla, curvas 90° fechadas, retas longas.
Carro aprende: teste final — a política treinada precisa funcionar para qualquer combinação dos conceitos vistos nas pistas anteriores. Se aprendeu mal algum conceito específico, falha aqui.

## Justificativa da progressão pedagógica

Q-Learning tabular não generaliza entre estados — o agente precisa **visitar variações** para aprender uma política robusta. A progressão das fáceis garante isto:

1. **01**: política mínima viável (1 curva).
2. **02**: simetria — o agente não pode aprender só "vira direita".
3. **03**: sequência — política depende do contexto, não só do estado atual.
4. **04**: gradiente — viradas suaves em vez de "tudo ou nada".
5. **05**: rotação 180° — orientação não importa, só LIDAR.
6. **06**: encadeamento — múltiplas decisões consecutivas.

Já nas médias, o agente combina esses elementos sob pressão (corredor mais estreito ou mais curvas seguidas). Difíceis exigem velocidade controlada (gargalos corredor 2).
