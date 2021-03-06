This assumes prior knowledge of [Merkle Trees](https://en.wikipedia.org/wiki/Merkle_tree) and Merkle proofs.

See [Merkle Tree &amp; Merkle Root Explained](https://www.mycryptopedia.com/merkle-tree-merkle-root-explained/) and [Merkle Trees - Efficient Data Verification](https://www.youtube.com/watch?v=fpAxfVWXjds) for succinct introduction.

## Sparse Merkle Trees

### Introduction

A Sparse Merkle Tree (SMT) is a [full binary] Merkle tree that contains a _leaf_ 
for every possible output of a hash function.
For a hash function with N-bit output the SMT:
 - height: _h = N_,
 - number of leaves: _l = 2<sup>N</sup>_,
 - number of all nodes: _n = 2<sup>N + 1</sup> - 1_.

For example when using any 256-bit hash function the corresponding SMT has
2<sup>256</sup> leaves.

Inserting or looking up a key in the tree involves going down the branch of the tree from the root to the leaf, while at the same time iterating the bits of the key from the most significant bit 
to the least significant bit. Each bit of the key determines whether we go down to the left or to the right child.


### Implementation

Since the full in-memory representation of an SMT is impractical 
(to say the least), we have to simulate it. 
The simulation is practical because the tree is sparse: most leaves are empty
and the hashes of empty leaves are the same. 
Similarly, the hashes of _internal_ nodes whose children are all empty are 
the same.

This means that only the hashes for the `k` Merkle branches need to be computed,
where `k` is the number of keys stored in the tree.

### Default hashes

It is valuable to pre-compute a set of _default hashes_ for all `h` levels:

- at level 0, the default hash is `D0 = H("")`,
- at level 1, the default hash is `D1 = H(D0 || D0)`,
- at level 2, the default hash is `D2 = H(D1 || D1)`

... and so on,
where `H` is the hash function returning the result hash as an array of bytes, 
`""` is 0-length array of bytes and `||` represents bytes concatenation.

In a sparse tree almost all leaves (level 0 nodes) have the value `D0`, 
almost all level 1 nodes have the value `D1`, 
almost all level 2 nodes have the value `D2`, and so on.
Only the nodes that lead to a non-zero leaf value actually need to be 
computed individually - the rest have the same value and we can compute them 
efficiently.

### Example 1

Here's an illustration of the simplified SMT storing 4-bit values. 
Here to store the keys `5` and `C` we only need to compute the hashes 
corresponding to white circles, all grey circles are default hashes and 
should not be duplicated in the storage.
![Sparse Merkle Tree example image]

## Sparse Merkle Proofs

Unlike a proof in the usual Merkle Tree, proofs in SMT are **not** `h` N-bit 
hashes going all the way from level 0 at the leaf all the way to the root! 

Instead, a proof has a format `(proof_bits, array_of_hashes)`, 
where  `proof_bits`  is `h` bits that can compactly represent whether the hashes
going up to the root are default hashes or not – that is, for _i-th_ bit:

- 0 means "use the default hash of level _i_"
- 1 means "use the corresponding hash from the `array_of_hashes`"

The `array_of_hashes` contains just the non-default hashes in order of tree levels.
The last element in the array is always the tree root hash.

### Example 2

For the tree illustrated above:

- `H2 = H(D0 || H("5"))`
- `H1 = H(D2 || H(H2 || D1))`
- The proof of existence of the key `5` : `(0b0001, H3, Hroot)`
- The proof of existence of the key `C` : `(0b0001, H1, Hroot)`
- The proof of non-existence of the key `6` : `(0b0101, H2, H3, Hroot)`


[full binary]: https://wikipedia.org/wiki/Binary_tree
[Sparse Merkle Tree example image]: assets/SMT.jpg
