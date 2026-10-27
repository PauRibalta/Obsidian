## 1. Objective of the Memory Hierarchy

The goal is to improve computer performance by providing the illusion of a memory that is both **large and fast**, supplying instructions and data to the processor at the speed it can process them.

### The processor–memory performance gap

Processor performance improves faster than main memory performance. The memory hierarchy reduces the impact of this gap by placing frequently or recently used data in faster memory.

### Principle of locality

- **Temporal locality:** if an instruction or data item is accessed, it is likely to be accessed again soon.
    - Example: instructions and data reused in loops.
- **Spatial locality:** if a memory location is accessed, nearby locations are likely to be accessed soon.
    - Examples: sequential instruction execution and access to array or matrix elements.

## 2. Levels of the Memory Hierarchy

|Level|Technology|Characteristics|
|---|---|---|
|CPU registers|Registers|Fastest, smallest|
|Cache (L1/L2)|SRAM|Fast, small, volatile|
|Main memory|DRAM|Larger, slower, volatile|
|Persistent memory|Flash|Non-volatile|
|Disk|Disk storage|Largest, slowest|

Moving **up** the hierarchy means lower access time and higher cost per unit of storage. Moving **down** means greater capacity and slower access.

Data is transferred between **adjacent levels** of the hierarchy.

### Cache and main memory

- Cache is smaller, faster and more expensive than main memory.
- Cache stores copies of selected blocks from main memory.
- The minimum unit that can be present or absent in the cache is a **block**, also called a **cache line**.
- A block contains one or more words and exploits spatial locality.

The number of blocks in a cache is:

`Number of blocks = Cache size / Block size`

Example: a 64 KiB cache with 16-byte blocks contains `64 KiB / 16 B = 4096 = 4K blocks`.

## 3. Cache Hits and Misses

- **Hit:** the requested data is found in the cache.
- **Miss:** the requested data is not found in the cache and must be retrieved from a lower level. The first access is always a miss.

On a read miss, the processor typically stalls while the system:

1. Requests the block containing the required data from main memory.
2. Copies the block into the cache.
3. Repeats the cache access, which should now hit.

### Cache entry structure

Each cache entry contains:

- **Valid bit (V):** indicates whether the entry contains valid data. At startup, entries are invalid; the initial misses are called _cold-start misses_.
- **Tag:** identifies which memory block is stored in the entry.
- **Data block:** the cached copy of the memory block.

## 4. Cache Block Placement

The placement policy determines where a main-memory block can be stored in the cache.

There are three main organizations:

1. Direct-mapped cache.
2. Fully associative cache.
3. N-way set-associative cache.

### 4.1 Direct-mapped cache

Each memory block maps to exactly one cache line.

`Cache block index = Memory block index mod Number of cache blocks`

Example: for a cache with 8 blocks, memory block 12 maps to:

`12 mod 8 = 4`

**Characteristics**

- Only one possible location for each memory block.
- Only one tag needs to be checked after selecting the indexed line.
- Simple implementation, but blocks that map to the same line can repeatedly evict one another.

### 4.2 Fully associative cache

A memory block can be stored in **any cache line**.

**Characteristics**

- No index field is needed in the address.
- The tags of all cache entries must be checked to find a matching block.
- Offers flexible placement, but requires more hardware for parallel tag comparisons.
- Requires a replacement policy when the cache is full and a new block must be loaded.

### 4.3 N-way set-associative cache

The cache is divided into sets, each containing `n` lines (ways).

`Number of cache blocks = Cache size / Block size`

`Number of sets = Cache size / (Block size × n)`

A memory block maps to one specific set:

`Cache set index = Memory block index mod Number of sets`

It can then occupy any of the `n` lines within that set.

**Characteristics**

- The index selects a set.
- The tags of all `n` ways in that set are compared in parallel.
- Combines restricted placement with some flexibility.

