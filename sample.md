### Common designs patterns
Jina is a really flexible AI powered neural search framework. It is designed to enable patterns that can be framed as a neural search problem.
However, there are basic common patterns that show when developing search solutions with Jina, and here are compilations of some of them, for your detailed reference when building a neural search with Jina.
<\p>

- CompoundIndexer (Vector + KV Indexers):

To develop neural search applications, it is useful to use a `CompoundIndexer` in the same `Pod` for both the `index` and the `query` flows.
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


This type of constructon will act as a single `indexer` which allows the seamless `query` of the index with the `embedding` vector coming from any upstream `encoder`. This ensures that the corresponding binary information, stored in the key-value index is obtained in the response message of the pod. This also allows the `VectorIndexer` to be responsible for obtaining the most relevant documents, by finding similarities in the `embedding` space while targeting the `key-value` database. This is done in order to extract meaningful data and fields from those relevant documents.

