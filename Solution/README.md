# Puzzle #73 - Referring to Refs

Jeff and Carl want to know why these Blazor component references aren't behaving the way they expected.

YouTube Video: https://youtu.be/IbTz67F-thE

Blazor Puzzle Home Page: https://blazorpuzzle.com

## The Challenge

In this example, we are using the `@ref` directive to refer to the `Counter` component. The `@ref` directive allows us to access the component instance directly in our code. This is useful when we want to call methods or access properties of the component from the parent component.

Working with components using the `@ref` keyword can lead to some interesting and unexpected behaviors. In this sample, we've got a few scenarios to look at.  The Counter component is being used here and has some methods and parameters exposed on it.  Here are the various scenarios to inspect:

1. We don't want to bind directly to the Increment property of the component, so we've added a 'SaveIncrement' button that sets that property for us when the value is confirmed in the Increment textbox.
1. The Reset Counter button sets the value of the counter to zero, but it never shows zero on the counter.  How can we update this feature to ensure the value shows on screen?
1. We have some text that we'd like to show in the counter, and a 'ShowText' button that runs a method and should show the text then hide it after 2 seconds.  Why isn't this working?
1. We're doing a lot of pushing on the Counter component using @ref, is there a way to show that text without calling a method on the component?

## The Solution

There are three solutions to three different issues outlined in items 2, 3, and 4 above. Let's take them one at a time.

* The Reset Counter button sets the value of the counter to zero, but it never shows zero on the counter.  How can we update this feature to ensure the value shows on screen?

The solution to this is to add a new ResetCounter method to the component:

```c#
public void ResetCounter(int newValue = 0)
{
    CurrentCount = newValue;
    StateHasChanged();
}
```

In the main app, we can call it when the Reset button is clicked:

```c#
void ResetCounter()
{
    MyCounter.ResetCounter(0);
}
```

If you set a parameter value like a property, unless you have a setter that calls StateHasChanged, it won't update the UI.

* We have some text that we'd like to show in the counter, and a 'ShowText' button that runs a method and should show the text then hide it after 2 seconds.  Why isn't this working?

This is also a problem of calling StateHasChanged(). Here's the updated ShowText method. 

```c#
public void ShowText()
{
    TextIsVisible = true;
    StateHasChanged();
    Task.Delay(2000).ContinueWith(_ =>
    {
        TextIsVisible = false;
        InvokeAsync(StateHasChanged);
    });
}
```

Note that in the ContinueWith() method that executes after 2 seconds, we have to call this:

```c#
InvokeAsync(StateHasChanged);
```

InvokeAsync should be used whenever you need to call UI code from a non UI-thread.

* We're doing a lot of pushing on the Counter component using @ref, is there a way to show that text without calling a method on the component?

Yes! We can bind the TextVisible property to our local HomeTextVisible variable like so:

```xml
<Counter @ref="MyCounter" TextVisible="HomeTextVisible" ></Counter>
```

Now we can change HomeTextVisible to true to show the text.

```c#
void ShowText2()
{
    HomeTextVisible = true;
}
```

Let's take a look at the TextVisible parameter.

```c#
[Parameter]
public bool TextVisible
{
    get { return TextIsVisible; }
    set
    {
        if (value)
        {
            ShowText();
        } else {
            TextIsVisible = false;
        }
    }
}
```

Note that the setter calls ShowText() when the value is set to true.

Boom!