# Serialization

`DocumentArray` provides the following methods for importing from/exporting to different formats.

| Description                       | Export Method                                                       | Import Method                                 |
|-----------------------------------|---------------------------------------------------------------------|-----------------------------------------------|
| LZ4-compressed binary string/file | `.to_bytes()` (or `bytes(...)` for more Pythonic), `.save_binary()` | `.load_binary()`                              |
| JSON string/file                  | `.to_json()`, `.save_json()`                                        | `.load_json()`, `.from_ndjson()`              |
| CSV file                          | `.save_csv()`                                                       | `.load_csv()`, `.from_lines()`, `.from_csv()` |
| `pandas.Dataframe` object         | `.to_dataframe()`                                                   | `.from_dataframe()`                           |
| Local files                       |                                                                     | `.from_files()`                               |
| `numpy.ndarray` object            |                                                                     | `.from_ndarray()`                             |
| Jina Cloud Storage (experimental) | `.push()`                                                           | `.pull()`                                     |

```{seealso}
`.from_*()` functions often utlizes generators. When using independently, can be more memory-efficient. See {mod}`~jina.types.document.generators`.   
```

### Sharing DocumentArray across machines

```{caution}
This is an experimental feature introduced in Jina `2.5.4`. The behavior of this feature might change in the future. 
```

Since Jina `2.5.4` we introduce a new IO feature: {meth}`~jina.types.arrays.mixins.io.pushpull.PushPullMixin.push` and {meth}`~jina.types.arrays.mixins.io.pushpull.PushPullMixin.pull`, 
which allows you to share a DocumentArray object across machines.

Considering you are working on a GPU machine via Google Colab/Jupyter. After preprocessing and embedding, you got everything you need in a DocumentArray. You can easily transfer it to the local laptop via:

```python
from jina import DocumentArray

da = DocumentArray(...)  # heavylifting, processing, GPU task, ...
da.push(token='myda123')
```

Then on your local laptop, simply

```python
from jina import DocumentArray

da = DocumentArray.pull(token='myda123')
```

Now you can continue the work at local, analyzing `da` or visualizing it. Your friends & colleagues who know the token `myda123` can also pull that DocumentArray. It's useful when you want to quickly share the results with your colleagues & friends.

For more information of this feature, please refer to {class}`~jina.types.arrays.mixins.io.pushpull.PushPullMixin`.

```{danger}
The lifetime of the storage is not promised at the momennt: could be a day, could be a week. Do not use it for persistence in production. Only consider this as temporary transmission or a clipboard.
```

