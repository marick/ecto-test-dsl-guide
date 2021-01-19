# Describing an example \(EctoClassic.Insert\)

A typical example is a keyword argument to the `workflow` function, and looks like this:

```elixir
workflow(                                         :success,
  bossie: [
    params(name: "Bossie", age: 10, date_string: "2000-01-10"),
    changeset(changes: [
       name: "Bossie", age: 10, date_string: "2000-01-10",
       date: ~D[2000-01-10], days_since_2000: 9])
  ])
```

The key is the name of the example, and the value is a list. The following pages describe what can be in that list.

{% hint style="info" %}
Strictly, the example's defining list is a keyword list. It's created with functions like `params` and `changeset` because I think that makes the structure more clear, especially given syntax highlighting. `params`, then, really just expands into `{:params, [name: "Bossie", ...]}`. 
{% endhint %}



