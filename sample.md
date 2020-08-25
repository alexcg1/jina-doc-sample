# Common Design Patterns
Jina is an exceptionally flexible AI-powered neural search framework designed to enable any pattern which can be framed as a neural search problem. However, common patterns occur when designing search solutions with Jina: 

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

The YAML file above creates a Flow which:
    
- Functions as a single indexer
- Empowers users to smoothly query the index with the embedding vector from any upstream encoder  
- Provides the binary information in the key-value index in the Pod's response message  

The `VectorIndexer`:
- Collects the most relevant Documents by identifying similarities in the embedding space 
- Targets the key-value database to extract meaningful data from the Documents