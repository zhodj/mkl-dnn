Verbose Mode {#dev_guide_verbose}
========================================================

It is often useful to collect information about how much of an application
runtime is spent executing Intel(R) Math Kernel Library for Deep Neural
Networks (Intel(R) MKL-DNN) primitives and which of those take the most time.
Intel MKL-DNN's verbose mode enables tracing execution of Intel MKL-DNN
primitives and collection of basic statistics like execution time and
primitive parameters.

The behavior is controlled with `MKLDNN_VERBOSE` environment variable or
@ref mkldnn_set_verbose function.

| Value | Behavior
| :---- | :----
| **0** | no verbose output (default)
| 1     | primitive information at execution
| 2     | primitive information at creation and execution

The function setting takes precedence over the environment variable.

The first line of verbose information contains the build version and git hash,
if available, as well as the supported instruction set architecture.

Each subsequent line of verbose information is formatted as a comma-separated list
containing:
- `mkldnn_verbose` marker string
- operation: `create` or `exec`
- primitive name: `convolution`, `reorder`, `sum`, etc
- primitive implementation
- propagation: `forward_training`, `forward_inference`, or `backward`
- information about input and output data types and formats
- auxiliary information like algorithm name or number of inputs
- a problem description in [benchdnn format](@ref dev_guide_benchdnn)
- execution time in milliseconds

## Example

~~~sh
MKLDNN_VERBOSE=1 ./benchdnn --conv ic16ih7oc16oh7kh5ph2n"wip"
~~~

This produces the following output (the line break was added to fit the page width):

~~~sh
mkldnn_verbose,info,Intel(R) MKL-DNN v0.95.0 (Git Hash ce116d48579332ae7e51a46219114f8d3c1e48db),Intel(R) Advanced Vector Extensions 2 (Intel(R) AVX2)
mkldnn_verbose,exec,reorder,jit:uni,undef,src_f32::blocked:abcd:f0 dst_f32::blocked:aBcd8b:f0,num:1,2x16x7x7,0.468994
mkldnn_verbose,exec,reorder,jit:uni,undef,src_f32::blocked:abcd:f0 dst_f32::blocked:ABcd8b8a:f0,num:1,16x16x5x5,0.458008
mkldnn_verbose,exec,reorder,jit:uni,undef,src_f32::blocked:abcd:f0 dst_f32::blocked:aBcd8b:f0,num:1,2x16x7x7,0.453857
mkldnn_verbose,exec,reorder,simple:any,undef,src_f32::blocked:a:f0 dst_f32::blocked:a:f0,num:1,16,0.462891
mkldnn_verbose,exec,convolution,jit:avx2,forward_training,
    src_f32::blocked:aBcd8b:f0 wei_f32::blocked:ABcd8b8a:f0 bia_f32::blocked:a:f0 dst_f32::blocked:aBcd8b:f0,
    alg:convolution_direct,mb2_ic16oc16_ih7oh7kh5sh1dh0ph2_iw7ow7kw5sw1dw0pw2,0.026123
mkldnn_verbose,exec,reorder,jit:uni,undef,src_f32::blocked:aBcd8b:f0 dst_f32::blocked:abcd:f0,num:1,2x16x7x7,0.464111
~~~

Please see the profiling example [here](@ref dev_guide_verbose), as it uses
MKLDNN_VERBOSE output to tune Intel MKL-DNN code to align with
[best practices](@ref dev_guide_inference).

@warning
Verbose mode has non-negligible performance impact especially if the output
rate is high.


--------
[Legal information](@ref legal_information)
