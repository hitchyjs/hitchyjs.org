# The Common Module Function Pattern (CMFP)

As a concept derived from [common module pattern](cmp.md) functions might be in compliance with _common module function pattern_ by satisfying these criteria:

1. It **must** expect reference on `api` via its `this` variable.
2. It **must** expect reference on `options` in first argument.
3. It **might** expect any _additional arguments_ succeeding that first one.
4. It **might** return Promise instance to delay actual result. This **may** suspend overall processing for relying on this function's result.

In several cases a component complying with Common Module Pattern is exporting functions via this pattern which comply with Common Module Function Pattern and thus are called with references on `api` and `options`, too. Though this is slightly redundant by design it establishes more flexibility in designing components and modules since modules mustn't comply with Common Module Pattern as a whole, but still might provide functions complying with this pattern. In addition passing references locally might result in a slightly better performance due to preventing deeply accessing closure scope variables.
