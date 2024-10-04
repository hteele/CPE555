# RT Embedded Systems Lecture 3 (Lect 2 Pt III)

(thanks gustavo)

### Caches

caches are used between CPU and main memory to improve access times,

- implemented with fast memory tech like SRAM
- takes advantage of temporal/spacial locality by storing values that are likely to be accessed
- It is essentially just very small very fast memory that is physically closer to the CPU to store frequently used data

### Cache basics

- caches are divided into blocks of various sizes
- typical diagrams of caches show the contents as blocks that are one word in size
- when Xn is initially requested, it is not in the cache and must be fetched from memory
- after the first request, Xn is cached
- this action significantly reduces access time of already called data
- if cache is filled, cache removes the variable that was updated first (oldest piece of info)

### Cache questions

- When we copy blocks of data from main memory, where should we put it in cache
    - simplest way: each memory location is mapped to exactly one location in cache and mapping is done on memory address (mapping is NOT one to one, cache is MUCH smaller than main memory)
    - if the cache contains 2^k blocks, then the data at memory address i would go to the cache block with index i mod(2^k) [this part makes alot more sense if you look at an image of color coded cache blocks]
    - the previous step is accomplished by taking the least k significant bits of the memory address as the cache index (so something with memory address 0011) in a k bit cache would get stored as 11 in the cache
    - another issue to address is how to tell where the cached memory comes from in main memory (any data cached in 11 in cache memory could come from any main memory location with least significant bits 11)
    - the remaining bits are used to solve this, they are called the tag. 
    - Index bits are the least k significant bits and it tells us where the data should go to in cache,
    - Tag bits are the remaining bits and they tell us where cached memory comes from in main memory
    - When cache is empty and does not contain valid data, the data in it is garbage, thus we assign a valid bit for each cache block
        - this bit is 0 for when the cache is initiated and does not go to 1 until data is written into the bit
    - When CPU reads from memory, the address is sent to a cache controller,
        - the cache controller takes the lowest k bits of the address and will index a block in the cache
        - if the block is valad and the tag maches the upper (m-k) bits of the m bit address then the data will be sent to the CPU
    - in essense, the cache stores the index, the valid bit, the tag bit, and the data, the size of the data at a cache is = (2^k)*(1+(m-k)+8)

### Spatial locality

- One byte cache blocks dont take advantage of spatial locality which predicts that an access to one address will be followed by an access to a nearby one
- to take advantage of this, we preemptivly store data nearby the requested data in cache to make the system read faster
- we can take advantage of this by doing our previous indexing system by having multiple memory addresses to 
- now we need block addresses which we use INTEGER DIVISION FOR
    - block address of a byte address i = i/2^n where n is the number of columns in cache
    - i.e. 12/2 = 6 13/2 = 6 (truncation)
- lets assume a cache with 2^k blocks, each containing 2^n bytes, we can determine where a byte of data belongs in this cache by looking at its address in main memory
    - k bits of the address will select one of the 2^k cace blocks
    - the lowest n bits are not a block offset that decides which of the 2^n bytes in the cace block will store the data.
    - finally we also have a valid bit in this structure
    - total number of bits = (2^k)*(block size + tag size + valid bit)

### Class exercises

- how many total bits are required for a direct mapped cache with 16 KiB of data and 4 word blocks, assuming a 32-bit address (KiB = kibibyte = 2^10 bytes, 1 word = 4 bytes)
    - we are given data size, we just need to find size of tag + index + valid
    - size of data is (2^14) we know that that number is 2^k * 2^4 so we know k must be 10 this is the number of rows
    - so we have 1024 rows
    - tag size = size of address - k - n = 32 - 4 - 10 = 18 bits for tag
    - total size = a x b where a is the total length and b is total rows (2^k)
    - total size = (2^k)x(1+18+16*8) 
    - 8 comes from the number of bytes 

- Consider a cache with 64 blocks and a block size of 16 bytes. To what block number does byte address 1200 map 
    