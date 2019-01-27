# Performance By Asynchronicity

Javascript code is executed by a single-threaded engine. There is always only one string of code executed at a time. However, this way any running code prevents other parts of code from being executed. Running code isn't bad in itself. But code shouldn't wait for aditional information but permit other parts of code to fill this time of idle whenever available.

Obeying this helps writing super-fast code. The optimum is in choosing proper balance between synchronous code and event-driven deferall of further code execution. As a rule of thumb you might consider to do as much as possible using synchronous code, but whenever there is a lack of further information stop working synchronously but register callback invoked as soon as the lacking information has become available. 
