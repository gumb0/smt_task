# The SMT Task

Read the [Sparse Merkle Tree description].

Implement Sparse Merkle Tree data structure based on 64-bit hash function (you can use `std::hash`) which supports the following operations:
- construct an empty tree
- insert a 64-bit key into the tree
- lookup the key in the tree and return the proof of existence / non-existence. The proof should be represented as `(existenceFlag, proofBits, array of hashes)`

Create a program that reads a file from the path given as a command-line argument, each line of a file is a hexadecimal string representing 32 bytes of the key to store in the tree.

After populating a tree with keys from the file, the program should get input from standard input, interpret each line as a key to lookup and output to standard output the line of the following format:

```
0|1 proofBits hash1 hash2 ... hashN
```

`proofBits` and all hashes should be represented as unsigned 64-bit integers.


[Sparse Merkle Tree description]: ./Sparse%20Merkle%20Tree.md
