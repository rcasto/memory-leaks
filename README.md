# Memory Leaks
 "A memory leak is a type of resource leak that occurs when a computer program incorrectly manages memory allocations in such a way that memory which is no longer needed is not released" from [Wikipedia](https://en.wikipedia.org/wiki/Memory_leak)

Most common sources on the web:
- Unintentional global variables
- Event listeners
- Timers: setTimeout, setInterval, requestAnimationFrame...
- Detached DOM references
- External dependencies

