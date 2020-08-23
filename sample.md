### Common designs patterns
Jina is a flexible, AI-powered, neural search framework. By design, Jina can enable any variety of pattern that can be framed as  
a neural search problem. However, certain patterns have emerged as best practices when developing search solutions with Jina. 
Below, you will find a select few for your detailed reference when building a neural search with Jina.

- CompoundIndexer `(Vector + KV Indexers)`:

To develop neural search applications, it is useful to use a `CompoundIndexer` in the same `Pod` for both `index` and `query` flows.
The following `YAML` file shows an example of this pattern:

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

The above type of constructon will act as a single `indexer`. This will allow Jina to seamlessly `query` this index with the `embedding` vector coming
from any upstream `encoder`, and obtain in the response message of the pod the corresponding `binary` information stored in
the key-value index. 

This lets the `VectorIndexer` be responsible for obtaining the most relevant documents by finding similarities
in the `embedding` space, while targeting the `key-value` database to extract the meaningful data and fields from those relevant documents.

