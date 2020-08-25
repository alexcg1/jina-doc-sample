# Common Design Patterns
Jina is an exceptionally flexible AI-powered neural search framework which is designed to enable any pattern that can be framed as a neural search problem. However, there are common patterns that occur when designing search solutions. with Jina and here is a recopilation of some of them below for your detailed reference when building a neural search with Jina.  

## CompoundIndexer (Vector + KV Indexers):

Using a `CompoundIndexer` in the same Pod for both index and query Flows, as seen in the YAML file below, facilitates the development of neural search applications. 

```
!CompoundIndexer
components:
  - !NumpyIndexer
    with:
      index_filename: vectors.gz
      metric: cosine
    metas:
      name: vecIndexer
  - !BinaryPbIndexer
    with:
      index_filename: values.gz
    metas:
      name: kvIndexer  # a customized name
metas:
  name: complete indexer
```

The above YAML file creates a Flow which:
    
- Acts as an single `indexer`
- Allows users `query` seemlessly the index with the `embedding` vector from any upstream `encoder`  
- Obtain in the Pod's response message the corresponding `binary` information stored in the key-value index. 

This lets the `VectorIndexer` be responsible for obtain the most relevant documents by finding similarities in the `embedding` space while targeting the `key-value` database to extract the meaningful data and fields from those relevant documents.

