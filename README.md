# Catastrophe tracing debugger

Catastrophe is a tracing non-interactive debugger for Javascript. It works by recording every step of a function's execution and then allowing you to query the trace to find problems. It can
also speculatively execute code under given hypothetical conditions. This, in turn, produces more traces that you can query.

NOTE! This project doesn't work yet and may change significantly before it does.

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

    > tracer.find('$(_x)').filter('_.value.length === 0').map('_x.value')
    ['selector1', 'selector2', ...]
    >

Now suppose that the code is written such that jQuery takes on a variety of different names. The query above won't work because it assumes that jQuery is called `$`. To fix it, we'll need
to match on any function call whose function object is equal to the jQuery global:

    > tracer.find('_f(_x)').filter('_f.value === jQuery && !_x.value.length').map('_x.value')
    ['selector1', 'selector2', ...]
    >

In the queries above, things starting with an underscore were wildcards. If you want to bind to a specific constant that happens to start with an underscore, you can use a filter:

    > tracer.find('_x + _y').filter('_x.data === "_x"')

This matches only additions whose left-hand side is literally the identifier `_x`.