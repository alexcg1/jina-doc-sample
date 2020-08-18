# Common designs patterns

Jina is a flexible AI-powered neural search framework. It is designed to enable any pattern that can be framed as a neural search problem. However, there are basic common patterns that appear when developing search solutions with Jina. Below is an example of a compilation that can serve as your detailed reference when building a neural search with Jina.

## CompoundIndexer (Vector + KV Indexers)

To develop neural search applications, it is useful to use a `CompoundIndexer` in the same `Pod` for both `index` and `query` flows.
The following `JSON` file shows an example of this pattern.

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

This type of construction will act as a single `indexer`. It will allow you to seamlessly `query` this index with the `embedding` vector coming from any upstream `encoder`. In the response message of the pod, it obtains the corresponding `binary` information stored in the key-value index. This lets the `VectorIndexer` be responsible for obtaining the most relevant documents. It does this by finding similarities in the `embedding` space while targeting the `key-value` database to extract the meaningful data and fields from those relevant documents.
