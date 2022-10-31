---
permalink: /reagent-reframe-reanimated-and-svg
type: posts
---

# Is this a title or a subtitle?

I have a clojurescript project in which I used reagent and I was happy. But then I wanted to make it a little more complex, I started reading documentation about re-frame and reanimated, and quickly got lost. So here's a post that summarizes all my findings - hopefully it will be useful for you! An alternative that might also help you is just type "lein new re-frame yourapp" and study the code that is created.

# Zero: project start

First, require both libraries in your project.clj:

```
    [timothypratley/reanimated "0.3.0"]
    [re-frame "0.7.0"]
    [reagent "0.5.1"]
```

Oddly enough, re-frame goes against the convention of stating this first instructions, and based on the following initial description, I initially thought this was not a library to require:

```
    re-frame is a pattern for writing SPAs in ClojureScript, using Reagent.
    This repo contains both a description of this pattern and a reference implementation.
```

Now launch your project:

```
    lein figwheel
```

And open your main file in your editor of choice.

(WARNING: We need to define the basic html file)

# First, a minimal reagent app

As you might have read in the reagent documentation, we will be trying to keep a single place for all information (structure and status) of your application, in a reagent atom:

```
    (ns yourapp.core
      (:require [reagent.core :refer [atom]]))

    (defonce app (atom {:title "Hello world!"
                        :color "#123456"}))
```

Great, now we define a couple of components:

```
    (defn custom-title []
      [:h3 (:title @app)])

    (defn colored-div []
      [:div {:style {:background-color (:color @app)}}
        [custom-title]
        "Some more text"])
```

And render the app:

```
    (defn render-components []
      (render-component [colored-div]
                        (js/document.getElementById "elt-1"))
       ; Just show it twice, dont be shy
      (render-component [colored-div]
                        (js/document.getElementById "elt-2")))

    (render-components)
```

Done! But for the moment we are using only reagent. You can modify app and reagent will rerender the appropriate components when needed. On to re-frame...

# Second, let's see what this re-frame thing is all about

When you finish this tutorial I would advise you to read the re-frame documentation, to understand more deeply the following. But let's start with a simple example of it. First, we will need to require some more things in our file:

```
    (ns yourapp.core
      (:require [re-frame.core :refer [dispatch register-handler reg-event-pure subscribe register-sub]])
      (:require [reagent.core :refer [atom]])
      (:require-macros [reagent.ratom :refer [reaction]]))
```

Great.

## Dispatching events

Now, you have seen that reagent talks about "all app state in a single place". Re-frame defines a complementary rule, which is "and there is a unique sequence of events that modify that app state". The way we do this is by dispatching these events and letting subscribers react to it. For this, lets consider an event a vector of values (or any other particular data).

Whenever we would have done the following thing in reagent:

```
    (defn random-color-button []
      [:div  {:class "button"
              :on-click  #(any-random-color-function-that-suits-this-particular-button)}
              "Random color!"])
```

Now re-frame asks us to please do this:

```
    (defn random-color-button []
      [:div  {:class "button"
              :on-click  #(dispatch [:random-color])}
              "Random color!"])
```

Throughout all of our application. That seems easy, and the reason is easy to see: we can then treat events in a coherent way. But how to receive them? Let's see that.

## Handling events

Now we want to consume events and update the status of our app. First, we need to create a function that gets the app and event, and returns a new step of the app:

```
    (defn update-color [app event]
      (assoc-in app [:color] (rand-nth [#000000 #002457 #123456]))
```

With reagent, we would be doing the swap! function call. Here, re-frame does it - and we keep clear. Great for testing!

Now we only need to tell re-frame when to execute this function.

```
    (reg-event-pure :random-color update-color)
```

This should work, now doesnt it?

## Listening to changes (necessary?? Or is this just to speed reagent???)

You might have seen earlier that we required a Reagent macro, called Reaction. You have already used it, unknowingly, through reagent components, but we are going to star using it explicitly. There is further depth in the re-frame docs, but the main idea behind it is that it enables reagent to react to a change in a *value*. This is important: no matter how many times the inside of a reaction is updated, if it evaluates to the same value, reaction won't propagate any change outside.

```
    (defn example-computation [input]
      (identity input))

    (defn color-query [app query]
      (reaction
        (example-computation (1)
          (get-in app query)))) (2)
```

Reactions enable use to access a part of the app (2), compute things on it (1), and react to changes in this part. Of course the access and the computation have the same nature, Im only separating them for the explanations sake. The key of this is that our code wont be recalculating again and again when there are changes in things that are completely unrelated to what we are trying to accomplish - only when something useful for us changes.

Now, this reaction needs to be fed to register-sub:

    (register-sub :color-query color-query)

You will need to redefine the colored-div component with this pattern:

```
  (defn colored-div []
    (let [color (re-frame/subscribe [:color])] ; (1)
      (fn []                                   ; (2)
          [:div {:style {:background-color @color}}
            [custom-title]
            "Some more text"])))
```

You need to do this so you can execute code once per component instantiation (1) and then have the normal inner function that reexecutes each renderization of the component (2). This will only happen if 'color' changes. You can read more about this in [this section][https://github.com/Day8/re-frame/wiki/Creating-Reagent-Components#the-three-ways] of the reagent documentation, but you can keep reading now.

The good part being that you are explicitly defining the subscriptions.
