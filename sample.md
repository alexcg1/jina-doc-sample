# Common Design Pattern

Jina is a flexible neural search framework powered by AI, which is designed to enable any pattern that can be framed as a neural search problem. However, there are some basic common patterns that show up while developing the search solutions with Jina. Here is a recompilation of some of them below for your detailed reference when building a neural search solution with Jina.

`- CompoundIndexer (Vector + KV Indexers):`

To develop neural search applications, it is helpful to use a `CompoundIndexer` in the same `Pod` for both the `index` and `query` flows. The following `json` file shows an example of this pattern:

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

This type of construction will act as a single `indexer` and will allow to seamlessly `query` this index with the `embedding` vector coming from any upstream `encoder` and obtain, in the response message of the pod, the corresponding `binary` information stored in the key-value index. This allows the `VectorIndexer` be responsible to obtain the most relevant documents by finding similarities in the `embedding` space, while targeting the `key-value` database to extract the meaningful data and fields from those relevant documents.
