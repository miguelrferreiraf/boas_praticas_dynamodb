# Projeto Boas práticas com DynamoDB

Projeto realizado no Bootcamp Geração Tech Unimed-BH - Ciência de Dados pela Digital Innovation One - Instrutor 
Cassiano Peres

## Descrição
Features Relacionais (SQL) e Não Relacionais (NoSQL) usando o mesmo banco de dados? Isso é possível? Com o DynamoDB sim! Entenda um pouco das possibilidades desse banco de dados totalmente gerenciado da AWS. Para isso, nosso super expert apresenta uma série de boas práticas para o DynamoDB.

### Serviços utilizados
- Amazon DynamoDB
- Amazon CLI para execução em linha de comando

### Conteúdos
- Introdução e explicação do Desafio
- DB Relacional x DB Não Relacional
- Introdução ao DynamoDB
- Componentes e Boas Práticas com DynamoDB
- Pensando fora da caixa no DynamoDB
- Mãos à obra: Observando a Arquitetura da prática
- Configurando o AWS e Criando uma Tabela
- Inserindo Itens na Tabela
- Criando Index
- Finalizando o Projeto
- Entendendo o Desafio

### Etapas práticas
- #### Criar uma tabela

```sql
aws dynamodb create-table 
    --table-name Music 
    --attribute-definitions 
        AttributeName=Artist,AttributeType=S 
        AttributeName=SongTitle,AttributeType=S 
    --key-schema 
        AttributeName=Artist,KeyType=HASH 
        AttributeName=SongTitle,KeyType=RANGE 
    --provisioned-throughput 
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

- #### Inserir um item
```sql
aws dynamodb put-item 
    --table-name Music 
    --item file://itemmusic.json
```

- #### Inserir múltiplos itens
```sql
aws dynamodb batch-write-item 
    --request-items file://batchmusic.json
```

- #### Pesquisar item por artista
```sql
aws dynamodb query 
    --table-name Music 
    --key-condition-expression "Artist = :artist" 
    --expression-attribute-values  "{":artist":{"S":"Iron Maiden"}}"
```

- #### Pesquisar item por artista e título da música
```sql
aws dynamodb query 
    --table-name Music 
    --key-condition-expression "Artist = :artist and SongTitle = :title" 
    --expression-attribute-values file://keyconditions.json
```

- #### Criar um index global secundário baeado no título do álbum
```sql
aws dynamodb update-table 
    --table-name Music 
    --attribute-definitions AttributeName=AlbumTitle,AttributeType=S 
    --global-secondary-index-updates 
        "[{\"Create\":{\"IndexName\": \"AlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- #### Pesquisa pelo index secundário baseado no título do álbum
```sql
aws dynamodb query 
    --table-name Music 
    --index-name AlbumTitle-index 
    --key-condition-expression "AlbumTitle = :name" 
    --expression-attribute-values  "{":name":{"S":"Fear of the Dark"}}"
```

- #### Criar um index global secundário baseado no nome do artista e no título do álbum
```sql
aws dynamodb update-table 
    --table-name Music 
    --attribute-definitions
        AttributeName=Artist,AttributeType=S 
        AttributeName=AlbumTitle,AttributeType=S 
    --global-secondary-index-updates 
        "[{\"Create\":{\"IndexName\": \"ArtistAlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"Artist\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- #### Pesquisa pelo index secundário baseado no título do álbum
```sql
aws dynamodb query 
    --table-name Music 
    --index-name AlbumTitle-index 
    --key-condition-expression "AlbumTitle = :name" 
    --expression-attribute-values  '{":name":{"S":"Fear of the Dark"}}'
```

- #### Criar um index global secundário baseado no título da música e no ano
```sql
aws dynamodb update-table 
    --table-name Music 
    --attribute-definitions\
        AttributeName=SongTitle,AttributeType=S 
        AttributeName=SongYear,AttributeType=S 
    --global-secondary-index-updates 
        "[{\"Create\":{\"IndexName\": \"SongTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"SongTitle\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"SongYear\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- #### Pesquisa pelo index secundário baseado no título da música e no ano
```sql
aws dynamodb query
    --table-name Music 
    --index-name SongTitleYear-index 
    --key-condition-expression "SongTitle = :v_song and SongYear = :v_year" 
    --expression-attribute-values  '{":v_song":{"S":"Wasting Love"},":v_year":{"S":"1992"} }'
```

#### Links úteis

 - [Repositório no GitHub (implementação de referência)](https://github.com/cassianobrexbit/dio-live-dynamodb)
 - [Sobre o DynamoDB](https://aws.amazon.com/pt/dynamodb/?trk=3e4c2258-4f21-4854-9de7-2f6da2ef0989&sc_channel=ps&s_kwcid=AL!4422!3!589951433444!p!!g!!dynamodb&ef_id=Cj0KCQjwqc6aBhC4ARIsAN06NmPuv0qGafZCOkHXKXx0SWAXdq5B80l5xWobYGB-4a8z9ypTlGOpVD8aAurVEALw_wcB:G:s&s_kwcid=AL!4422!3!589951433444!p!!g!!dynamodb)
 - [AWS LCI](https://aws.amazon.com/pt/cli/)

