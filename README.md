# Catastrophe tracing debugger

Catastrophe is a tracing non-interactive debugger for Javascript. It works by recording every step of a function's execution and then allowing you to query the trace to find problems. Note
that this is not a fast library; on my netbook it takes more than 30 seconds to annotate a minified jQuery without pretracing.

## Implementation status

This project sort of works right now.

    patterns:                        probably broken
    native eval tracing:             untested, but what could possibly be wrong?
    closure inspection/modification: not yet implemented
    pretrace mode:                   untested
    trace mode:                      successfully traces jQuery

## Quickstart guide

This guide goes through a quick example of how to use Catastrophe to find problems in client-side code. You can also use it on the server-side, for instance in node.js.

### Step 1: Tracing a function

  Catastrophe works by wrapping the function you want to debug. All code within this function will be instrumented with debugging hooks that Catastrophe uses to gather data (this process is
  very similar to Caterwaul's function recompilation). For example, here's how you would instrument a simple jQuery webapp:

    <script src='catastrophe.js'></script>      <- include catastrophe script
    <script>
      tracer = catastrophe();
      $(tracer(function () {                    <- pass the instrumented function to $()
        // app code here
      }));
    </script>

Because `tracer` is a global variable, you can then interact with it on the console.

### Step 2: Querying the trace

Let's suppose you want to find jQuery selections that ended up containing no DOM nodes. This can be done by first filtering for all jQuery calls, and then filtering again for those whose
results' length is zero. Here's what that looks like in the Javascript console:

    > tracer.find('$(_x)').filter('v(_).length === 0').map('_x.toString()')
    ['expression1', 'expression2', ...]
    >

In the query above, things starting with an underscore were wildcards. The `v()` function gives you the value of a given syntax node; `_` contains the entire matched tree. If you want to
bind to a specific constant that happens to start with an underscore, you can use a filter:

    > tracer.find('_x + _y').filter('_x.data === "_x"')

This matches only additions whose left-hand side is literally the identifier `_x`.

### Step 3. Pulling values out

You can get all of the values generated by the program and manipulate them on the console. For example, here's an array of the output of each jQuery selection:

    > tracer.find('$(_x)').map('v(_)')

You can also ask about the values of tree pieces. Here's an array of pairs; each pair contains a string selector and the resulting jQuery output at the time the selection originally ran:

    > tracer.find('$(_x)').map('[v(_x), v(_)]')