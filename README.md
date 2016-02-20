# vim-tunnel-vision

Easymotion like motion plugin for vim

Easily move cursor to anywhere on the page without having to move your eyes off
your target or think at all about the motion.  In other words, it allows you to
have tunnel vision. 

## Problem

Easymotion is very useful plugin but it has some serious flaws.  

1. For some reason it's slow.  Especially on larger files when using something
like ,,w or ,,b 2. To choose your target you need to type a new overlayed
sequence of characters.  It takes precious time and energy for your brain to
read and respond to this new input.

I don't know the cause of the first problem and so I don't know how to fix it
but I have an idea to solve the second problem and that's what this project is
for.

## Progressive incremental search

Incremental search is great but if there are many occurances of your search
string on the page then it will halt at the first instance it finds until you
type so may characters that the location you're looking for is finally unique.
What we want is for it to intelligently assume that if you're still typing then
we're not at the right place yet.  In other words: after every character typed
jump to the next match.

### Example

First here is the example text I will use
```

var blah = function(blam) {
  var bloop = function(blame) {
    var fundamental = blam;
    var fact = blam;
  };
};
```
Legend: 
^ represents the cursor 
& represents the target 
\_<chars>\_ represents highlighted text 
```

var^ blah = function(blam) {
  var bloop = function(blame) {
    &var fundamental = blam;
    var fact = blam;
  };
};
```
You type `,/` and it puts you in enters you into tunnel vision mode which we'll call `tv-mode` mode on the
command line.  Now you type `v`.  The buffer is updated by highlighting next
v like this:


```

var blah = function(blam) {
  _v_ar bloop = function(blame) {
    var fundamental = blam;
    var fact = blam;
  };
};
```
But that's not the v you're looking for so you type `a` and it moves the
highlight to:
```

var blah = function(blam) {
  var bloop = function(blame) {
    _va_r fundamental = blam;
    var fact = blam;
  };
};
```

Still not there so you type `r` and it moves the highlight again to:


```
var blah = function(blam) {
  var bloop = function(blame) {
    var fundamental = blam;
    _var_ fact = blam;
  };
};
```
Now we're where we want to be so we hit enter.

With easymotion we would have had to type only 2 chars but they would have been
2 random and unexpected chars.  This way all you have to do is type what you
see.  If we had done regular /<pattern> then it would have found the first
`var` after which we would have either had to type and additional ` fa` or
`<cr>nn` not only is that more typing but in the second case it would require
us to take our eyes off our target which ultimately distracts us from what we
were planning to do once we got there.

## Unique Post Text

I'm sure you're thinking, ok, but what if there are 4 or 5 or 10 `var`s?
There's still a lot of typing to do isn't there? To solve that problem use
easymotions overlay technique but we use it better.  If you're still typing
after the first 2 chars then we start overlaying the next char or next two
chars with unique chars.  You are still free to keep typing chars from the
original text but as soon as your mind registers the overlay chars you can type
them and you immediately exit `tunnel-vision` mode and the cursor moves to that
location.

### Example

The cursor position has changed for this example but the target remains the same
```
^
var blah = function(blam) {
  var bloop = function(blame) {
    var fundamental = blam;
    &var fact = blam;
  };
};
```
now we type `va` as before.  The new state is 
```

var blah = function(blam) {
  _va_f bloop = function(blame) {
    vad fundamental = blam;
    &vaj fact = blam;
  };
};
```
Notice that the `r`'s in each `var` have changed to a different letter.  That
letter uniquely identifies that instance of `var`.  Now lets say the user
doesn't instantly notice the new letter and procedes to type the `r` anyways.
The new state is now

```
^
var blah = function(blam) {
  var bloop = function(blame) {
    _var_d fundamental = blam;
    &varj fact = blam;
  };
};
```
So the identifying letter hasn't changed, it's just been moved right one char.
Now the user can either type space or `j` and they're there.  If they type `j`
then they exit `tv-mode` immediately.  If they type space then they must type
enter otherwise we will just continue trying to match what they type.

## Most likely first

Further optimization can be made by reasoning about what the most likely target
actually is.  Since the goal of this plugin is to allow the user to stare at
his target and ignore everything else there's no need for the progression to be
in order. 

Actually going in order makes little sense.  If the target in the examples
above were the first var then the user could have easily just typed `j` and
they would have been done so why would we start with the first.  It makes a lot
more sense to start with the most difficult to reach target.  If ordered it
that way then we probably would have hit the target in one key. 

We can also reason that you are more likely to be searching for the start of
a word than in the middle so prioritize matches that start with a word
boundary.

Another trick is that if there are multiple matches with multiple "branches"
prioritize all matches that are part of the most common branch.  For instance
in the above examples if we were looking for the third `blam` as such:
```
^
var blah = function(blam) {
  var bloop = function(blame) {
    var fundamental = blam;
    var fact = &blam;
  };
};
```
we would start with `bl` and that would match 5 `bla`s and 1 `blo`.  So that's
2 different branches that follow from the `bl` initial.  The `bla` branch is
much more comean than the `blo` branch and so we reason it would have been more
difficult to reach through a traditional `/` and so we prioritize the `bla`
matches over the `blo` matches. if the user then types an `a` then we follow
the same reasoning to prioritize the 4 `blam`s over the 1 `blah`.


## Conclusion

I would love for this to exist but I don't have any experience with vimscript
so I would need some help or for someone else to do it.