Example: a 2-way set-associative cache with 8 total lines has 4 sets. Memory block 12 maps to set `12 mod 4 = 0` and can occupy either line in that set.

### Comparison of placement policies

|Property|Direct mapped|Fully associative|N-way set associative|
|---|---|---|---|
|Possible locations|Exactly one line|Any line|Any line in one set|
|Index field|Yes|No|Yes, selects a set|
|Tag comparisons|1|All cache lines|`n` ways in selected set|
|Replacement choice|Fixed line|Any line|Any line in selected set|

## 5. Memory Address Structure

A memory address is divided into fields used to locate a block and then select the requested data within it.

For the address formats in the slides:

- **Tag:** identifies the memory block and is compared with the stored tag.
- **Index:** selects a cache line or set.
- **Word offset:** selects a word within the block.
- **Byte offset:** selects a byte within a word.

![[Pasted image 20261020115838.png|556]]

Let:

- `N` = number of bits in the memory address.
- `M` = number of index bits.
- `K` = number of word-offset bits.
- `B` = number of byte-offset bits.

Then:

`Tag bits = N − (M + K + B)`

If memory is not byte-addressable, `B = 0`. If a block contains only one word, `K = 0`.

### Calculating the offset

For a byte-addressable memory:

`Offset bits = log₂(Block size in bytes)`

When the block contains `2^K` words, each of `2^B` bytes:

- Word offset: `K = log₂(words per block)`
- Byte offset: `B = log₂(bytes per word)`

Total offset bits:

`K + B = log₂(Block size in bytes)`

**Example:** a 128-bit block contains 16 bytes. Its total offset is `log₂(16) = 4` bits. With 32-bit words, the word offset uses 2 bits and the byte offset uses 2 bits.

### Example 1: Direct-mapped cache

Given:

- 32-bit memory addresses.
- Cache size = 4 KiB.
- Block size = 32 bits = 4 bytes.
- One word per block.

Calculations:

- Number of cache blocks: `4 KiB / 4 B = 1024 = 2¹⁰`.
- Index bits: `M = 10`.
- Byte offset bits: `B = 2`.
- Word offset bits: `K = 0`.
- Tag bits: `32 − 10 − 2 = 20`.

Address format:

`Tag (20 bits) | Index (10 bits) | Byte offset (2 bits)`

### Example 2: Direct-mapped cache

Given:

- 32-bit memory addresses.
- Cache size = 64 KiB.
- Block size = 128 bits = 16 bytes.
- Four 32-bit words per block.

Calculations:

- Number of blocks: `64 KiB / 16 B = 4096 = 2¹²`.
- Index bits: `M = 12`.
- Word offset bits: `K = 2`.
- Byte offset bits: `B = 2`.
- Tag bits: `32 − 12 − 2 − 2 = 16`.

Address format:

`Tag (16 bits) | Index (12 bits) | Word offset (2 bits) | Byte offset (2 bits)`

### Example 3: Fully associative cache

Given:

- 32-bit memory addresses.
- Cache size = 256 bytes.
- Block size = 128 bits = 16 bytes.

Calculations:

- Number of blocks: `256 B / 16 B = 16`.
- No index field.
- Total offset = `log₂(16) = 4` bits.
- Tag bits: `32 − 4 = 28`.

Address format:

`Tag (28 bits) | Word offset (2 bits) | Byte offset (2 bits)`

### Example 4: Four-way set-associative cache

Given:

- 32-bit memory addresses.
- Cache size = 4 KiB.
- Block size = 32 bits = 4 bytes.
- Associativity = 4 ways.

Calculations:

- Total cache blocks: `4 KiB / 4 B = 1024`.
- Number of sets: `1024 / 4 = 256 = 2⁸`.
- Index bits: `M = 8`.
- Byte offset bits: `B = 2`.
- Tag bits: `32 − 8 − 2 = 22`.

Address format:

