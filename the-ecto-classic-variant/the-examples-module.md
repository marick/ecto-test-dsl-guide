---
description: >-
  This module contains the definition of the test data, which is mainly a series
  of "examples".
---

# The examples module

Variants come in two, um, variants: one for insertion and one for updating. For the rest of this chapter, I'll use `EctoClassic.Insert` and explain the differences between it and `EctoClassic.Update` later. 

The overall structure of the module looks like this:

```elixir
defmodule Examples.SchemaName.Insert do
  alias App.Schemas.SchemaName, as: SchemaName
  use TransformerTestSupport.Variants.EctoClassic.Insert

  def create_test_data do 
    ...
  end

```

Every examples module must `use` the appropriate variant \(line 3\), and it must contain a function named `create_test_data/0`\(line 5\).

For convenience, `create_test_data` is called lazily the first time a test needs the data. you can call it yourself to get the entire data structure it produces, however:

1. That structure isn't the version actually used in tests. It's readable, but additional annotations are added to simplify `transformer_test_support`'s code.
2. The annotated version is stored in a process. \(Using a process allows it to contain anonymous functions. Despite that, it's still an immutable structure.\)

## The \`start\` function

 `create_test_data` must begin by calling a function, creatively named `start`:

```elixir
  def create_test_data do 
    start(
      module_under_test: SchemaName,
      repo: App.Repo,
    ) |>
    ...
  end

```

{% hint style="info" %}
Note that line 5 above ends in a pipe operator \(`|>`\). All of the remaining functions described on this page accept a "test data" structure. It's unidiomatic Elixir to put the pipe operator at the end of a "producer" line, rather than at the beginning of the "consumer" line. In this one particular case, I think it looks better, and I'm persnickety about code formatting.
{% endhint %}

{% hint style="info" %}
The "test data structure" is actually a plain map. Given that each variant can add its own field \(like `changeset_with` for Ecto Classic\), a map is more convenient than nested `structs`. This may change.
{% endhint %}

### Required arguments

For both `EctoClassic` variants, two keyword arguments are required. 

* **module\_under\_test** names the module that contains  a schema definition and changeset functions. 
* **repo** is the Ecto repo module used for `Repo.insert`.

### Optional arguments

* **changeset\_with** describes the function that converts params into a changeset. It must be a function of two argument: first, the module under test, next the params. The default version is, in effect, this: 

```elixir
    start(
      ...
      changeset_with: fn module_under_test, params -> 
        default_struct = struct(module_under_test)
        module_under_test.changeset(default_struct, params)
      end
```

* **insert\_with** takes a changeset to insert and returns either an `{:ok, %SchemaName{}}` tuple or an `{:error, %Changeset{}}` structure. The default value is `&App.Repo.insert/1`. 
* **format** changes variant-specific parameter formatting. In Ecto Classic, parameters are written in a convenient way, like `age: 5`. However, they are converted into the format Phoenix uses to pass parameters to a controller. That is: 

  * the keys are strings, not symbols.
  * integers are passed as strings \(so: `"age" => "5"`\).
  * lists are passed as stringified lists like `["1", "2", "3"]`.
  * maps are passed as stringified maps  The last two points mean that you can use deeply nested structures in tests without worrying about converting them into Phoenix's `name1.name2.name3[]` convention. 

  
  If you want parameters passed in their original form, use `format: :raw`. Ecto's `cast` function handles both the same, as far as I know.

## Workflows

Workflows group examples according to how they are processed. Each variant will have its own workflow categories. For Ecto Classic, they are these:

```elixir

  def create_test_data do 
    start(...) |> 
    workflow(                       :success, 
      ...),
    workflow(                       :validation_success, 
      ...),
    workflow(                       :validation_error,
      ...),
    workflow(                       :constraint_error,
     ...
    ) |>

```

* The **success** workflow creates a changeset from the params, checks that changeset for correctness, calls `Repo.insert`, and checks for an `{:ok, <schema struct>}` result. \(Note: it checks nothing about the returned schema struct, since that would really be testing whether Ecto works. You can, however, capture the results of insertion to make your own checks, which is useful if you have overridden the `insert_with` function. _**add link**_\)
* The **validation\_success** workflow stops after checking the changeset. It's useful when you want to stop before some kind of complicated insertion.
* The **validation\_error** workflow is used when you expect an invalid changeset \(because of errors in the params\). It stops after checking the changeset, since it makes no sense to try to insert.
* The **constraint\_error** workflow is like **success**, except that it expects the insertion to fail. \(That is, it assumes the return value from `Repo.insert` will be `{:error, <changeset>}`, and it will typically describe the errors in that changeset.\)

Workflows contain examples that are given as a keyword list. The keys are _example names_ and the values are lists that describe examples.

```elixir
workflow(                                         :success,
  bossie: [..],
  jake: [...],
  ...
)
```

