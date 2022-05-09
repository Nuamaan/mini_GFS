# Minimal GFS
A minimal version of GFS for ECS 251 - Operating Systems.

## Master Node

- **GetChunkUrls**:
  - Returns the chunk handle and the location of the primary and secondary replicas.
  - Input:
    - chunk_index: The index of the chunk.
  - Output:
    - chunk_handle: Globally unique 64 bit chunk handle assigned by the master at the time of chunk creation.
    - primary_url: The location of the primary replica.
    - secondary_a: The location of the first secondary replica.
    - secondary_b: The location of the second secondary replica.

## Json rpc Stubs

### Replica Data
  
- **write_chunk**:
  - Caches the chunk in an LRU and returns a unique id that identifies the uncommited chunk.
  - Input:
    - data: bytes of an entire chunk encoded as a base64 string.
  - Output:
    - chunk_id: The lru chunk id. An id that uniquely identifies the uploaded chunk.
    - Or Error.

- **read_chunk**:
  - Input:
    - chunk_handle: Globally unique 64 bit chunk handle assigned by the master at the time of chunk creation.
    - offset: offset from the start of the chunk.
    - len: the number of bytes to read.
  - Output:
    - data: bytes of an selected part of the chunk encoded as a base64 string.
    - Or Error.


### Primary Replica Control

- **commitAll**:
  - Commits the chunk and forwards the commit message to all secondary replicas.
  - Input:
    - primary_url: The location of the primary replica.
    - primary_chunk_id: The lru chunk id for the primary replica node.
    - secondary_a: The location of the first secondary replica.
    - secondary_a_chunk_id: The lru chunk id for the first secondary replica node.
    - secondary_b: The location of the second secondary replica.
    - secondary_b_chunk_id: The lru chunk id for the second secondary replica node.
    - chunk_handle: Globally unique 64 bit chunk handle assigned by the master at the time of chunk creation.
  - Output:
    - Null or error

- **abortAll**:
  - Removes the chunk from the lru, and forwards the abort message to all secondary replicas.
  - Input:
    - primary_url: The location of the primary replica.
    - primary_chunk_id: The lru chunk id for the primary replica node.
    - secondary_a: The location of the first secondary replica.
    - secondary_a_chunk_id: The lru chunk id for the first secondary replica node.
    - secondary_b: The location of the second secondary replica.
    - secondary_b_chunk_id: The lru chunk id for the second secondary replica node.
  - Output:
    - Null or error

### Secondary Replica Control
  
- **commit**:
  - Commits the chunk to persistent storage.
  - Input
    - chunk_id: The lru chunk id.
    - chunk_handle: Globally unique 64 bit chunk handle assigned by the master at the time of chunk creation.
    - commit_serial_number: a unique serial number of the commit. Commits should be done in order of their 'commit_serial_number'.
  - Output:
    - Null or error

- **abort**:
  - Removes the chunk from the lru and deletes any data related to the uncommited chunk.
  - Input:
    - chunk_id: The lru chunk id.
  - Output:
    - Null or error
