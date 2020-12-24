---
title:  "Regular and Retina Sprites with Compass"
date: 2014-01-31
---

So you use compass to build sprites, but now you want everything to have 2x graphics? This mixin should get you up and running.

Bear in mind that you should make sure all images have even dimensions. If you have a 115x115px image in the retina size, you should bump it up to 116x116px and have a regular sized image of 58x58px. This is a slight modification of [this gist](https://gist.github.com/thulstrup/2140082) with the fix proposed by [rstacruz](https://github.com/rstacruz) in order to avoid hard coding the sprite width manually.

{{<highlight scss>}}
$sprite: sprite-map("sprite-sprite/*.png");
$sprite-retina: sprite-map("sprite-sprite-retina/*.png");

@mixin sprite-background($name) {
  $width: image-width(sprite-file($sprite, $name));
  $height: image-height(sprite-file($sprite, $name));

  background-image: sprite-url($sprite);
  background-position: sprite-position($sprite, $name);
  background-repeat: no-repeat;
  width: $width;
  height: $height;

  $background-width-size: image-width(sprite-path($sprite));

  @media (-webkit-min-device-pixel-ratio: 2), (-o-min-device-pixel-ratio: 3/2), (min-device-pixel-ratio: 2) {
    // Workaround for https://gist.github.com/2140082
    @if (sprite-position($sprite, $name) != sprite-position($sprite-retina, $name)) {
      $ypos: round(nth(sprite-position($sprite-retina, $name), 2) / 2);
      background-position: 0 $ypos;
    }

    @include background-size($background-width-size auto);
    background-image: sprite-url($sprite-retina);
  }
}
{{</highlight>}}

## Usage

Just use the filename without the extension (all files should be .png extension). You'll need to give your element `display: block;` or `display: inline-block;` in order for it to work. You could add this to the mixin if you wanted.

{{<highlight css>}}
.someclass {
  @include sprite-background("file");
}
{{</highlight>}}
