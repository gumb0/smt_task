# The SMT Task

Read the [Sparse Merkle Tree description].

Implement Sparse Merkle Tree data structure which can be used to store a set of 64-bit keys.

Your implementation should support the following operations:

- construct an empty tree
- insert a 64-bit key into the tree
- look up the key in the tree and return the proof of existence / non-existence. The proof should be represented as `(existenceFlag, proofBits, array of hashes)`

As we don't store any special values in the tree associated with each key (we want to represent a "set" of values), it is natural to keep at the leaves of the tree the values `0` and `1`. In other words the default hash stored at the leaf is `H(0)`, and after the key is inserted the corresponding hash at the leaf is `H(1)`.

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

Your implementation can use standard map container (`std::map` or `std::unordered_map`) to represent the internal storage of tree nodes, i.e. storing `hash => node` pairs. 

Create a program that reads a file from the path given as a command-line argument, each line of a file is an unsigned 64-bit integer to store in the tree.

After populating the tree with keys from the file, the program should read an input from the standard input, interpret each line as an integer key to look up and output to the standard output the line of the following format:

```
key 0|1 proofBits hash1 hash2 ... hashN
```

`key`, `proofBits` and all hashes should be represented as unsigned 64-bit integers.

Please include in the submission a simple make or cmake config file or Visual Studio project for us to be able to build it on Linux or macOS or Windows.


[Sparse Merkle Tree description]: ./Sparse%20Merkle%20Tree.md
