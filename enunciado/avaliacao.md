# Entrega e Avaliação

## Grupo

O EP pode ser feito **individualmente ou em grupos de até 3 pessoas**. Todos os integrantes precisam dominar a modelagem e o código — qualquer um pode ser sorteado para apresentar.

## Código fonte

Deverá ser entregue o **repositório no GitHub**. Deve ser implementado usando o ambiente de testes em https://github.com/senac-ia/rf-carro-autonomo

Não é obrigatório, mas preferencialmente usar Python. A correção do professor considera que o algoritmo deve rodar em sua máquina local, portanto, deverá ter as instruções de como rodar e dependências no `README.md`.

**Não será permitido o uso de bibliotecas de Inteligência Artificial pronta** (`gymnasium`, `stable-baselines3`, `ray[rllib]`, `tianshou`, etc.). Você deve implementar o Q-Learning **do zero**. Você pode usar `numpy` e `tqdm` (já listados em `requirements.txt`).

Pode ser baseado no starter code fornecido em https://github.com/senac-ia/rf-carro-autonomo que contém:

- Parser de pistas em emojis (`src/track.py`)
- Ambiente do carro com física e LIDAR (`src/env.py`)
- Visualização animada (`src/visualize.py`)
- 18 pistas (`pistas/pista_01.txt` a `pistas/pista_18.txt`)
- Esqueleto de `solucao.py` para preencher

## Treino e holdout

- **Treino:** pistas **01 a 16** (round-robin — a cada episódio, sorteia-se uma pista do conjunto).
- **Holdout (avaliação):** pistas **17 e 18** — **proibido usar durante o treinamento**.

O EP avalia a capacidade do agente de **generalizar** para pistas não vistas; treinar nas pistas de holdout é considerado *data leakage* e descaracteriza o EP.

## Salvamento do modelo treinado

Como o treinamento (480 mil episódios) pode demorar 30-60 minutos em CPU, você **deve salvar o modelo treinado em disco**. Use `pickle` (ver [`anexo_b_pickle.md`](anexo_b_pickle.md)).

Salve **um único arquivo**, em `/treinamento/qlearning.pkl`:

```
seu-projeto/
├── README.md
├── solucao.py
├── src/
├── pistas/
├── docs/                      ← seu relatório vai aqui
└── treinamento/
    └── qlearning.pkl         ← Q-Learning treinado em round-robin nas 16 pistas
```

Esse arquivo deve ser **commitado no repositório**. Isso permite ao professor reproduzir a avaliação sem re-treinar.

## Relatório (em `docs/`)

**Toda a documentação do EP fica em `docs/`** do seu repositório. Sugestão: `docs/relatorio.md` como arquivo principal, com sub-arquivos para tópicos densos se necessário.

O relatório obrigatoriamente cobre:

1. **Escolha dos hiperparâmetros** — para $\alpha$, $\gamma$ e $\varepsilon$-schedule, qual valor você usou e por quê? Houve experimentação?
2. **Mecânica de exploração** — como o agente escolhe entre explorar e exploitar a cada passo? Houve variações além do $\varepsilon$-greedy clássico?
3. **Implementação** — modelagem do MDP, estrutura da tabela $Q$, discretização, esquema de treinamento round-robin.
4. **Resultado nas pistas de holdout 17 e 18** — métricas, comparação com treino, análise de generalização, inspeção qualitativa via `src/visualize.py`.

## O que será avaliado

### Em apresentação

- **Representação do espaço de estados:**
    - Como você implementou a discretização do vetor de 6 floats com $K = 5$?
    - Qual o tamanho real da tabela $Q$ ao final do treinamento? Como esse número se compara ao máximo teórico de $5^6 \times 5 = 78{.}125$ entradas?
- **Espaço de ações:** como as 5 ações foram codificadas?
- **Função de recompensa:** como o reward shaping foi implementado? Você experimentou variações?
- **Política de exploração:** schedule de $\varepsilon$, justificativa.
- **Estratégia de treinamento round-robin:** como você seleciona pistas a cada episódio? Como compensa pistas mais difíceis?
- **Generalização:** desempenho do agente em pista_17 e pista_18 (holdout). Há queda em relação ao treino? Como interpretar?

> **Atenção:** fazer cópia do algoritmo apenas e explicar o que é o conceito **não vale**. O trabalho requer a explicação de como o conceito foi **modelado e implementado para este problema específico** (pilotar um carrinho).

### Em código

- **Implementação do Q-Learning** do zero.
- **Função de discretização** com $K = 5$.
- **Loop de treinamento round-robin** nas pistas 01-16, que registra histórico de recompensas por episódio (salvo no pickle do modelo).
- **Loop de avaliação** com $\varepsilon = 0$ nas pistas de holdout (17 e 18), que gera `q_learning_pista_17.txt` e `q_learning_pista_18.txt`.
- **Inspeção da política final** via animação no terminal (`renderizar_episodio` em `src/visualize.py`) nas pistas de holdout — descreva no relatório o que observou.
- **Salvamento e carregamento** do modelo via pickle único em `/treinamento/qlearning.pkl`.

### Critérios de avaliação

- Explicação da lógica do problema e da modelagem do MDP.
- Explicação da discretização adotada (por que $K = 5$ funciona bem aqui).
- Explicação das funções principais e estrutura do código.
- Demonstração dos resultados (curva de aprendizado em formato textual, animação do agente no terminal, métricas nas pistas de holdout).
- **Análise crítica de generalização:** o que a diferença treino-vs-holdout revela sobre a representação de estado (LIDAR local) e a capacidade do Q-Learning tabular?
- Criatividade — extensões além do mínimo, exploração de variações na função de recompensa, na política de seleção de pista no round-robin, etc.

## Política de uso de ferramentas

Este trabalho deve seguir:

- [Política de uso de ferramentas generativas de IA](https://www.notion.so/...)
- [Política antiplágio](https://www.notion.so/...)
