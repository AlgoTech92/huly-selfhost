# Huly Migration

## v0.6.377

### Fulltext Service

Fulltext search functionality has been extracted into a separate `fulltext` service. This service is now required to be running in order to use the fulltext search functionality.

Configuration:

```yaml
  fulltext:
    image: hardcoreeng/fulltext:${HULY_VERSION}
    ports:
      - 4700:4700
    environment:
      - SERVER_SECRET=${HULY_SECRET}
      - DB_URL=mongodb://mongodb:27017
      - FULLTEXT_DB_URL=http://elastic:9200
      - ELASTIC_INDEX_NAME=huly_storage_index
      - STORAGE_CONFIG=minio|minio?accessKey=minioadmin&secretKey=minioadmin
      - REKONI_URL=http://rekoni:4004
      - ACCOUNTS_URL=http://account:3000
      - STATS_URL=http://stats:4900
    restart: unless-stopped
```

Update the `transactor` service to use the new `fulltext` service:

```yaml
  transactor:
    ...
    environment:
      ...
      - FULLTEXT_URL=http://fulltext:4700
      # Remove the following lines
      # - ELASTIC_URL=http://elastic:9200
      # - ELASTIC_INDEX_NAME=huly_storage_index
      # - REKONI_URL=http://rekoni:4004
```

### Statistics Service

New statistics service has been added. The serivce is responsible for collecting and storing statistics about the usage of the application.

Configuration:

```yaml
  stats:
    image: hardcoreeng/stats:${HULY_VERSION}
    ports:
      - 4900:4900
    environment:
      - PORT=4900
      - SERVER_SECRET=${HULY_SECRET}
    restart: unless-stopped
```

Other Huly services have been updated to use the new statistics service:

```yaml
  ...
    environment:
      - STATS_URL=http://stats:4900
      ...
```