# Graphormer

## Dependências

```
pip install python-dotenv
pip install -U transformers==4.40.2
pip install -U datasets 
pip install -U evaluate
pip install -U scikit-learn
pip install -U networkx
pip install -U matplotlib
pip install -U Cython
pip install -U accelerate
pip install -U torch
```

## Reparar erro do Graphormer

Abrir o Arquivo:

    "..\venv\Lib\site-packages\transformers\models\graphormer\algos_graphormer.pyx"

Onde tem:

    path_copy = path.astype(long, order='C', casting='safe', copy=True)
    edge_feat_copy = edge_feat.astype(long, order='C', casting='safe', copy=True)

Substituir por:

    path_copy = path.astype(np.int64, order='C', casting='safe', copy=True)
   	edge_feat_copy = edge_feat.astype(np.int64, order='C', casting='safe', copy=True)

Limpar Cache:

```powershell
$ Remove-Item "..\venv\Lib\site-packages\transformers\models\graphormer\__pycache__" -Recurse -Force
```

## Dataset


### Json

```json
{
  "graph_id": 0,
  "pair": [A, B],
  "path_len": 5,
  "num_nodes": 8,
  "edge_index": [[...],[...]],
  "edge_attr": [...],
  "current_node": 3,
  "target_node": 7,
  "candidate_node": 4,
  "labels": [1]
}
```

---

### Descrição

* **`graph_id`: 0**
  Identificador único do grafo de onde essa amostra foi gerada. Facilita rastrear várias amostras vindas do mesmo grafo.

* **`pair`: \[A, B]**
  Par de nós origem–destino escolhido no grafo.

  * `A`: nó de origem da comunicação.
  * `B`: nó de destino final (interesse, no caso do RADNET).
    Esse par define o caminho “verdadeiro” que usamos para rotular.

* **`path_len`: 5**
  Comprimento (número de nós) do caminho ótimo entre `A` e `B`, calculado pelas métricas de custo (RSSI, delay, perda etc.).

* **`num_nodes`: 8**
  Número total de nós do grafo. Aqui, o grafo tem 8 nós numerados de 0 a 7.

* **`edge_index`: \[\[...],\[...]]**
  Lista de arestas direcionadas, formato padrão PyG/Graphormer.

  * Exemplo: `[[0,1,1,2],[1,0,2,1]]` representa arestas 0→1, 1→0, 1→2, 2→1.
  * Sempre duplicamos as arestas para capturar as duas direções.

* **`edge_attr`: \[...]**
  Lista de inteiros, um por aresta (mesmo comprimento de `edge_index`).
  Cada inteiro é um **ID de tipo de aresta**, que codifica:

  * Qualidade do link (RSSI, delay, perda, jitter, banda → binarizados).
  * Flags se a aresta toca o nó atual (`current_node`), o destino (`target_node`) e o vizinho candidato (`candidate_node`).
  * Intervalo: 0–255.

* **`current_node`: 3**
  Nó em que estamos no momento (onde a decisão de roteamento acontece).

* **`target_node`: 7**
  Nó de destino final (para onde queremos encaminhar a mensagem).

* **`candidate_node`: 4**
  Vizinho de `current_node` que estamos avaliando como **possível próximo salto**.

* **`labels`: \[1]**
  Classe binária:

  * `1`: este `candidate_node` (4) **é de fato** o próximo salto correto no caminho ótimo de `3 → … → 7`.
  * `0`: se não fosse o próximo salto correto.

---