`Tag (22 bits) | Set index (8 bits) | Byte offset (2 bits)`

### General address calculation procedure

1. Convert the block size to bytes.
2. Calculate total cache blocks: `Cache size / Block size`.
3. Determine the number of sets:
    - Direct mapped: number of sets equals number of lines.
    - Fully associative: no index or set field.
    - N-way set associative: total lines divided by `n`.
4. Calculate index bits: `log₂(number of sets)`.
5. Calculate offset bits: `log₂(block size in bytes)`.
6. Calculate tag bits: `N − index bits − offset bits`.

For the slide examples, sizes and numbers of sets are powers of two, so the bit counts are integers.

## 6. Cache Lookup and Associativity

The lookup procedure depends on the cache organization:

- **Direct mapped:** use the index to select one line, then check its valid bit and tag.
- **Fully associative:** compare the requested tag against all entries and check validity.
- **Set associative:** use the index to select a set, then compare the tag against all ways in that set and check validity.

### Effect of increasing associativity

For a fixed cache size, increasing associativity generally reduces the miss rate.

**Advantages**

- Fewer misses due to placement conflicts.

**Disadvantages**

- More expensive hardware.
- Increased hit time.
- Fewer index bits and more tag bits as the number of sets decreases.

Very high associativity (above 8 ways in the slides) is not considered worthwhile because of implementation cost. The choice depends on the trade-off between implementation cost, hit time and miss rate.

## 7. Cache Block Replacement

When a miss requires loading a new block:

- **Direct mapped:** there is only one possible line to replace.
- **Fully associative:** any cache line may be replaced.
- **Set associative:** any line in the selected set may be replaced.

Common replacement policies for associative caches:

- **Random:** choose a line randomly.
- **LRU (Least Recently Used):** replace the line that has not been used for the longest time. This exploits temporal locality but is relatively complex to implement in hardware.
- **FIFO (First In, First Out):** replace the line that was loaded earliest, regardless of subsequent accesses.

The slides identify LRU as providing the best behavior among these policies, while noting its hardware complexity.

## 8. Write Policies

Write policies determine how a write updates the cache and main memory.

### 8.1 Write-through

Every write updates both:

- The cache block.
- The corresponding main-memory block.

**Advantages**

- Simple to implement.
- Main memory stays up to date after completed writes.

A **write buffer** is used to avoid making the processor wait for slower main-memory writes.

### 8.2 Write-back

A write updates only the cache block. Main memory is updated when that modified block is replaced following a subsequent miss.

- The modified cache block becomes **dirty**.
- Main memory can contain an older value than the cache.
- When the dirty block is replaced, it must be written to main memory to restore consistency.

**Advantages**

- Writes can be accepted at cache speed.
- Multiple writes to the same block can result in a single write to main memory.

### Write buffer

A write buffer is typically a FIFO with 4 or 8 entries.

- The processor writes to the cache and write buffer in parallel.
- The memory controller writes buffered data to main memory later.
- The main problem is **write-buffer saturation**.
- Write buffers are typically paired with write-through caches, but can also be used with write-back caches.

### Read/write hit and miss behavior

|Event|Behavior|
|---|---|
|Read hit|Read the data from the cache.|
|Read miss|Stall, fetch the block from memory, copy it into cache, then repeat the read.|
|Write hit — write-through|Write to cache and main memory.|
|Write hit — write-back|Write to cache; mark the block dirty.|
|Write miss|Stall and fetch the block into cache, then perform the write; with write-through, also write to memory.|

The slides describe the write-miss procedure as fetching the block and then rewriting the cache block. They do not detail alternative write-miss policies.

## 9. Cache Performance

### Hit rate and miss rate

- **Hit rate:** fraction of memory accesses that find the requested data in the upper level.
    `Hit Rate = Number of hits / Total memory accesses`
- **Miss rate:** fraction of memory accesses that fail to find the requested data in the upper level.
    `Miss Rate = Number of misses / Total memory accesses`

