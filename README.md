# Hash Hash Hash
This lab creates a hash table implementation that is safe to use concurrently. The base hash table implementation `hash-table-base` implements the hash table sequentially using chaining to deal with collisions. The following hash table implementations-`hash-table-v1` and `hash-table-v2` run the hash table concurrently with multiple threads. `hash-table-v1` runs a course-grained locking, and `hash-table-v2` runs a fine-grained locking. 

## Building
To build the program, first download all files contained in this repository (excluding README.md). Then run the following command in the directory containing all the files: 
```shell
make
```

## Running
To run the program, use the following command:
```shell
./hash-table-tester -t 8 -s 50000
```
`-t` refers to the number of threads to test with. Follow the `-t` arguement with the number of threads you would like to test with (default is 4). `-s` changes the number of hash table entries to add per thread (default is 25,000). 

## First Implementation
In the `hash_table_v1_add_entry` function, a course-grained locking strucuture was implemented to allow for the correct implementation of concurrency to work. This structure involved locking and unlocking the entire critical section within `hash_table_v1_add_entry`. The critical section included getting the hash table entry, the list head, and the list entry and then creating and adding the list entry to the list. Thus, a global lock was created, and before the hash-table was accessed to get the correct hash table entry, and it was unlocked after the entry was created and added. The lock was destroyed in `hash_table_v1_destroy` along with the destruction of the hash table. 

### Performance
To test the correctness and implementation of the hash table, the same test was run three times. 
```shell
//test code
./hash-table-tester -t 4 -s 50000
```
Test 1 Results
```
# Generation: 44,713 usec
# Hash table base: 82,389 usec
#   - 0 missing
# Hash table v1: 217,133 usec
#   - 0 missing
```
Test 2 Results
```
# Generation: 55,767 usec
# Hash table base: 333,800 usec
#   - 0 missing
# Hash table v1: 689,031 usec
#   - 0 missing
```
Test 3 Results
```
# Generation: 66,960 usec
# Hash table base: 348,512 usec
#   - 0 missing
# Hash table v1: 695,649 usec
#   - 0 missing
```
There are 0 missing entries in the v1 hash table which means that the implementation of concurrency is correct. However, the time it takes for the v1 implementation is about doubled compared to the base implementation. This is because of the thread overhead with the lock. Since there is only one lock, each thread must share the same lock and only one thread can go through the lock at a time. Thus, even though there are multiple threads running concurrently, there is a bottleneck because of the lock, which causes more overhead and decreases performance time. 

## Second Implementation
In the `hash_table_v2_add_entry` function, a fine-grained locking structure was implemented to reduce the amount of time it took to add all the entries while still allowing for correctness in the implementation. This was done by adding a lock to each table entry inside `hash_table_v2_create`. Then, inside `hash_table_v2_add_entry`, the lock for the corresponding `hash_table_entry` was locked after accessing the table entry, and unlocked after adding the new list entry to the list. Then in `hash_table_v2_destroy`, all the locks were destroyed so as to not cause any memory leaks. 

### Performance
To test the correctness and implementation of the hash table, the same test was run three times. 
```shell
//test code
./hash-table-tester -t 4 -s 50000
```
Test 1 Results
```
# Generation: 43,008 usec
# Hash table base: 80,530 usec
#   - 0 missing
# Hash table v2: 20,944 usec
#   - 0 missing
```
Test 2 Results
```
# Generation: 41,069 usec
# Hash table base: 81,967 usec
#   - 0 missing
# Hash table v2: 22,154 usec
#   - 0 missing
```
Test 3 Results
```
# Hash table base: 85,382 usec
#   - 0 missing
# Hash table v2: 23,282 usec
#   - 0 missing
```

This time the speed up is about 4 times as fast as the sequential hash table that does not use any concurrency. This is because locking each individual hash table entry doesn't create a bottleneck like course-grained lock for v1. The results of the tests match up with the expected speeds for adding entries into the hash table. The hash table should run the number of threads times faster than a sequential adds because each of these threads add a list_entry to the hash table as long as they are not competing for the same hash table entry. 

## Cleaning up
To clean up, run the following command on the shell:
```shell
make clean

```