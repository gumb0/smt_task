# The SMT Task

Read the [Sparse Merkle Tree description].

Implement Sparse Merkle Tree data structure which supports the following operations:
- construct an empty tree
- insert a 64-bit key into the tree
- lookup the key in the tree and return the proof of existence / non-existence. The proof should be represented as `(existenceFlag, proofBits, array of hashes)`

You should use these hash functions for hashing the leaves and internal tree nodes:

```cpp
#include <functional>

// Use either hash(0) or hash(1) to represent leaf nodes
size_t hash(uint64_t v)
{
    static std::hash<uint64_t> h;
    return h(v);
}

// Each internal node is addressed by a hash of its two children
size_t hash(uint64_t h1, uint64_t h2)
{
    size_t ret = hash(h1);
    return ret ^ (hash(h2) + 0x9e3779b9 + (ret << 6) + (ret >> 2));
}
```

Your implementation can use standard map container (`std::map` or `std::unordered_map`) to represent the internal storage of tree nodes, i.e. storing `hash => node`pairs. 

Create a program that reads a file from the path given as a command-line argument, each line of a file is an unsigned 64-bit integer to store in the tree.

After populating a tree with keys from the file, the program should get a value input from standard input, interpret each line as an integer key to lookup and output to standard output the line of the following format:

```
0|1 proofBits hash1 hash2 ... hashN
```

`proofBits` and all hashes should be represented as unsigned 64-bit integers.


[Sparse Merkle Tree description]: ./Sparse%20Merkle%20Tree.md
