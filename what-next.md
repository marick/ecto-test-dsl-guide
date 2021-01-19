# What next?



{% hint style="info" %}
You can find instructions for setting up the library at its [Github page](https://github.com/marick/transformer_test_support). For `tts_ecto_example`, note that a server is started in [`test_helper.exs`](https://github.com/marick/tts_ecto_example/blob/main/test/test_helper.exs)and the [`mix.exs`](https://github.com/marick/tts_ecto_example/blob/main/mix.exs#L24) file instructs the compiler to compile`.ex` files within the`test` subdirectory. \(Example files end in`.ex.)`
{% endhint %}




The library is built to be "pluggable", using **variants**. However, the first two variants come from a Phoenix/Ecto [app of mine](https://github.com/marick/crit19/). Because of that, it makes more sense to explain the **Ecto Classic variant** \(the simpler of the two\) first, rather than start with an explanation that applies to any possible variant.

{% hint style="info" %}
I haven't started extracting the second variant, the Ecto View Model variant, from the app's original version of this library.
{% endhint %}

