# Z-Ant MNIST-8 Profiling Report

What now follows is a callgrind and massif aided report of the performance of a drawn digit recognition MNIST-8 prediction, it as been set up according to pre-existing tests regarding auto-generated libraries using the new codegen features recently introduced in the Z-Ant codebase.

All tests were run on an Arch machne running a Ryzen 5 3600 and 16GB of DDR4 3000MHz RAM. No other CPU-Intensive processes were running throughout the duration of the tests, and each profiling task was run ten times using a shell script.

## Callgrind Profiling

All callgrind runs were done by running the command `valgrind --tool=callgrind --dump-instr=yes *bynary name*`.

>Methods sorted by inclusion percentage:\
[Callgrind - Inclusion Percentage](./screenshot-incl.png)

>Methods sorted by self percentage:\
[Callgrind - Self Percentage](./screenshot-inclself.png)

>Methods sorted by number of calls:\
[Callgrind - Calls](./screenshot-callsnum.png)

Throughout the callgrind runs a function stood out as massive optimization targets: the ONNX convolution with bias functions, only two calls of it comprised ~35% of all the runtime of the application, it's a reasonable and expected result, as the convolution function is the main actor in calculating a prediction, it makes sense that the most performance demand arises from its calls.

With a whopping ~27K calls `Tensor(f32).flatten_index` made up ~18% of all runtime,

All ten callgrind runs yielded the same results, plus/minus one or two memset calls that are probably related to under-the-hood linux workings, no notable variations were seen in any of the zig-related functions.

## Massif Profiling

All massif runs were done by running the command `valgrind --tool=massif --detail-freq=2 *binary name*`.

>Massif-Visualizer Heap Space Graph:\
[Massif-Visualizer Graph](./screenshot-heapgraph.png)

All ten massif runs yielded the same exact graph, heap consumption peaks happen exactly during the two calls to the ONNX Convolution function mentioned beforehand, and result in a peak consumption of 233.4 KB.

## Execution Time

An additional ten runs were done without any external tools running and timed with the standard unix command `time *binary name*`, yielding on average a 6.2ms run time, of which on average 0.9ms were due to system methods, and 5.3ms were due to the actual program.