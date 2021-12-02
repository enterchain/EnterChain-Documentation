# Bonsai Tries

Previously most Ethereum Mainnet clients use what’s called a “forest of tries” to store Ethereum’s complex and vital state. () Pronounced, “try,” these data structures, when strung together (as trees), snapshot what the Ethereum blockchain looks like at a point in time. Transactions, account updates, smart contract changes, and metadata are all stored in tries within tries.

As a refresher, state is defined as a set of variables describing a certain system at a specific time, which you can read more about [here](https://consensys.net/blog/blockchain-explained/want-to-really-understand-blockchain-you-need-to-understand-state/).\


![The Forest storage format requires trie traversal to access account and transaction data from the leaf nodes](https://cdn.consensys.net/uploads/2021/10/05164950/Bonsai-7.png)

**How It Works**

Enter Bonsai Tries, a new storage format for Besu; the Quore's blockchain engine. The experimental “bonsai” flag, when set, starts a Besu node with a new “flat” storage structure. Instead of keeping large “trees” within storage, Bonsai keeps only the most recent trie in its storage as well as a trie log layer. This log layer provides a smaller store of changes that, when needed, can be used to construct the complete history of the tries, not just the most recent. This reduces storage and offers much faster times for nodes to read any data about Ethereum’s current state (O(1) account lookups). It also makes accessing the most recent data on the blockchain much faster, though lookups farther back take a little more time. To get into the weeds, the bonsai approach also provides implicit tree pruning, reduced disk usage by nodes, faster synchronization of nodes, and a reduction of the client “running blocks behind” the network.

![Bonsai Tries uses flat account storage and a Trie Log Layer for instant read access to information, only traversing the tries as needed](https://cdn.consensys.net/uploads/2021/10/05165500/Bonsai-8-1.png)

**Performance Benchmarks**&#x20;

Now let’s look at some numbers. When we sort by the storage approach, a full archive of the Ethereum network is less than 1TB. Our tests showed in the older Forest mode, nearly 12.5TB were needed for the same archive. Even nodes running in Fastsync mode (designed to operate faster and lighter than a full archive), become much larger than those running Bonsai over time. When paired with Fastsync, Bonsai synchronizes with the Ethereum network 11 hours faster in our tests than the Forest mode. For those running nodes over time, this is a marked improvement for infrastructure. After two months, we recorded a \~554 GB difference in size between Fastsync Forest and Bonsai nodes. All of this culminates in faster read and storage performance for Besu nodes everywhere.&#x20;

![Testing of storage sizes in Hyperledger Besu produced the above results around state storage size](https://lh3.googleusercontent.com/94hOfhsF-z-NptfDI4rgeX\_vr7NpcLyVvS5JzGC6RHdDxq63pt9UeJvmei3tVFKnGy4dRnQiMoOMI8Hi-QuwrHuKdKVFve8zcYdOdJ9z2SFDm1c20g6-MhXBmd4csmenih\_nwQmF=s0)



