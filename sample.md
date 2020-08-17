### Common design patterns
Jina is a really flexible AI-powered neural search framework. It is designed for building neural search applications.

Neural search applications designed around the Jina framework tend to use `executor`s in certain arrangements that are common. Some of these common arrangements, or design patterns, will be highlighted below: 


- CompoundIndexer (Vector + KV Indexers):

To develop neural search applications, it is of useful to use a `CompoundIndexer` in same `Pod` for both `index` and `query` flows.
The following `json` file shows an example of this pattern.

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

This type of constructon will acts as an single `indexer` and will allow to seamlessly `query` this index with the `embedding` vector coming
from any upstream `encoder` and obtain in the response message of the pod the corresponding `binary` information stored in
the key-value index. This lets the `VectorIndexer` be responsible for obtaining the most relevant documents by finding similarities
in the `embedding` space while targeting the `key-value` database to extract the meaningful data and fields from those relevant documents.

