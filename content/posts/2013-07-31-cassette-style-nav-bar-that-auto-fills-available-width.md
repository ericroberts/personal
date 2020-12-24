---
title:  "Cassette Style Nav Bar That Auto-Fills Available Width"
date:   2013-07-31
---

<style type="text/css">
.cassette {
  list-style-type: none !important;
  padding: 0;
  margin: 0 0 20px 0 !important;
}

.cassette:after {
  content: "";
  display: block;
  clear: both;
}

.cassette li {
  float: left;
}

.cassette li:first-child > * { border-radius: 2px 0 0 2px; }
.cassette li:last-child > * { border-radius: 0 2px 2px 0; }
.cassette .cassette-filler {
  float: none;
  overflow: hidden;
  padding: 0 3px 3px 0;

  position: relative;
  z-index: 2;
}

.cassette-item,
.cassette-filler span {
  box-shadow: 1px 1px 0 #1b7fc2, 2px 2px 0 #1b7fc2, 3px 3px 0 #1b7fc2 !important;
  display: block !important;
  height: 40px !important;
  padding: 0 15px !important;
  border-left: 1px solid #41a5e8 !important;
  border-right: 1px solid #278bce !important;

  background: #3498db !important;

  color: #fff !important;
  line-height: 40px !important;
}

.cassette-item:hover {
  background: #4eb2f5 !important;

  color: #fff !important;
  text-shadow: 1px 1px 0 #3498db, 2px 2px 0 #3498db, 3px 3px 0 #3498db !important;
}

.cassette-item:active,
.cassette-item.is-active {
  box-shadow: 1px 1px 0 #1b7fc2 !important;
  margin: 2px -2px -2px 2px !important;
}

</style>
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

{{<highlight html>}}
<ul class="cassette">
  <li><a href="#" class="cassette-item">Do</a></li>
  <li><a href="#" class="cassette-item">Cool</a></li>
  <li><a href="#" class="cassette-item">Things</a></li>
  <li><a href="#" class="cassette-item">Like</a></li>
  <li><a href="#" class="cassette-item is-active">This</a></li>
  <li class="cassette-filler"><span></span></li>
</ul>
{{</highlight>}}

The trick is to float each list item left, except for the last one. On the last one, you need to set overflow to hidden. Here's all that's required for the magic auto-fill width behaviour.

{{<highlight css>}}
.cassette li {
  float: left;
}

.cassette li.cassette-filler {
  float: none;
  overflow: hidden;
}
{{</highlight>}}

Voila! Auto filled width. As for why this works, I have no idea. I learned this magical trick on [Stack Overflow](http://stackoverflow.com/questions/6572040/make-divs-auto-fill-space).

Here's the whole cassette.scss file, in case you want to copy and use it:

{{<highlight scss>}}
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
{{</highlight>}}
