
## Data Modeling and File Formats

每个大数据课程都会提到的两个概念。

1. Data Modeling 是处理数据时，包括在程序中使用中用的概念： 以何种方式组织数据，有效的操作数据。
2. File Formats 是存储数据时，用到的概念： 如何有效的存储数据、传输数据，确保数据的完整性和正确性？

### Relational data model

关系性数据模型。 Table、Row、Column。

### Graph data model

点（Vertices，entities）、边（Edges，relations）。

### Unstructured Data

Technically, all data is structured at least as a byte sequence. Usually, means "not structured enough for a task"

就是对一个任务来说，结构化不够的意思。

### File Format(Storage Format)

Primary Function: to transform between raw bytes and programmatical data structures(serialization & deserialization)

最关键的功能是在存储格式（raw bytes） 与 程序数据格式之间转换。

一般有几个指标评估一个 File Format

|File Format| Space Efficiency | (Encoding /Decoding) Speed | Data Types | Splittable| Extensibility
|--|--|--|--|--|--|
| CSV & TSV| BAD 因为都是字符串，数字也用字符串| GOOD| Only Strings| Splittable W/O Header| BAD
| JSON | BAD(worse than CSV) | GOOD ENOUGH| Strings, numbers, booleans, maps, lists| Splittable if 1 document per line | YES


|Binary Format| Space Efficiency | (Encoding /Decoding) Speed | Data Types | Splittable| Extensibility
|--|--|--|--|--|--|
| SequenceFile| Moderate to GOOD | GOOD | ANY W/SER./DESER. CODE| Splittable | NO
| Avro| Moderate to GOOD| GOOD With CodeGen | JSON-LIKE | Splittable | YES
| RCFile|GOOD| Moderate to GOOD, Less I/O | Byte Strings | Splittable | NO
| Parquet|

注： 
1. Sequence File是  First binary format implemented in Hadoop。 存储 键值对序列。每个record包含一个键值信息。或者每个block包含若干records。records或者blocks序列存储。具体见 <https://wiki.apache.org/hadoop/SequenceFile>


### Compression

- Block-level
- File-level


|Codecs | Compression Speed| Decompression| Ratio|
|--|--|--|--|
|Bzip2   | 12~14MiB/s  |38~42MiB/s   | 4.02~4.80  |
|Gzip| 16~90MiB/s| 250~320MiB/s| 2.77~3.43|
|LZO| 77~150MiB/s | 290~314MiB/s| 2.10~2.48
|Snappy| 200MiB/s| 475MiB/s| 2.05|

[MiB = mebibyte](https://en.wikipedia.org/wiki/Mebibyte)

什么时候用 Compression？

- CPU-bound： 不要用压缩。用压缩不仅不会提高效率，反而会增加CPU计算负担
- I/O-bound： 可以从压缩中收益。
