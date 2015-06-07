A quick note on lein repl


I have a big project that takes some time to load, so sometimes I just lein repl into it to try something.

One weird thing that I found is that sometimes I didn't have any code in a specific namespace.

It seems that that is normal - whenever you have this issue, it's because *you need to load the file beforehand*.

Otherwise, if you {% highlight bash %}lein repl{% endhighlight %} and {% highlight clojure %}(in-ns 'your.namespace){% endhighlight %} into it, a new namespace will be created.

To avoid this, just {% highlight clojure %}(load "your/path/to/yourfile.clj"){% endhighlight %} and *then* {% highlight clojure %}(in-ns 'your.namespace){% endhighlight %} into the namespace.

Source: <http://www.beyondtechnicallycorrect.com/2013/04/14/loading-and-using-namespaces-in-the-clojure-repl/>

