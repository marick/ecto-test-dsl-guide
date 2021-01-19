---
description: >-
  You automagically get a submodule of the Examples module. It contains
  functions used to run tests.
---

# The Tester submodule

When you `use` a variant in your Examples module, like this:

```elixir
defmodule Examples.SchemaName.Insert do
  use TransformerTestSupport.Variants.EctoClassic.Insert
  ...
end
```

You can think of yourself as also defining a submodule that looks like this:

```elixir
defmodule Examples.SchemaName.Insert do
  use TransformerTestSupport.Variants.EctoClassic.Insert
  ...
  defmodule Tester do 
    def test_data()...
    def example(name)...
    def params(example_name)...      
    def check_workflow(example_name, trace_opts \\ [])...
    ...
  end
end
```

All of the above functions are common to every variant. The most important is **check\_workflow.** It runs a series of creation and checking steps and reports the results via ExUnit. The tracing options are explained \[\[\[where\]\]\]. 

## Workflow functions



## Diagnostic functions

**test\_data\(\)** returns how the results of `create_test_data` are stored. There's a lot of repetition in it for convenience, so it's unlikely to be interesting to you.

**example\(name\)** returns the portion of the test data that is specific to one example. It's also fairly complicated:

