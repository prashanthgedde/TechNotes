# Database Indexes - BTree vs B+Tree


## BTrees - Why was it Invented
* Assuming we have millions of rows stored in a DB how do we search assuming there is no index?
* Perform scan starting at first row, searching for the row with the specified key value.
* To speed up probably, we can partition the table and parallelize the search.
* But in both of these cases, we would end up FULL-TABLE scan without indexes.

## B-Tree indexes 
* We would reduce the search space which minimizes table scan.
* B-Tree Index: additional data structure which can help to perform faster search. With logarithmic lookup time.
* Generalized binary tree.
* Reduces the search spaces - faster search speed. 
* Each check in the tree can reduce the search space by half.
* Additional cost: Balancing the tree - as a new row is inserted, additional cost of balancing the tree.
* https://www.cs.usfca.edu/~galles/visualization/BTree.html
* Nodes in the tree also contains values - This increases the size of each node which could potentially increase the disk io while searching.

## B+ Tree indexes
* Main difference netween B and B+ tree indexes is that only the leaf nodes will keep the values of records.
* All intermediate nodes in the tree will only contain key value, reducing the size of overall index.
* Each leaf node will also additional pointer to find adjacent nodes which allows us to perform range queries.

* Almost BTree's are not used. MySQL and Postgress uses B+ Trees for indexing.
* If there's no requirement of range queries, its OK to use BTree.
* MongoDB uses B-Tree, may be its because its Key-Valu store(?).
* From Discord blog (https://blog.discord.com/how-discord-stores-billions-of-messages-7fa6ec7ee4c7): they moved from MongoDB to Cassandra:
>``` the data and the index could no longer fit in RAM and latencies started to become unpredictable. It was time to migrate to a database more suited to the task.```
* Could be because MongoDB uses B-Tree, which incurs huge index size as it embeds data within the index nodes.
* B+ tree will not have this problem as only the leaf nodes have pointers to data.


https://stackoverflow.com/questions/870218/what-are-the-differences-between-b-trees-and-b-trees

Advantages of B+ trees:
* Because B+ trees don't have data associated with interior nodes, more keys can fit on a page of memory. Therefore, it will require fewer cache misses in order to access data that is on a leaf node.
* The leaf nodes of B+ trees are linked, so doing a full scan of all objects in a tree requires just one linear pass through all the leaf nodes. A B tree, on the other hand, would require a traversal of every level in the tree. This full-tree traversal will likely involve more cache misses than the linear traversal of B+ leaves.

Advantage of B trees:
* Because B trees contain data with each key, frequently accessed nodes can lie closer to the root, and therefore can be accessed more quickly.

![](https://i.stack.imgur.com/l6UyF.png)


