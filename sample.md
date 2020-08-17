### Common design patterns
Jina is a really flexible AI-powered neural search framework. It is designed for building neural search applications, or neural search apps for short.

Neural search apps that use Jina tend to converge around certain logical arrangements of [`Executors`](https://docs.jina.ai/api/jina.executors.html) in their workflow design. This is because organizing predefined steps into logical groups is a common need in many search workflows. Some of these logical arrangements, or design patterns, are highlighted below.


#### CompoundIndexer (Vector + KV Indexers)

When building neural search apps, it useful to use a [`CompoundIndexer`](https://docs.jina.ai/api/jina.executors.indexers.html#jina.executors.indexers.CompoundIndexer) in the same `Pod` for both `index` and `query` flows.
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

There are two `indexer`s specified above: a [`NumpyIndexer`](https://docs.jina.ai/api/jina.executors.indexers.vector.numpy.html#jina.executors.indexers.vector.numpy.NumpyIndexer) and a [`BinaryPbIndexer`](https://docs.jina.ai/api/jina.executors.indexers.keyvalue.proto.html#jina.executors.indexers.keyvalue.proto.BinaryPbIndexer). Although they are logically grouped under a `CompoundIndexer`, they will behave like a single `indexer`. 

Because all instances of `NumpyIndexer` are derived from a `VectorIndexer`, the `NumpyIndexer` named `vecIndexer` in our example will allow clients to to seamlessly `query` the index with the `embedding` vector coming from any upstream `encoder`, while the `indexer` named `kvIndexer` will allow clients to obtain the corresponding `binary` information stored in the key-value index in the response message from the Pod. In other words, `CompoundIndexer` will be responsible for obtaining the most relevant documents by finding similarities in the `embedding` space while targeting the key-value database to extract meaningful data and fields from relevant documents.

