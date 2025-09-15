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
pip install torch --index-url https://download.pytorch.org/whl/cu121 # Versão de CUDA 12.6
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