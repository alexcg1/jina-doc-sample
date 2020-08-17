### Common design patterns
Jina is a really flexible AI-powered neural search framework. It is designed for building neural search applications, or neural search apps for short.

Neural search apps that use Jina tend to converge around certain logical arrangements of `[Executors](https://docs.jina.ai/api/jina.executors.html)` in their workflow design. This is because organizing predefined steps into logical groups is a common need in many search workflows. Some of these logical arrangements, or design patterns, are highlighted below.


#### CompoundIndexer (Vector + KV Indexers)

When building neural search apps, it useful to use a `[CompoundIndexer](https://docs.jina.ai/api/jina.executors.indexers.html#jina.executors.indexers.CompoundIndexer)` in the same `Pod` for both `index` and `query` flows.
The following `yaml` file shows an example of this pattern.

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

This chain of two `indexer`s will behave like a single `indexer` and will allow clients to seamlessly `query` the index with the `embedding` vector coming from any upstream `encoder` and obtain in the response message of the pod the corresponding `binary` information stored in
the key-value index. This lets the `VectorIndexer` be responsible for obtaining the most relevant documents by finding similarities
in the `embedding` space while targeting the `key-value` database to extract the meaningful data and fields from those relevant documents.

