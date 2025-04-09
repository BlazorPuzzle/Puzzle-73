# Puzzle-73 - Referring to Refs

In this example, we are using the `@ref` directive to refer to the `Counter` component. The `@ref` directive allows us to access the component instance directly in our code. This is useful when we want to call methods or access properties of the component from the parent component.

Working with components using the `@ref` keyword can lead to some interesting and unexpected behaviors. In this sample, we've got a few scenarios to look at.  The Counter component is being used here and has some methods and parameters exposed on it.  Here are the various scenarios to inspect:

1. We don't want to bind directly to the Increment property of the component, so we've added a 'SaveIncrement' button that sets that property for us when the value is confirmed in the Increment textbox.
1. The Reset Counter button sets the value of the counter to zero, but it never shows zero on the counter.  How can we update this feature to ensure the value shows on screen?
1. We have some text that we'd like to show in the counter, and a 'ShowText' button that runs a method and should show the text then hide it after 2 seconds.  Why isn't this working?
1. We're doing a lot of pushing on the Counter component using @@ref, is there a way to show that text without calling a method on the component?