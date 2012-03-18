Node.js module binding for catastrophe | Spencer Tipping
Licensed under the terms of the MIT source code license

# Introduction

This just binds the catastrophe global to the module. If you're using catastrophe from inside node.js, you'll need to make sure to set the global object appropriately (see the source code
documentation for details about this).

    exports.catastrophe = catastrophe;