# Lethargy
Distinguish between scroll events initiated by the user, and those by inertial scrolling.

### Install

### Use

Include `lethargy.min.js` in your document.

    <script src="./lethargy.js"></script>

Create an instance of Lethargy, passing in any options (see below).

    var lethargy = new Lethargy(7, 100, 0.05);

Bind the mousewheel or scroll events and pass the event to Lethargy.

    $(window).bind('mousewheel DOMMouseScroll wheel MozMousePixelScroll', function(e){
        e.preventDefault()
        e.stopPropagation();
        if(lethargy.check(e) !== false) {
            // Do something with the scroll event
        }
    });


`lethargy.check(e)` will return `1` if it is a normal scroll *up* event, `-1` if it is a normal scroll *down* event, and `false` if it is initiated by inertial scrolling.

Lethargy focus on preventing false positives (saying it's a normal scroll event when it wasn't), but tolerates false negatives (saying it's not a normal scroll event when it is).

### Options

All options are optional.

![MacBook Air Trackpad `wheelDelta` curve](http://blog.danyll.com/content/images/2015/05/air.png)

**stability** - Specifies the length of the rolling average. In effect, the larger the value, the smoother the curve will be. This attempts to prevent anomalies from firing 'real' events. Valid values are all positive integers, but in most cases, you would need to stay between `5` and around `30`.

**sensitivity** - Specifies the minimum value for `wheelDelta` for it to register as a valid scroll event. Because the tail of the curve have low `wheelDelta` values, this will stop them from registering as valid scroll events. The unofficial standard `wheelDelta` is `120`, so valid values are positive integers below `120`.

**tolerance** - Prevent small fluctuations from affecting results. Valid values are decimals from `0`, but should ideally be between `0.05` and `0.3`.

### What problem does it solve?

Scroll plugins such as [smartscroll](https://github.com/d4nyll/smartscroll), [jquery-mousewheel](https://github.com/jquery/jquery-mousewheel) or [fullPage.js](http://alvarotrigo.com/fullPage/) work by detecting scroll events and then doing something with them, such as scroll to the next frame. However, inertial scrolling continues to emit scroll events even after the user stopped, and this can often lead to problems, such as scrolling two to three frames when the user only scrolled once.

Below charts the `wheelDelta` values of each scroll action using this [Gist](https://gist.github.com/msimpson/cd7eca7907132c984171) and [demo](http://jsfiddle.net/n7bk6pb9/1/) by [Matthew Simpson](https://github.com/msimpson).

**Desktop Mouse**

![Desktop Mouse `wheelDelta` graph](http://blog.danyll.com/content/images/2015/05/desktop.png)

**MacBook Air Trackpad**

![MacBook Air Trackpad `wheelDelta` curve](http://blog.danyll.com/content/images/2015/05/air.png)

As you can see, the Desktop Mouse emits small 

### How does it work?

Lethargy keeps a record of the last few `wheelDelta` values that is passed through it, it will then work out whether these values are decreasing (decaying), and if so, concludes that the scroll event originated from inertial scrolling, and not directly from the user.

### Limitations and Future Development

Not all trackpads work the same, some trackpads do not have a decaying `wheelDelta` value, and so at the moment, there's no way for Lethargy to distinguish between these events.

We can experiment with the time interval in which these events are emitted, but that will require a lot of effort and won't start until other areas are polished.

**ASUS Trackpad**

![ASUS Trackpad](http://blog.danyll.com/content/images/2015/05/asus.png)