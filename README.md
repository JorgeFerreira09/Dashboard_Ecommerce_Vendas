<div align="center">
  <h1> Dashboard Analítico de Vendas no E-commerce   </h1>
</div> <br>  

Estruturação do Projeto: <br>
🟣 Objetivo <br>
🟣 Apresentação visual <br>
🟣 Insights para tomador de decisão<br>
🟣 Tecnologias utilizadas<br>
🟣 Passo a passo <br><br>


### I. Objetivo  
Analisar o desempenho de vendas de uma grande loja virtual por categoria de produto e perfil demográfico, considerando estado, gênero e profissão. <br><br>


### II. Apresentação Visual
  
  ![Captura de tela 2025-05-13 102812](https://github.com/user-attachments/assets/e9efd793-5cac-47b3-ba99-ce8a6db948f4) 

  ![Captura de tela 2025-05-13 103842](https://github.com/user-attachments/assets/055641dd-aa7a-42ff-b9d4-4ba4b511e8f4)

  ![Captura de tela 2025-05-13 103900](https://github.com/user-attachments/assets/1de67d94-a651-431f-a733-ae991a56b588)

<br>

### III. Insights para Tomador de Decisão 

<br>

___✔️ As mulheres compraram 5x mais produtos que os homens na categoria “sports”. Essa categoria pode ter majoritariamente produtos destinados para as mulheres como calças legging, top, maiô de natação. É preciso verificar a composição de produtos "sports".___ <br><br>

___✔️  As vendas estão concentradas no público feminino (45%) e masculino (42%) no geral. Cada um dos demais gêneros - Genderfluid, Agender, Genderqueer, Non-binary, Poly Gender, Bi Gender - representa menos de 5% das vendas. Isso pode indicar que as ações de marketing não estão se comunicando de forma efetiva com esses públicos. Tornar a comunicação mais inclusiva pode ser uma solução.___ <br><br>

___✔️  A categoria “Beauty” lidera as vendas, com distribuição equilibrada entre os estados americanos e destaque para a Geórgia (GA), que representa 3x mais vendas que os demais. No entanto, __não houve nenhuma venda__ dessa categoria para os gêneros Genderfluid, Agender, Genderqueer e Non-binary. Isso indica falha na estratégia de inclusão da marca, seja na comunicação ou na falta de produtos "Beauty" destinados para esse público.___ <br><br>   


### IV. Tecnologias utilizadas

☑️ SQL <br>
☑️ Pandas <br>
☑️ Python <br>
☑️ Power BI <br>
☑️ Google Colab <br><br>


### V. Passo a Passo

<br>

🟪 **Análise Exploratória dos dados com SQL** <br>

___1º Importação das bibliotecas sqlite3 e pandas e carregamento das duas bases de dados a partir de arquivos CSV.___<br>

```
import sqlite3
import pandas as pd

df_transacoes = pd.read_csv("TB_TRANSACOES.csv", delimiter=';')
df_clientes = pd.read_csv("TB_CLIENTES.csv", delimiter=';')

conn = sqlite3.connect('projeto.db')
df_transacoes.to_sql('TB_TRANSACOES', conn, index=False, if_exists='replace')
df_clientes.to_sql('TB_CLIENTES', conn, index=False, if_exists='replace')

def run_query(query):
    return pd.read_sql_query(query, conn)
```

<br>

___2º Conhecendo a Tabela Transação com SQL.___<br>

```
query = "SELECT * FROM TB_TRANSACOES LIMIT 5"
result_df = run_query(query)
print(result_df)
```
Resultado da consulta de 5 linhas:
```
   id_client     Category   Price   Card Type
0         37  Electronics   72,93  mastercard
1         38      Jewelry  121,89  mastercard
2         39         Baby    64,3  mastercard
3         40     Outdoors    9,48  mastercard
4          5     Outdoors   61,95  mastercard
```

<br>

___3º Conhecendo a Tabela Clientes com SQL.___ <br>
```
query = "SELECT * FROM TB_CLIENTES LIMIT 10"
result_df = run_query(query)
print(result_df)
```
Resultado da consulta de 10 linhas:

```
  state_name First_name  Gender                     Job_Title  Id_client
0         TX    Domingo    Male  Structural Analysis Engineer          1
1         MI    Russell    Male            Speech Pathologist          2
2         AL     Kimble    Male           Account Coordinator          3
3         IL   Barnabas    Male               General Manager          4
4         MN     Tanney  Female                  VP Marketing          5
5         VA     Launce    Male       Automation Specialist I          6
6         CO      Adham    Male               Project Manager          7
7         TX      Dante    Male           Geological Engineer          8
8         PA         Hi  Female        Senior Cost Accountant          9
9         CA    Carmine  Female               Statistician II         10
```

<br>

___4º Uso do comando INNER JOIN do SQL para retornar apenas as linhas que têm correspondência em ambas as tabelas.___ <br>
```
query = """
SELECT
*
FROM TB_TRANSACOES
INNER JOIN TB_CLIENTES
ON TB_TRANSACOES.Id_client = TB_CLIENTES.Id_client
ORDER BY TB_TRANSACOES.Id_client
"""
result_df = run_query(query)
print(result_df)
```
```
     id_client    Category   Price   Card Type state_name First_name   Gender  \
0            1    Outdoors   16,97  mastercard         TX    Domingo     Male   
1            2     Grocery  143,39  mastercard         MI    Russell     Male   
2            3       Music   37,64  mastercard         AL     Kimble     Male   
3            4     Jewelry    8,51  mastercard         IL   Barnabas     Male   
4            5    Outdoors   61,95  mastercard         MN     Tanney   Female   
..         ...         ...     ...         ...        ...        ...      ...   
291        171       Books   35,85  mastercard         UT  Fairleigh     Male   
292        172  Automotive  130,08  mastercard         DC    Lambert  Agender   
293        173        Kids   28,38  mastercard         NY      Jacob     Male   
294        174      Sports   36,35  mastercard         OK        Pip     Male   
295        175        Kids  141,71  mastercard         OR     Murvyn   Female   

                        Job_Title  Id_client  
0    Structural Analysis Engineer          1  
1              Speech Pathologist          2  
2             Account Coordinator          3  
3                 General Manager          4  
4                    VP Marketing          5  
..                            ...        ...  
291    Desktop Support Technician        171  
292             Chemical Engineer        172  
293      Senior Financial Analyst        173  
294         Chief Design Engineer        174  
295          Safety Technician IV        175  

[296 rows x 9 columns]
```

<br>

🟪 **Limpeza de Dados com Python** <br>

___Limpeza com Pandas antes de exportar para o formato CSV.___<br>
```
result_df['Price'] = result_df['Price'].replace(',', '.', regex=True)
result_df['Price'] = result_df['Price'].astype(float)
result_df.drop('Id_client', axis=1, inplace=True)
result_df.columns = result_df.columns.str.lower()
result_df.rename(columns={'card type': 'card_type'}, inplace=True)

print(result_df)

#print(result_df.isnull().sum())                   # verificar a existencia de valores nulos no df.
#print(result_df.dtypes)                           # verificar a existencia de tipos de dados no df.
#print(result_df.applymap(type).nunique() == 1)    # Verificar a existência de um só tipo de dado em cada coluna do df.
```


```
     id_client    category   price   card_type state_name first_name   gender  \
0            1    Outdoors   16.97  mastercard         TX    Domingo     Male   
1            2     Grocery  143.39  mastercard         MI    Russell     Male   
2            3       Music   37.64  mastercard         AL     Kimble     Male   
3            4     Jewelry    8.51  mastercard         IL   Barnabas     Male   
4            5    Outdoors   61.95  mastercard         MN     Tanney   Female   
..         ...         ...     ...         ...        ...        ...      ...   
291        171       Books   35.85  mastercard         UT  Fairleigh     Male   
292        172  Automotive  130.08  mastercard         DC    Lambert  Agender   
293        173        Kids   28.38  mastercard         NY      Jacob     Male   
294        174      Sports   36.35  mastercard         OK        Pip     Male   
295        175        Kids  141.71  mastercard         OR     Murvyn   Female   

                        job_title  
0    Structural Analysis Engineer  
1              Speech Pathologist  
2             Account Coordinator  
3                 General Manager  
4                    VP Marketing  
..                            ...  
291    Desktop Support Technician  
292             Chemical Engineer  
293      Senior Financial Analyst  
294         Chief Design Engineer  
295          Safety Technician IV  

[296 rows x 8 columns]
```

<br>

🟪 **Exportação de Dados** <br>

___Exportação dos dados para o formato CSV.___<br>
```
result_df.to_csv('BASE_ECOMMERCE.csv', index=False)
```
<br>

🟪 **Análise de Dados no Power BI** <br>

___Carregamento dos dados e análise com Power BI. Segue o link abaixo:___<br>
https://app.powerbi.com/links/zy8qgNIGU3?ctid=437dbe8a-034a-4d15-ba99-3c71e2415611&pbi_source=linkShare


