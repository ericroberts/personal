---
layout: post
title:  "Cassette Style Nav Bar That Auto-Fills Available Width"
date:   2013-07-31
categories: css, html
---

<script src="//ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
<script>
$(function() {
  $('.cassette-item').click(function(event) {
    event.preventDefault();
    var link = $(this);
    link.closest('ul').find('.cassette-item').removeClass('is-active');
    link.addClass('is-active');
  });
});
</script>
<ul class="cassette">
  <li><a href="#" class="cassette-item">Do</a></li>
  <li><a href="#" class="cassette-item">Cool</a></li>
  <li><a href="#" class="cassette-item">Things</a></li>
  <li><a href="#" class="cassette-item">Like</a></li>
  <li><a href="#" class="cassette-item is-active">This</a></li>
  <li class="cassette-filler"><span></span></li>
</ul>

Recently I was trying to make a nav bar that had "pushed-in" buttons <small>(like cassette tape decks)</small>, but I was running into a problem. I needed the nav bar to fill the full width, but I couldn't set a background on it because it would mess with the display of the pushed in state. Then I found out that `overflow: hidden;` does magical things that I can't explain.

Here's the HTML for the above navbar.

{% highlight html linenos %}
<ul class="cassette">
  <li><a href="#" class="cassette-item">Do</a></li>
  <li><a href="#" class="cassette-item">Cool</a></li>
  <li><a href="#" class="cassette-item">Things</a></li>
  <li><a href="#" class="cassette-item">Like</a></li>
  <li><a href="#" class="cassette-item is-active">This</a></li>
  <li class="cassette-filler"><span></span></li>
</ul>
{% endhighlight %}

The trick is to float each list item left, except for the last one. On the last one, you need to set overflow to hidden. Here's all that's required for the magic auto-fill width behaviour.

{% highlight css linenos %}
.cassette li {
  float: left;
}

.cassette li.cassette-filler {
  float: none;
  overflow: hidden;
}
{% endhighlight %}

Voila! Auto filled width. As for why this works, I have no idea. I learned this magical trick on [Stack Overflow](http://stackoverflow.com/questions/6572040/make-divs-auto-fill-space).

Here's the whole cassette.scss file, in case you want to copy and use it:

{% highlight scss linenos %}
$color: #3498db;

.cassette {
  list-style-type: none;
  padding: 0;
  margin: 0 0 20px 0;

  li {
    float: left;

    &:first-child > * { border-radius: 2px 0 0 2px; }
    &:last-child > * { border-radius: 0 2px 2px 0; }
    &.cassette-filler {
      float: none;
      overflow: hidden;
      padding: 0 3px 3px 0;

      position: relative;
      z-index: 2;
    }
  }
}

%cassette-item {
  $bg: $color;
  $shadow: darken($color, 10);

  @include box-shadow(1px 1px 0 $shadow, 2px 2px 0 $shadow, 3px 3px 0 $shadow);

  display: block;
  height: 40px;
  padding: 0 15px;
  border-left: 1px solid lighten($color, 5);
  border-right: 1px solid darken($color, 5);

  background: $color;

  color: #fff;
  line-height: 40px;
}

.cassette-item {
  @extend %cassette-item;

  &:hover {
    background: lighten($color, 10);

    color: #fff;
    text-shadow:
      1px 1px 0 $color,
      2px 2px 0 $color,
      3px 3px 0 $color;
  }
  &:active, &.is-active {
    @include box-shadow(1px 1px 0 darken($color, 10));
    margin: 2px -2px -2px 2px;
  }
}

.cassette-filler {
  span { @extend %cassette-item; }
}
{% endhighlight %}
