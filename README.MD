
# 1. Instalação de Bibliotecas:
## Instalar Pyspark e openpyxl
```bash
!pip install pyspark
!pip install openpyxl
```

# 2. Criação da Sessão Spark:
## Importe as bibliotecas necessárias e crie uma sessão Spark para usar as funcionalidades do PySpark.
```bash
from pyspark.sql import SparkSession
import pandas as pd
from pyspark.sql.functions import col
spark = SparkSession.builder.appName("Exemplo ETL").getOrCreate()
```

# 3. Extração: Carregar o Arquivo Excel com Pandas:
## Carregue o arquivo Excel utilizando a biblioteca Pandas.
```bash
df_excel = pd.read_excel('/content/data_faturamento.xlsx')
```

# 4. Conversão para CSV:
## Converta o DataFrame lido com Pandas para um arquivo CSV.

```bash
df_excel.to_csv('/content/data_faturamento.csv', index=False)
```

# 5. Carregar o CSV usando PySpark:
## Leia o arquivo CSV usando PySpark e exiba os dados carregados.
```bash
df_spark = spark.read.csv('/content/data_faturamento.csv', header=True, inferSchema=True)
df_spark.show()
df_spark.printSchema()
```

# 6. Transformação: Filtragem e Adição de Coluna:
## Aplique transformações no DataFrame PySpark, filtrando registros e adicionando uma nova coluna.
```bash
df_transformed = df_spark.filter(col("faturamento") > 1000)
df_transformed = df_transformed.withColumn("ValorComTaxa", col("faturamento") * 1.1)
df_transformed.show()
```

# 7. Salvar Dados Transformados em Novo CSV:
## Salve os dados transformados em um novo arquivo CSV.
```bash
df_transformed.write.csv('/content/data_faturamento_transformado.csv', header=True)
```

# 8. Converter PySpark DataFrame para Pandas:
## Converta o DataFrame PySpark transformado para um DataFrame Pandas para facilitar a manipulação de dados.
```bash
df_transformed_pd = df_transformed.toPandas()
```

# 9. Gerar e Salvar Comandos SQL:
## Crie comandos SQL de inserção com base nos dados transformados e salve-os em um arquivo SQL.
```bash
table_name = 'data_faturamento'
sql_inserts = []

for index, row in df_transformed_pd.iterrows():
    values = "', '".join([str(x).replace("'", "''") for x in row.values])  # Escape de apóstrofos para SQL
    sql_inserts.append(f"INSERT INTO {table_name} VALUES ('{values}');")

with open('/content/data_faturamento_transformado.sql', 'w') as file:
    file.write('\n'.join(sql_inserts))
```

# 10. Encerrar a Sessão Spark:
## Encerre a sessão Spark para liberar recursos.
```bash
spark.stop()
```
