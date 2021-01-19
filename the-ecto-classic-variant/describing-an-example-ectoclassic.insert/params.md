---
description: Every example needs params.
---

# Params \(including associations\)

Params are described with the `params` function. It's typically first in an example's description, but it can be anywhere.

```elixir
  bossie: [
    params(name: "Bossie", age: 10),
    ...
  ]
```

## \`params\_like\`

You can also use `param_like` to refer to an example previously described. Given the definition of `:bossie` above, the following two are equivalent:

```elixir
  jake: [params(name: "Jake", age: 10)]
  
  jake: [params_like(:bossie, except: [name: "Jake"])]
```

**Note: does params\_like work if bossie used an \`id-of\`?**

The `:except` can be omitted, in which case `:jake` would have the same params as `:bossie`. 

The `:except` clause can add fields that aren't present in the original:

```elixir
  params_like(:bossie, 
     except: [name: "Jake", species: "equine"])]
```

## Ecto associations

Associations are handled by giving specific values to foreign keys. Consider an `Animal` schema with a `belongs_to` association to a `Species` schema. That's represented by giving a value to the `Animal`'s `species_id` foreign key:

```elixir
    params(name: "Bossie", species_id: ...)
```

Most likely, you don't want to give `:species_id` an explicit integer or GUID value. Instead, you want the database to create the `Species` row and use whatever value it gave as the primary key. That is accomplished as follows. Suppose you have a `SpeciesExamples` module containing a `:bovine` example:

```elixir
  defmodule SpeciesExamples do
    use Template.EctoClassic.Insert

    def create_test_data do 
      start(...) |> 
      workflow(:success, 
        bovine: [params(...)])
    end
  end
```

Then `:bossie` can be created "within" species `:bovine` like this:

```elixir
bossie: [params(
           name: "Bossie", 
           species_id: id_of(bovine: SpeciesExamples))])
```

{% hint style="info" %}
At the moment, you can only fetch the primary key of another example, and that primary key must be named `:id`. All that's easily changed upon request.
{% endhint %}

The previous example showed how to refer to an example in a different module. If you want to use an example in the same module, you can abbreviate to just the example name:

```elixir
bossie: [params(
           name: "Bossie", 
           mate_id: id_of(:ferdinand))]
```

### Mechanism

Whenever an example is used, the very first step is to insert any other examples it depends on. Then those values are used to construct the examples params. If you trace the running of an example, you'll see those recursive creations. Here's an example of creating \`:bossie

```elixir
TBD: replace with bossie


> Run :duplicate_name (:constraint_error)
  :previously
    > Run :bossie (:success)
      :previously
        result: %{}
      :params
        params: %{"age" => "10", "date_string" => "2000-01-10", "name" => "Bossie"}
        result: %{"age" => "10", "date_string" => "2000-01-10", "name" => "Bossie"}
      :make_changeset
        result: #Ecto.Changeset<action: nil, changes: %{age: 10, date: ~D[2000-01-10], date_string: "2000-01-10", days_since_2000: 9, name: "Bossie"}, errors: [], data: #App.Schemas10.Named<>, valid?: true>
      :check_validation_changeset
      :insert_changeset
        result: {:ok, %App.Schemas10.Named{__meta__: #Ecto.Schema.Metadata<:loaded, "named">, age: 10, date: ~D[2000-01-10], date_string: "2000-01-10", days_since_2000: 9, id: 311, inserted_at: ~N[2020-12-09 00:59:07], name: "Bossie", updated_at: ~N[2020-12-09 00:59:07]}}
      :check_insertion
    < Run :bossie
    result: %{{:bossie, Examples.Schemas10.Named.Insert} => %App.Schemas10.Named{__meta__: #Ecto.Schema.Metadata<:loaded, "named">, age: 10, date: ~D[2000-01-10], date_string: "2000-01-10", days_since_2000: 9, id: 311, inserted_at: ~N[2020-12-09 00:59:07], name: "Bossie", updated_at: ~N[2020-12-09 00:59:07]}}
  :params
    params: %{"age" => "10", "date_string" => "2000-01-10", "name" => "Bossie"}
    result: %{"age" => "10", "date_string" => "2000-01-10", "name" => "Bossie"}
  :make_changeset
    result: #Ecto.Changeset<action: nil, changes: %{age: 10, date: ~D[2000-01-10], date_string: "2000-01-10", days_since_2000: 9, name: "Bossie"}, errors: [], data: #App.Schemas10.Named<>, valid?: true>
  :check_validation_changeset
  :insert_changeset
    result: {:error, #Ecto.Changeset<action: :insert, changes: %{age: 10, date: ~D[2000-01-10], date_string: "2000-01-10", days_since_2000: 9, name: "Bossie"}, errors: [name: {"has already been taken", [constraint: :unique, constraint_name: "named_name_index"]}], data: #App.Schemas10.Named<>, valid?: false>}
  :check_constraint_changeset
    result: #Ecto.Changeset<action: :insert, changes: %{age: 10, date: ~D[2000-01-10], date_string: "2000-01-10", days_since_2000: 9, name: "Bossie"}, errors: [name: {"has already been taken", [constraint: :unique, constraint_name: "named_name_index"]}], data: #App.Schemas10.Named<>, valid?: false>
< Run :duplicate_name

```

