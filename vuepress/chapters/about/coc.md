# Convention over Configuration

This term refers to a paradigm to design your code in a way that it expects all other code to comply with certain conventions when it comes to naming functions, laying out files, processing options etc. instead of supporting complex configurations. This is improving performance, simplifying code and thus accounts to reducing probable errors by reducing size of code.

So for example, Hitchy is detecting folders containing extension by checking whether it's containing file `hitchy.json`. It is processing files in subfolder `config/` for any necessary configuration. Yes, configuration isn't prohibited, but its complexity is limited and its not required in most cases.
