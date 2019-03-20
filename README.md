# Memory Leaks
 "A memory leak is a type of resource leak that occurs when a computer program incorrectly manages memory allocations in such a way that memory which is no longer needed is not released" from [Wikipedia](https://en.wikipedia.org/wiki/Memory_leak)

Basic [memory lifecycle](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management#Memory_life_cycle) in most languages:
1. Allocate the memory you need
2. Use the allocated memory (read, write)
3. Release the allocated memory when it is not needed anymore

![Memory Management Flow](./MemoryFlow.png)

## Javascript Memory Management
From the steps in the previous section, you may notice that in Javascript you don't explicitly allocate memory for your variables and such. You just create or initialize them.  

Behind the scenes the Javascript Engine is allocating this memory for you. This makes Javascript a language with automatic memory management.  

This also means that there needs to be something checking if memory allocated previously can be reclaimed. This is the garbage collector.  

All modern browsers come equipped with a mark-and-sweep garbage collector. This will periodically run, traversing from the global garbage collector root(s) (ex. window object) and marking what can be reached.  

![Mark and Sweep Garbage Collector](./MarkAndSweepGC.gif)

Anything left unreachable, can be safely garbage collected. This does imply that in order for something to cleaned up, you have to make it unreachable (typically you won't have to consciously think about this though).

## Common memory leaks
- Unintentional global variables
- Event listeners
- Timers
- Detached DOM references

### Unintentional global variables
<p class="codepen" data-height="265" data-theme-id="0" data-default-tab="html,result" data-user="rcasto" data-slug-hash="WmgpRj" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="Unintentional Global Variable Memory Leak">
  <span>See the Pen <a href="https://codepen.io/rcasto/pen/WmgpRj/">
  Unintentional Global Variable Memory Leak</a> by Richie (<a href="https://codepen.io/rcasto">@rcasto</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

**Rule of thumb:**  
Always declare a variable using `var`, `const`, or `let`.  
Always be certain of what the value assigned to the [`this`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) context is.

### Timers
There are a few timer creators in Javascript, a timer could be created using any of the following:
- [setTimeout](https://devdocs.io/dom/windoworworkerglobalscope/settimeout)
- [setInterval](https://devdocs.io/dom/windoworworkerglobalscope/setinterval)
- [requestAnimationFrame](https://devdocs.io/dom/window/requestanimationframe)

Timers get added to the browser event loop, the conceptual model of this is as follows:
![Javascript Runtime Model](./JavascriptRuntimeModel.svg)

<p class="codepen" data-height="265" data-theme-id="0" data-default-tab="js,result" data-user="rcasto" data-slug-hash="ZPMJWr" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="Timer Memory Leak">
  <span>See the Pen <a href="https://codepen.io/rcasto/pen/ZPMJWr/">
  Timer Memory Leak</a> by Richie (<a href="https://codepen.io/rcasto">@rcasto</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

**Rule of thumb:**   
Every setTimeout, setInterval, requestAnimationFrame should have a corresponding [clearTimeout](https://devdocs.io/dom/windoworworkerglobalscope/cleartimeout), [clearInterval](https://devdocs.io/dom/windoworworkerglobalscope/clearinterval), [cancelAnimationFrame](https://devdocs.io/dom/window/cancelanimationframe).

In [React](https://reactjs.org/) a good time to do this cleanup is in a components [`componentWillUnmount` lifecycle method](https://reactjs.org/docs/react-component.html#componentwillunmount).

### Event listeners
Event-driven programming is very common in Javascript and typically you will add event handlers to DOM nodes or otherwise utilizing `on*` handlers or [`addEventListener`](https://devdocs.io/dom/eventtarget/addeventlistener).

**Rule of thumb:**  
Remember to cleanup event handlers utilizing [`removeEventListener`](https://devdocs.io/dom/eventtarget/removeeventlistener).

### Detached DOM references

## Detecting/finding memory leaks
[Chrome dev tools](https://developers.google.com/web/tools/chrome-devtools/) is an essential tool when trying to find memory leaks and knowing at least some of the basics of using it can help tremendously. The most useful tools for detecting memory leaks within the dev tools are as follows:
- [Performance profiler](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/)
- [Heap snapshot comparison](https://developers.google.com/web/tools/chrome-devtools/memory-problems/heap-snapshots#comparison_view)
- [Allocation Timeline](https://developers.google.com/web/tools/chrome-devtools/memory-problems/allocation-profiler)

## Resources
- https://developers.google.com/web/tools/chrome-devtools/memory-problems/
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop
- https://en.wikipedia.org/wiki/Memory_leak
- https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)
- https://en.wikipedia.org/wiki/Tracing_garbage_collection