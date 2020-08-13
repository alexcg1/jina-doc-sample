### Common designs patterns
jina is a really flexible AI powered neural search frameworks. It is designed to enable any pattern that can be framed as  
a neural search problem.
However, there are basic basic common patterns that show when developping search solutions with jina and here is a recopilation of some of them below for your detailed reference when building a neural search with JINA.
<\p>

- CompoundIndexer (Vector + KV Indexers):

To develop neural search applications, it is of useful to use a `CompoundIndexer` in same `Pod` for both `index` and `query` flows.
The following `json` file shows an examples of this pattern.

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
`

This type of constructon will acts as an single `indexer` and will allow to seamlessly `query` this index with the `embedding` vector coming
from any upstream `encoder` and obtain in the response message of the pod the corresponding `binary` information stored in
the key-value index. This lets the `VectorIndexer` be responsible for obtain the most relevant documents by finding similarities
in the `embedding` space while targeting the `key-value` database to extract the meaningful data and fields from those relevant documents.

