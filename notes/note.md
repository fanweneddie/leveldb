# level_db Note

## Install

1. Clone the repo(note that we should add the submodule)

   ```
   $ git clone --recurse-submodules https://github.com/fanweneddie/leveldb.git
   ```

2. compile `leveldb`

   ```
   $ cd leveldb
   $ mkdir -p build && cd build
   $ cmake -DCMAKE_BUILD_TYPE=Release .. && cmake --build .

## test on db_bench

In directory `build`, 

we run

```
./db_bench
```

and we can get the result

```
LevelDB:    version 1.23
Date:       Fri Sep 17 10:00:23 2021
CPU:        8 * Intel(R) Core(TM) i5-8250U CPU @ 1.60GHz
CPUCache:   6144 KB
Keys:       16 bytes each
Values:     100 bytes each (50 bytes after compression)
Entries:    1000000
RawSize:    110.6 MB (estimated)
FileSize:   62.9 MB (estimated)
------------------------------------------------
fillseq      :       2.304 micros/op;   48.0 MB/s     
fillsync     :    3240.275 micros/op;    0.0 MB/s (1000 ops)
fillrandom   :       3.156 micros/op;   35.1 MB/s     
overwrite    :       4.698 micros/op;   23.5 MB/s     
readrandom   :       5.209 micros/op; (864322 of 1000000 found)
readrandom   :       4.027 micros/op; (864083 of 1000000 found)
readseq      :       0.165 micros/op;  672.3 MB/s    
readreverse  :       0.285 micros/op;  388.6 MB/s    
compact      :  856826.000 micros/op;
readrandom   :       2.912 micros/op; (864105 of 1000000 found)
readseq      :       0.154 micros/op;  720.2 MB/s    
readreverse  :       0.257 micros/op;  430.0 MB/s    
fill100K     :     850.684 micros/op;  112.1 MB/s (1000 ops)
crc32c       :       1.484 micros/op; 2632.5 MB/s (4K per op)
snappycomp   :       5.236 micros/op;  746.0 MB/s (output: 55.1%)
snappyuncomp :       0.592 micros/op; 6601.8 MB/s   
```

This is the write performance:

```
fillseq      :       2.304 micros/op;   48.0 MB/s     
fillsync     :    3240.275 micros/op;    0.0 MB/s (1000 ops)
fillrandom   :       3.156 micros/op;   35.1 MB/s   
overwrite    :       4.698 micros/op;   23.5 MB/s  
```

This is the read performance before compaction:

```
readrandom   :       5.209 micros/op; (864322 of 1000000 found)
readrandom   :       4.027 micros/op; (864083 of 1000000 found)
readseq      :       0.165 micros/op;  672.3 MB/s    
readreverse  :       0.285 micros/op;  388.6 MB/s   
```

This is the read performance after compaction:

```
compact      :  856826.000 micros/op;
readrandom   :       2.912 micros/op; (864105 of 1000000 found)
readseq      :       0.154 micros/op;  720.2 MB/s    
readreverse  :       0.257 micros/op;  430.0 MB/s    
```

The read performance improves after compaction.

## Main

We can write a test file like this(which contains some basic operations of `leveldb`)

```c++
#include <cassert>
#include "leveldb/db.h"
#include <iostream>

int main() {
    leveldb::DB* db;
    leveldb::Options options;
    options.create_if_missing = true;
    options.error_if_exists = true;
    // open a database
    leveldb::Status status = leveldb::DB::Open(options, "testdb", &db);
    if (!status.ok()) std::cerr << status.ToString() << std::endl;
    // delete a database
    delete db;
}
```

