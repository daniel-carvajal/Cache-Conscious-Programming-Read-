# Cache-Conscious Programming


```javascript
// Image represented as 2D array - each value is brightness (0-255)
const image = [
  [120, 145, 190, ...],  // Row 0: 1000 pixels
  [135, 160, 175, ...],  // Row 1: 1000 pixels
  // ... 998 more rows
];

// We need to calculate the average of all pixel values
```

Element 175 is at position `image[1][2]` because it's in the second row (index 1) and is the third element in that row (index 2).​​​​​​​​​​​​​​​​

Can think of it as array of arrays at high-level. 



## Why "Jumping Around" Is Bad

In the inefficient version:
```javascript
for(let i = 0; i < 1000; i++)
  for(let j = 0; j < 1000; j++)
    sum += matrix[j][i];  // Column-wise access
```

This accesses memory in this pattern:
1. `matrix[0][0]`
2. `matrix[1][0]` (1000 elements away in memory)
3. `matrix[2][0]` (another 1000 elements away)
4. And so on...

When you access `matrix[j][i]`, you're accessing by column, but the data is stored by row. This means each memory access requires jumping 1000 elements forward in memory.


## The Efficient Version

```javascript
for(let i = 0; i < 1000; i++)
  for(let j = 0; j < 1000; j++)
    sum += matrix[i][j];  // Row-wise access
```

This version accesses memory sequentially:
1. `matrix[0][0]`
2. `matrix[0][1]` (right next to it in memory)
3. `matrix[0][2]` (right next to that)

Each cache line fetched contains multiple elements you'll use immediately, dramatically reducing memory wait time.




## Performance Impact

This simple change can provide 2-10x speedup because memory access is often the bottleneck. In real-world testing:
- Column-wise: ~1-3 billion cycles
- Row-wise: ~100-300 million cycles



## What Happens In Memory

When JavaScript stores this 2D array, it organizes it in memory like:
```
[Row0Pixel0, Row0Pixel1, ..., Row0Pixel999, Row1Pixel0, Row1Pixel1, ...]
```

The efficient version accesses memory like:
1. Read Row0Pixel0
2. Read Row0Pixel1
3. Read Row0Pixel2
...and so on, moving sequentially through memory.

The inefficient version accesses memory like:
1. Read Row0Pixel0
2. Jump 1000 elements forward to Row1Pixel0
3. Jump 1000 elements forward to Row2Pixel0
...requiring constant large jumps in memory.