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
In the `hash_table_v1_add_entry` function, a course-grained locking strucuture was implemented to allow for the correct implementation of concurrency to work. This structure involved locking and unlocking the entire critical section within `hash_table_v1_add_entry`. Thus, a lock was created before the hash-table was accessed to get the correct hash table entry, and it was unlocked and destroyed after the entry was created and added. 

### Performance
To test the correctness and implementation of the hash table, the same test was run three times. 
```shell
//test code
./hash-table-tester -t 8 -s 50000
```
Test 1 Results
```
Generation: 72,600 usec
Hash table base: 362,071 usec
  - 0 missing
Hash table v1: 703,576 usec
  - 0 missing
```
Test 2 Results
```
Generation: 55,767 usec
Hash table base: 333,800 usec
  - 0 missing
Hash table v1: 689,031 usec
  - 0 missing
```
Test 3 Results
```
Generation: 66,960 usec
Hash table base: 348,512 usec
  - 0 missing
Hash table v1: 695,649 usec
  - 0 missing
```
There are 0 missing entries in the v1 hash table which means that the implementation of concurrency is correct. However, the time it takes for the v1 implementation is about doubled compared to the base implementation. This is because of the creation and destruction of the mutex locks each time an entry is added. Everytime an entry is added to the hash table, the program creates a lock, locks the lock, adds the entry, unlocks the lock, and then destroys the lock. Thus, this created a lot of overhead that caused the performance of the v1 implementation to be slower than the base implementation. 

## Second Implementation
In the `hash_table_v2_add_entry` function, a fine-grained locking structure was implemented to reduce the amount of time it took to add all the entries while still allowing for correctness in the implementation. 

### Performance
```shell

```

This time the speed up is xxx

## Cleaning up
To clean up, run the following command on the shell:
```shell
make clean

```