They satisfy:

`Hit Rate + Miss Rate = 1`

### Hit time, miss penalty and miss time

- **Hit time:** time to access the upper-level cache, including determining whether the access is a hit or miss.
- **Miss penalty:** extra time required to retrieve the block from a lower level and replace a block in the upper level.
- **Miss time:** total time for an access that misses.

`Miss Time = Hit Time + Miss Penalty`

Typically:

`Hit Time << Miss Penalty`

### Average Memory Access Time (AMAT)

For a two-level hierarchy consisting of cache and main memory:

`AMAT = Hit Rate × Hit Time + Miss Rate × Miss Time`

Since:

`Miss Time = Hit Time + Miss Penalty`

and:

`Hit Rate + Miss Rate = 1`

the formula simplifies to:

`AMAT = Hit Time + Miss Rate × Miss Penalty`

**Example from the slides**

Given:

- Hit rate = 95%.
- Hit time = 2 ns.
- Miss penalty = 25 clock cycles.
- Clock cycle = 2 ns.

Calculations:

- Miss rate = `1 − 0.95 = 0.05`.
- Miss penalty = `25 × 2 ns = 50 ns`.
- AMAT = `2 ns + 0.05 × 50 ns = 4.5 ns`.

## 10. Unified vs Separate Instruction and Data Caches

A unified L1 cache stores both instructions and data. A Harvard-style organization uses separate L1 caches:

- **I-cache:** instruction cache.
- **D-cache:** data cache.

The slides give two reasons for separate caches:

- Better exploitation of locality.
- Avoiding structural conflicts in the pipeline.

For separate instruction and data caches:

`AMAT = %Instructions × (Hit Time + I$ Miss Rate × Miss Penalty) + %Data × (Hit Time + D$ Miss Rate × Miss Penalty)`

The slides indicate that typically:

`I$ Miss Rate << D$ Miss Rate`

## 11. Multi-Level Cache Hierarchy

A two-level hierarchy may not provide sufficient performance, so modern systems use multiple cache levels, such as L1 and L2, followed by main memory.

For a hierarchy with L1 and L2:

`AMAT = Hit Time_L1 + Miss Rate_L1 × Miss Penalty_L1`

The L1 miss penalty includes accessing L2:

`Miss Penalty_L1 = Hit Time_L2 + Miss Rate_L2 × Miss Penalty_L2`

Therefore:

`AMAT = Hit Time_L1 + Miss Rate_L1 × (Hit Time_L2 + Miss Rate_L2 × Miss Penalty_L2)`

The same reasoning can be extended to additional levels.

## Exam Essentials

- **Locality:** temporal = reuse of the same item; spatial = access to nearby items.
    
- **Hit vs miss:** a hit finds the data in cache; a miss requires access to a lower level.
    
- **Placement:** direct mapped = one line; fully associative = any line; set associative = any way in one set.
    
- **Address fields:** `Tag | Index | Offset`; fully associative caches have no index field.
    
- **Core formulas:**
    
    - `Number of blocks = Cache size / Block size`
        
    - `Number of sets = Cache size / (Block size × associativity)`
        
    - `Offset bits = log₂(Block size in bytes)`
        
    - `Tag bits = Address bits − Index bits − Offset bits`
        
    - `Miss Rate = 1 − Hit Rate`
        
    - `Miss Time = Hit Time + Miss Penalty`
        
    - `AMAT = Hit Time + Miss Rate × Miss Penalty`
        
- **Replacement:** LRU uses recency; FIFO uses load order; random chooses randomly.
    
- **Write-through:** update cache and memory. **Write-back:** update cache and write dirty blocks to memory on replacement.
    
- **Associativity trade-off:** higher associativity generally lowers miss rate but increases cost and hit time.
    
- **Multiple levels:** an L1 miss penalty includes the access time and possible miss penalty of L2.