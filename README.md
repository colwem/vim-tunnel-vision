# vim-tunnel-vision
Easymotion like motion plugin for vim

Easymotion is very useful plugin but it has some serious flaws.  

1. For some reason it's slow.  Especially on larger files when using something like ,,w or ,,b
2. To choose your target you need to type a new overlayed sequence of characters.  It takes precious time and energy for your brain to read and respond to this new input.

I don't know the cause of the first problem and so I don't know how to fix it but I have an idea to solve the second problem and that's what this project is for.

First here is the example text I will use

```

var blah = function(blam) {
  var bloop = function(blame) {
    var fundamental = blam;
    var fact = blam;
  };
};
```
Lets say the cursor is at ^ and you want to go to &
```

var^ blah = function(blam) {
  var bloop = function(blame) {
    &var fundamental = blam;
    var fact = blam;
  };
};
```
You type `,/` and it puts you in enters you into `tunnel-vision` mode on the command line.  Now you type `v`.  The buffer is updated by highlighting next v like this:


```

var blah = function(blam) {
  _v_ar bloop = function(blame) {
    var fundamental = blam;
    var fact = blam;
  };
};
```
But that's not the v you're looking for so you type `a` and it moves the highlight to:

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

With easymotion we would have had to type only 2 chars but they would have been 2 random and unexpected chars.  This way all you have to do is type what you see.  If we had done regular /<pattern> then it would have found the first `var` after which we would have either had to type and additional ` fa` or `<cr>nn` not only is that more typing but in the second case it would require us to take our eyes off our target which ultimately distracts us from what we were planning to do once we got there.

## Unique Post Text

I'm sure you're thinking, ok, but what if there are 4 or 5 or 10 `var`s? To solve that problem use easymotions overlay technique but we use it better.  If you're still typing after the first 2 chars then we start overlaying the next char or next two chars with unique chars.  You are still free to keep typing chars from the original text but as soon as your mind registers the overlay chars you can type them and you immediately exit `tunnel-vision` mode and the cursor moves to that location.

### Example

Now the cursor is at ^ again and we're trying to go to the same location at &
```
^
var blah = function(blam) {
  var bloop = function(blame) {
    var fundamental = blam;
    &var fact = blam;
  };
};
```



