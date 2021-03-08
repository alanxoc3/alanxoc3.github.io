---
layout: post
title: More efficient sprite outlines.
caption: Improving cpu efficiency in pico-8.
tags: [pico-8, tutorial]
modified: 2020-02-08
---
"How do I make a sprite have an outline in Pico-8?" I wondered. That question
lead me to find this
[algorithm](https://gist.github.com/Liquidream/1b419261dc324708f008f24ee6d13d7b):

{% highlight text %}
- Change the color palette to one color.
- Draw the sprite 8 times at these positions:
   - x-1 , y-1
   - x   , y-1
   - x+1 , y-1
   - x+1 , y
   - x+1 , y+1
   - x   , y+1
   - x-1 , y+1
   - x-1 , y
- Reset the palette color.
- Draw the sprite one last time at `x,y`.
{% endhighlight %}

Months passed by, and I was content with that approach. Until I ran into CPU
efficiency concerns. I need to do something differently. So I came up with a
different algorithm using sprite scaling:

{% highlight text %}
- Change the color palette to one color.
- Draw the sprite about 2-4 pixels larger than normal.
- Reset the palette color.
- Draw the sprite again, but with a normal size.
{% endhighlight %}

While that algorithm is much more efficient and kind-of cool, the outline looks
really ugly. It was at this point that I decided to just draw sprite outlines
in the sprite sheet itself. It resulted in some 10x10 sprites and looked like
this:

{% include img.html src="/res/img/efficient_outline.png" %}

***The upsides:***
- My program is more efficient.
- I'm using less tokens.

***The downsides:***
- I'm wasting sprite space by keeping the outlines in the sprite sheet.

Eventually, I wanted to have more sprite space. So I went back to thinking of
algorithms, and I was able to come up with a new algorithm using rectangles!
Here it is:

{% highlight text %}
- For each column in the sprite.
   - Save the x & y for both the top & bottom pixels.
- Cache all those coordinates for future use.
- Draw a rectangle for each column.
- Draw the sprite.
{% endhighlight %}

Yeah, I was pretty proud of that idea. The code ended up being a bit more
complex due to edge cases, but it isn't too bad. You can see it on the
[lexaloffle post](https://www.lexaloffle.com/bbs/?tid=32996). You can also try
out the demo here:

{% include pico8.html name="efficient_outline" %}

This algorithm uses 50% less CPU than the first algorithm shared. Besides using
more tokens, there is another catch. If you study the algorithm, you'll notice
that crescent and hollow shaped objects will get filled instead of outlined.
The green arrow below would be the first algorithm, while the red arrow would
be this rectangle algorithm.

{% include img.html src="/res/img/efficient_outline_moon.png" %}

I am going to be using this in my
[Zeldo](https://twitter.com/alanxoc3/status/1086413617497423872) game in the
future, but I will probably go a step further and pre-process all the outlines,
so I don't have to waste tokens for the code generating the outlines. That's
all I wanted to share.
