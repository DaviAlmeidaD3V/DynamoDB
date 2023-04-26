# DynamoDB Inserting Data
## and Making Inquiries

Repository for Bootcamp Java Developer, With the Partnership of dio and Banco Pan .

### Apps Used
  - Amazon DynamoDB
  - Amazon CLI.

# Commands for Execute Database


-  Create My Tables

```
aws dynamodb create-table \
    --table-name Movies \
    --attribute-definitions \
        AttributeName=MoviesClassification,AttributeType=S \
        AttributeName=Director,AttributeType=S \
    --key-schema \
        AttributeName=MoviesClassification,KeyType=HASH \
        AttributeName=Director,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

- Insert One Item

```
aws dynamodb put-item \
    --table-name Movies \
    --item file://Film.json \
```

- Insert Multiply Items

```
aws dynamodb batch-write-item \
    --request-items file://Films.json
```

- Create Global Index secondary based in Movie Year

```
aws dynamodb update-table \
    --table-name Movies \
    --attribute-definitions AttributeName=MovieTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"MovieTitle-index\",\"KeySchema\":[{\"AttributeName\":\"MovieTitle\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Create Global Index secondary based Classification of Movie and Title Of Movie

```
aws dynamodb update-table \
    --table-name Movies \
    --attribute-definitions\
        AttributeName=MoviesClassification,AttributeType=S \
        AttributeName=MovieTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"MoviesClassificationMovieTitle-index\",\"KeySchema\":[{\"AttributeName\":\"MoviesClassification\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"MovieTitle\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Create Global Index secondary based in Title of Movie and Year of Release

```
aws dynamodb update-table \
    --table-name Movies \
    --attribute-definitions\
        AttributeName=MovieTitle,AttributeType=S \
        AttributeName=MovieYear,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"MovieTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"MovieTitle\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"MovieYear\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Query data by Classification Of Movie

```
aws dynamodb query \
    --table-name Movies \
    --key-condition-expression "MoviesClassification = :class" \
    --expression-attribute-values  '{":class":{"S":"Imdb"}}'
```
- Query data by Classification and Title of Movie

```
aws dynamodb query \
    --table-name Movies \
    --key-condition-expression "MoviesClassification = :class and MovieTitle = :title" \
    --expression-attribute-values file://keyconditions.json
```

- Query By Year of Title of Movie

```
aws dynamodb query \
    --table-name Movies \
    --index-name MovieTitle-index \
    --key-condition-expression "MovieTitle = :title" \
    --expression-attribute-values  '{":title":{"S":"Saving Private Ryan"}}'
```

- Query By Movie Classification and Title of Movie

```
aws dynamodb query \
    --table-name Movies \
    --index-name MoviesClassificationMovieTitle-index \
    --key-condition-expression "MoviesClassification = :class and MovieTitle = :title" \
    --expression-attribute-values  '{":class":{"S":"Imdb"},":title":{"S":"The Godfather"} }'
```

- Query By Movie Title and Year 

```
aws dynamodb query \
    --table-name Movies \
    --index-name MovieTitleYear-index \
    --key-condition-expression "MovieTitle = :title and MovieYear = :year" \
    --expression-attribute-values  '{":title":{"S":"Forrest Gump"},":year":{"S":"1994"} }'
```