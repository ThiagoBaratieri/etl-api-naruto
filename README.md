# Akatsuki Data Processing
---
Este projeto realiza a extração, transformação e visualização de dados sobre os personagens da Akatsuki a partir de uma API. O código utiliza as bibliotecas `requests` e `pandas` para obter e manipular os dados.

### Dependências

- `requests`: Para fazer solicitações HTTP à API.
- `pandas`: Para manipulação e análise de dados.

### Código

```python
import requests
import pandas as pd

# Definindo a URL base da API
base_url = 'https://narutodb.xyz/api/akatsuki?limit=44'
response = requests.get(base_url)

# Verificando se a requisição foi bem-sucedida
if response.status_code == 200:
    data = response.json()

# Criação do DataFrame com as colunas desejadas
df = pd.DataFrame(data['akatsuki'], columns=['id', 'name', 'family', 'jutsu', 'natureType', 'personal'])

# Expansão dos dados na coluna 'personal' para colunas separadas e remoção da coluna original
df_expandido_personal = pd.json_normalize(df['personal'])
df_expandido = pd.concat([df.drop(columns='personal'), df_expandido_personal], axis=1).fillna('Sem informação')

# Transformação da coluna 'family': converte dicionários em strings separadas por vírgulas
df_expandido['family'] = df_expandido['family'].apply(
    lambda x: ', '.join(map(str, x.values())) 
    if isinstance(x, dict) and x else 'Sem dados'
)

# Exibindo o DataFrame ordenado por nome
display(df_expandido.sort_values(by='name'))
```

# Explicação do Código
---
### 1. Requisição HTTP e Obtenção dos Dados:

```python
base_url = 'https://narutodb.xyz/api/akatsuki?limit=44'
response = requests.get(base_url)
```
Utiliza a biblioteca `requests` para fazer uma solicitação GET à API da Akatsuki. A URL contém um parâmetro `limit` para limitar a quantidade de dados retornados.

### 2. Criação do DataFrame:

```python
df = pd.DataFrame(data['akatsuki'], columns=['id', 'name', 'family', 'jutsu', 'natureType', 'personal'])
```
Cria um DataFrame `pandas` a partir dos dados retornados pela API. As colunas selecionadas são 'id', 'name', 'family', 'jutsu', 'natureType' e 'personal'.

### 3. Expansão e Manipulação dos Dados:

```python
df_expandido_personal = pd.json_normalize(df['personal'])
df_expandido = pd.concat([df.drop(columns='personal'), df_expandido_personal], axis=1).fillna('Sem informação')
```
Expande os dados da coluna 'personal' em colunas separadas e concatena com o DataFrame original. Valores ausentes são preenchidos com 'Sem informação'.

### 4. Transformação da Coluna 'family':

```python
df_expandido['family'] = df_expandido['family'].apply(
    lambda x: ', '.join(map(str, x.values())) 
    if isinstance(x, dict) and x else 'Sem dados'
)
```
Converte os dicionários na coluna 'family' em strings separadas por vírgulas. Se a coluna não contiver dados, é preenchida com 'Sem dados'.

### 5. Ordenação e Exibição:

```python
display(df_expandido.sort_values(by='name'))
```
Ordena o DataFrame pela coluna 'name' e exibe o resultado.

