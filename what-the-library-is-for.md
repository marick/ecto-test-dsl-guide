---
description: Some requirements. You may want to skip to the demonstration on the next page.
---

# What the library is for

I intend `transformer_test_support`to provide a **declarative language** for checkable examples \(that is, **tests**\) of code that transforms one complex structure into another, especially transformations with **side effects,** most commonly **Ecto database operations that back HTML forms** handled via **Phoenix.**

The language relies on the fact that most transformations are pretty rote. Lots and lots of Ecto transformations follow the same steps:

1. Accept a form, producing a changeset.
2. If the changeset is invalid, ask the user to try again using `error` fields in the changeset.
3. Otherwise, insert or update the database.
4. ... which may fail with database constraint errors, which should also be reported to the user or client in `error` fields. 

Moreover, the calculations that produce changeset fields are also most often rote. For example, the changeset may contain fields that are trivial transformations of the input \(like casting `"232"` into the integer `232`\), custom transformations \(like converting two strings representing dates into a Postgres interval value\), or refer to other structured data \(via associations.foreign keys\).

A lot of this rote code _could_ be easier to test than to write – rote code generally is – but it's usually not, often because of fiddly setup. This library aims to achieve that ease of test-writing, while also **supporting tests different enough that they shouldn't be declarative**. That is, the library should do the boring part of such tests, so you have to write imperative test code only for the interesting part. 

There are other requirements: 

1. The library should **fit into a test-driven design workflow.** 
2. **It should use the ExUnit framework as much as possible.** For example, individual examples are run as ExUnit `tests`, even though they don't use the notation, and individual failures as produced using the ExUnit reporting mechanism.
3. **It should produce error messages as good as ExUnit.** \(Wrappers of test frameworks often don't.\)
4. Like the Elixir compiler, the **example parser should produce good error messages.** A confusing error message is a bug.
5. It should be easy to **support different rote transformations**. For example, my personal form handling code doesn't follow the four step workflow shown above. Instead of a single `schema` structure, I use two: one outward-facing, tailored to the form, and one inward-facing, tailored to the database layout. So the same library supports both **variants**.
6. While leaning on **convention over configuration**, it should be possible to **override conventions** when \(1\) the overrides are fairly common and \(2\) overriding can be done without too much complication \(otherwise, it's better to fall back to imperative test code\). For example, it should be easy to say "Use this function instead of `Repo.insert` to insert new database records."



