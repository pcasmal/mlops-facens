# MLOps e Deploy de Modelos em Produção — Facens

Repositório único da disciplina. Ele cresce a cada encontro: versionamento e governança (Aula 1),
API e deploy (Aula 2), monitoramento e retreino (Aula 3), segurança e incidentes (Aula 4).

**Problema do curso:** prever a demanda horária de bicicletas compartilhadas
(*Bike Sharing Dataset*, UCI — Fanaee-T & Gama, 2013, licença CC BY 4.0).

## Começando

1. Clique em **Use this template → Create a new repository** (deixe **público**).
2. No seu repositório: **Code → Codespaces → Create codespace on main**.
   A primeira abertura instala tudo sozinha. Espere terminar antes de seguir.
3. Abra um terminal (**Ctrl + `**) e suba o MLflow. **Deixe esse terminal aberto:**
   ```bash
   bash scripts/start_mlflow.sh
   ```
4. Na aba **PORTAS** (PORTS), clique no 🌐 da porta **5000** para abrir a interface do MLflow.
5. Os notebooks das tarefas são liberados durante a aula. Arraste cada um para a pasta `aula01/` do seu Codespace.

## Estrutura

```
├── .devcontainer/        # ambiente do Codespaces (Python 3.12 + dependências)
├── scripts/
│   └── start_mlflow.sh   # servidor MLflow local (SQLite + artefatos em ./mlartifacts)
├── src/                  # código "de produção" do projeto
│   ├── bike.py           #   features, split temporal, métricas
│   ├── get_data.py       #   baixa os dados "disponíveis até" uma data
│   ├── prepare.py        #   estágio prepare  (limpeza)
│   ├── split.py          #   estágio split    (treino/teste no tempo)
│   ├── train.py          #   estágio train    (MLflow + Model Registry)
│   ├── evaluate.py       #   estágio evaluate (metrics.json)
│   └── plot.py           #   estágio plot     (gráfico real × previsto)
├── params.yaml           # hiperparâmetros do pipeline
└── aula01/               # aqui entram os notebooks das tarefas, liberados durante a aula
```

## Problemas comuns

| Sintoma | Solução |
|---|---|
| A interface do MLflow mostra *Invalid Host header* ou erro 403 | Suba o servidor **pelo script** (`bash scripts/start_mlflow.sh`), não com `mlflow ui`. |
| `Connection refused` na porta 5000 | O Codespace dorme após 30 min sem uso e o servidor cai. Rode o script de novo: nada se perde (fica em `mlflow.db`). |
| O notebook pede para escolher kernel | Escolha o **Python 3.12** do ambiente. |
| `dvc: command not found` ou `mlflow: command not found` | Feche e abra o terminal. Se persistir: `export PATH="$PATH:$HOME/.local/bin"` |
| `git commit` pede nome/e-mail (fora do Codespaces) | `git config --global user.name "Seu Nome"` e `git config --global user.email voce@exemplo.com` |

**Cota gratuita do Codespaces:** cerca de 60 h/mês em máquina de 2 núcleos. Ao terminar, pare o Codespace
(**Code → Codespaces → ⋯ → Stop codespace**). **Não apague** o Codespace: o MLflow da disciplina vive nele.

## Plano B (sem Codespaces)

- **No seu computador:** Python 3.12, `pip install -r requirements.txt` e os mesmos comandos.
- **No Google Colab (só T2 e T3):** clone o repositório, instale as dependências e use o MLflow sem servidor:
  ```python
  !git clone https://github.com/<seu-usuario>/<seu-repo>.git
  %cd <seu-repo>
  !pip install -q -r requirements.txt
  import os; os.environ["MLFLOW_TRACKING_URI"] = "sqlite:///mlflow.db"
  !python src/get_data.py --until 2011-12-31   # os dados v1, como na T0
  ```
  Sem interface gráfica: use `mlflow.search_runs()` para ver os resultados.
  DVC (T1/T4) não é recomendado no Colab, porque a máquina é apagada a cada sessão.
