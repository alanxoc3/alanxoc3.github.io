---
layout: post
title:  "Efficient pico-8 outline."
categories: blog
tags: [pico-8, tutorial]
caption: How to make your sprites cool and fast!
---
_I originally put this on the [lexaloffle
website](https://www.lexaloffle.com/bbs/?tid=32996), but I copied it here to
keep my things in one place. Future updates to the article will appear on this
blog, not the lexaloffle website._

So I was working on making an improved version of "The Story of Zeldo" and
realized a few weeks ago that outlining my sprites was taking up more CPU
cycles than I wanted it to. I was using the outlining function where the
palette is cleared to a color, then 8 sprites are drawn around the actual
sprite to produce an outline effect as shown
[here](https://gist.github.com/Liquidream/1b419261dc324708f008f24ee6d13d7b). I
decided to change all the sprites that needed outlines to 10x10 instead of 8x8.

{% include img.html src="/res/ss/efficient_outline.png" %}

You can see the 10x10 sprites at the bottom right of the graphic. Getting rid
of the outlining function like this improved my CPU, but wasted sprite space. I
later needed more sprite space, but didn't want to take a CPU hit. Then I
thought, what if I use rectangles to draw the outline of sprites instead of
drawing actual sprites for the outline?

So that's what I did and I thought I would share. There are two parts to this
process. The first is generating the rectangles for all your sprites and
caching them at the beginning of the game.

{% highlight lua %}
-- 175 tokens
g_out_cache = {}
function init_out_cache(s_beg, s_end)
   for sind=s_beg,s_end do
      local bounds, is_bkgd = {}, function(x, y)
         return mid(0,x,7) == x and sget(x+sind*8%128, y+flr(sind/16)*8) != 0
      end

      local calc_bound = function(x)
         local top, bot

         for i=0,7 do
            top, bot = top or is_bkgd(x,i) and i-1, bot or is_bkgd(x,7-i) and 8-i
         end

         return {top=top or 10, bot=bot or 0}
      end

      g_out_cache[sind] = {}
      for i=0xffff,8 do
         -- prev, cur, next
         local p, c, n = calc_bound(i-1), calc_bound(i), calc_bound(i+1)
         local top, bot = min(min(p.top, c.top), n.top), max(max(p.bot, c.bot), n.bot)

         if bot >= top then
            add(g_out_cache[sind], {x1=i,y1=top,x2=i,y2=bot})
         end
      end
   end
end
{% endhighlight %}

`init_out_cache` should be called on startup in the _init function, passing in
the start and end of the sprites you want outlined. Then the other part to
outlining sprites is to actually outline them!

{% highlight lua %}
-- 84 tokens
function spr_out(sind, x, y, sw, sh, xf, yf, col)
   local ox, x_mult, oy, y_mult = x, 1, y, 1
   if xf then ox, x_mult = 7+x, 0xffff end
   if yf then oy, y_mult = 7+y, 0xffff end

   foreach(g_out_cache[sind], function(r)
      rectfill(
         ox+x_mult*r.x1,
         oy+y_mult*r.y1,
         ox+x_mult*r.x2,
         oy+y_mult*r.y2,
         col)
   end)

   spr(sind, x, y, sw, sh, xf, yf)
end
{% endhighlight %}

This function just takes the rectangle data created from the init function and
draws them as rectangles. A little bit extra logic is needed for xf and yf to
work. Using these snippets, CPU efficiency of outlines is better than the old
method, but worse than having no outline at all.

Although I thought this was cool, there are four at least four drawbacks to
using this method:

- The token count is much higher than the old method (259 tokens instead of 59
  tokens).
- The init function is not very efficient, because I went for token count on
  that instead of efficiency.
- Currently sw and sh are not implemented with this method. So it only works on
  8x8 sprites.
- If a sprite is hollow, then the entire hollow region will be filled with the
  outline color as seen below.

{% include img.html src="/res/ss/efficient_outline_moon.png" %}

The green arrow is the old method, the red arrow is the new function.

Here is a demo of this sprite outline function in action!

{% include pico8.html name="efficient_outline" %}

As you can see, drawing 56 outlined sprites with this function saves over 15%
of CPU usage compared with the old method!

As far as improvements go, here are a few areas this code could be improved on:
- There are only up to 10 rectangles drawn for each 8x8 sprite, but some
  sprites could have less rectangles if extra code is added to check for
  duplicate rectangles.
- Making variable sprite height and width, instead of just 8x8 (aka. sw and
  sh).

I am going to be using this in my
[Zeldo](https://twitter.com/alanxoc3/status/1086413617497423872) game in the
future, but I will probably pre-process all the outlines I need and just store
the rectangle data to save on token count. Comment if you have improvements for
this, if you found this useful, or if you just have something to say. I might
do another post like this in the future if I find people read this one :D.

