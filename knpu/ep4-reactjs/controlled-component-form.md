# Controlled Component Form

With *controlled* components, the value for *each* field in your form needs to be
set to state. And then, you need to make sure to *update* that state whenever the
field changes. But once you do this, everything else is super nice! The input
automatically renders with the correct value, and it's dead-simple to read that
state and use it in other places.

In `RepLogCreator`, we did *not* use this strategy. Instead, we took advantage
of `refs` to access the DOM elements directly, and then read the values from there.

To *really* compare these two approaches, let's see how it would look to use
"controlled components" inside of `RepLogCreator`. Then, later, I'll give you *my*
clear recommendation on when to use each.

Copy `RepLogCreator` and create a *new* file: `RepLoyCreatorControlledComponents.js`.
Next, in `RepLogs`, copy the import statement, comment it out and, instead, import
`RepLogCreator` from this new file.

## Adding the new State

Perfect! Because our form has two fields - the select & the input - we need two
new pieces of state. On top, add `selectedItemId` set to empty quotes and
`quantityValue` set to 0. Delete the old refs stuff.

In `render()` destructure these out of state, and use them below: instead of `ref=`,
use `value={selectedItemId}`. On the input, the same thing: `value={quantityValue}`.
Oh, this is cool: when you use a controlled component with a select element, you
add the `value=` to the *select* element itself! That's not how HTML works. Normally,
you need to add a `selected` attribute to the correct option. But in React, you
can pretend like the select itself holds the value. It's pretty nice.

## Adding the Handler Functions

As *soon* as bind the value of a field to state, you *must* add an onChange handler.
Above, create `handleSelectedItemChange()` with an event argument. Inside, all we
need to do is set state: `this.setState()` with `selectedItemId` set to
`event.target.value`. `event.target` gives us the *select* DOM element, and then
we can use `.value`. We don't need to read the `selectedIndex` like before.

Copy this function, paste, and call it `handleQuantityInputChange`. This time, update
`quantityValue`... but the `event.target.value` part stays the same.

Before we use these functions below, head up to the constructor and bind both of
them to this.

*Finally*, head back down to render to hook up the handlers:
`onChange={this.handleSelectedItemChange}` and for the input,
`onChange={this.handleQuantityInputChange}`.

Ok - the controlled components are setup! Move over, refresh, inspect element to
find the text element, click it, and *then* go over to React. The dev tools show
us this *exact* element in React... which is nice because we can scroll up to
find `RepLogCreator` and see its state!

Select a new item. New state! Change the input. New state again! 

## Using the new State

The hard work is *now* behind us. Find `handleFormSubmit()`. Instead of looking
at the DOM elements themselves... we can just read the state! On top, destructure
what we need: `const { selectedItemId, quantityValue } = this.state`. Delete the
old refs stuff.

then, in the if statement, it's just `if quantityValue`. That's nice.

Use that again below for `onAddRepLog`. For the first argument, put a TODO just
for a minute. Then, at the bottom, *clearing* the form fields is also easier:
delete the old code, then re-set the `selectedItemId` and `quantityValue` state
back to their original values.

Ok, back to that `onAddRepLog()` call. The first argument is the item *label*:
that's the visual part of the option, not its value. But our state - `selectedItemId`
*is* the value. We're going to change this to use the value later, once we introduce
some AJAX calls. But, thanks to the `itemOptions` property we created earlier,
can use the option id to find the text. I'll create a new `itemLabel` variable
and paste in some code. This is *super* not important: it *finds* the item by
id, and, at the end. we call `.text` to get that property.

Use that below: `itemLabel`.

And... I think we're ready! Move over and refresh. Lift our big fat cat 25 times.
We got it! Try some coffee while we're at it.

## Controlled Versus Uncontrolled Components

Ok, let's finally judge these two approaches. The old `RepLogCreator` uses the
first strategy, called "Uncontrolled Components". It's about 100 lines long.
`RepLogCreatorControlledComponents` is a bit longer: 116 lines. And that reflects
the fact that controlled components require a bit more setup: each field needs its
own state *and* a handler to update state. Sure, there *are* some clever ways to make
one handler that can update everything. But, the point is, the added state and
handlers means a bit more setup & complexity. On the bright side, when you need
to read those values, it's super easy! Just read the staet. Oh, even this is too
much code: I forgot to use the local `selectedItemId` variable.

Controlled components are the React officially-recommended approach to forms. However,
because of the added complexity & state, we recommend using "uncontrolled components"
and `refs` instead... most of the time. But, this is subjective, and you're going
to be fine either way. No decision is permanent, and switching from uncontrolled
components to controlled is easy.

So, when *do* we recommend *controlled* components? The biggest time is when you
want to do render something as *soon* as a field changes - not *just* on submit.
For example, if you wanted to validate a field as the user is typing, disable or
enable the submit button as the user is typing or reformat a field - like a phone
number field... once again.. as the user is typing. If you're not in one of these
situations, you can totally still use controlled components! But we prefer to not
use them.

Oh, and remember one other downside to controlled component is that it *does*
require your component to have state. And so, if you dumb component is a function,
like `RepLogs`, you'll need to refactor it to a class. No huge deal - just something
to think about.

In `RepLogs`, let's change the import to use our original component.