# [WORK IN PROGRESS] Web Fonts

Web fonts play a great role on web design today. Thanks to the `@font-face` rule, we are able to use any custom font we want. It took us so long to get there. For a long time we were limited to a set of fonts that we used to call *web safe* fonts. Surprisingly the reason wasn't a technical one.

The problem with the fonts is that they are really expensive. It takes a designer a lot of practice and patience to produce a typeface. Back in 1998 the CSS Working Group proposed the `@font-face` rule to allow the usage of custom fonts on the web. IE4 implemented that feature but it didn't succeed. By the end, who would risk and put a font that costs hundreds or thousands of dollars on the web so that others can download it and use it for free?

In 2008 `@font-face` was implemented by Safari and Firefox and the road was being paved for custom fonts to come to the browsers. In 2009, [Typekit](https://typekit.com/) was founded (acquired by Adobe later on) and started offering high quality fonts with appropriate licenses for the usage on the web. Typekit published a post on their blog called [Serving and Protecting Fonts on the Web](https://blog.typekit.com/2009/07/21/serving-and-protecting-fonts-on-the-web/) explaining how they protect their fonts from being downloaded using different techniques. The growth of Typekit opened the door for other services to come to the surface and people started to get used to custom fonts on the internet. Time after time, the fear of piracy started to shrink. In 2010 Google launched [Google Fonts](https://fonts.google.com/) which now hosts more than 800 high quality font for anyone to use.

These services were great as they offered new elegant and shiny fonts for everyone to use but they didn't let you use your own custom fonts on your website. Using a custom font on the web isn't as easy as installing a font on your machine, you will have to convert it first to an appropriate format that the browser can understand. In 2010 a popular service for hosting fonts called [FontSquirrel](http://fontsquirrel.com) (originally founded in 2009) started offering a free service that allows anyone to upload a custom font and it convert it to what was commonly became known as a *font-face kit*.

## `@font-face`

We mentioned in the earlier section that the tools we use to generate the font-face kits generate different font formats. Typically, the output of the generated kit should be something similar to:

```css
@font-face {
  font-family: 'Custom Font';
  font-style: normal;
  font-weight: 400;
  src: local('Custom Font'),
       url('/fonts/file.woff2') format('woff2'), 
       url('/fonts/file.woff') format('woff'),
       url('/fonts/file.ttf') format('truetype'),
       url('/fonts/file.eot') format('embedded-opentype');
}
```

The declaration consists of the following descriptors:

* `font-family` describes the font name. This is the name that we will eventually use it when we want to apply that custom font. It's common to wrap custom font names in quotes. According to the [W3C](https://www.w3.org/TR/css-fonts-3/#family-name-value) punctuation characters, digits at the start of the name should be escaped. To avoid escaping errors, it's always recommended to quote custom font names.
* `font-style` tells the style of the font. This can either be normal or italic.
* `font-weight` tells the weight of the font. The range from `thin` towards `black`.
* `src` defines the path to the custom font files. It optionally starts with the local variant of the font then followed by the urls and formats of the provided font files. Why do we need to load all these formats? This is what we are going to discuss on the following section.
* `font-display` determines how a font face is displayed based on whether and when it is downloaded and ready to use. More information about this descriptor on the fonts loading section below.

Sometimes we use a descriptor called `unicode-range` to restrict rendering a range of characters to a specific font. This technique is commonly used in Arabic websites when the font that is used in the design doesn't have a Hindi glyphs for the numbers. Most Arab countries and users uses Hindi glyphs (١٢٣٤٥٦٧٨٩٠) and not Arabic numbers (1234567890). To fix that, we use a custom font that contain the glyphs we want to use (like [GE SS Two](https://fonts2u.com/ge-ss-two-bold.font)) and restrict it to only render the numbers like the following:

```css
/* The font used for the Aarbic text */
@font-face {
  font-family: 'Custom Arabic Font';
  font-style: normal;
  font-weight: 400;
  src: local('Custom Arabic Font'),
       url('/fonts/file.woff2') format('woff2'), 
       url('/fonts/file.woff') format('woff');
}

/* The font used for the Hindi numbers. Note that we use the same family name as the font we used above */
@font-face {
  font-family:"Custom Arabic Font";
  unicode-range: U+0030-0039;
  src: url('/fonts/GE-SS-TWO.woff2') format('woff2'), 
       url('/fonts/GE-SS-TWO.woff') format('woff');
}
```

As you have noticed, we used the same `font-family` value for both fonts with the addition off the `unicode-range` for the font that will be used to render the numbers. This way we ensure that the numbers will always be rendered as we want.

Note that the `U` character written at the beginning of the value should be in uppercase otherwise it will be an invalid values in some browsers.

> We used the term descriptor instead of properties within the @font-face rule. Refer to [CSS Syntax](syntax.md) to understand the difference between both.

## Formats

Different browsers support different formats of font files. This is why we are declaring a list of different formats for the browsers to pick the one it can use. Browsers didn't agree to support a common format because of several reasons like licensing and other stuff. The timeline for the formats usage was like the following:

* Older versions of IE started to support Embedded OpenType (EOT) format. EOT was proprietary to Microsoft then it was submitted to W3C to make it an open Web Standard.
* Until Safari 4 on iOS, SVG fonts was the only supported format. Its file size was the biggest (but compressed very well with gzip) and lacked [font-hinting](https://en.wikipedia.org/wiki/Font_hinting). It had a few bugs related to text selecting but it was the only way to display custom fonts on iPhone and iPad on this time.
* Newer versions of IE (9 and above), Firefox, Chrome and Safari on Mac was supporting OTF and TTF.
* Mozilla, Microsoft and Opera worked on a new standard together and proposed the Web Open Font Format (WOFF) which is OTF/TTF with metadata and compression. This format was created to be used on the web and it has an advantage over the other formats of being smaller in size. This format is widely supported on all modern browsers.
* WOFF2 is the next generation of WOFF and it is 30% smaller in size. It is less widely supported than its predecessor.

We don't use some formats like the EOT and SVG because it's unlikely to find users who still use old browsers that require these formats. You are most likely to encounter these formats in old code base. It's mostly limited now to WOFF2, WOFF and TTF/OTF.

## Generating custom font kits for the web

We mentioned earlier the service named [FontSquirrel](http://fontsquirrel.com) where you can go and upload your custom font (TTF/OTF) and generate the different formats in addition to the CSS you will need to use. It will check the font against a list of known or previously reported commercial fonts and if it isn't in that list it will generate the kit.

I personally use another service called [onlinefontconverter.com](https://onlinefontconverter.com/) because it works very well with fonts for non-latin languages (like Arabic) unlike FontSquirrel.

There is also a free desktop application called [FontPlop](https://www.fontplop.com/) but it works on Mac only.

## Fonts loading

The browser doesn't download the font files unless it tries to render text that require that font. In other words, if your CSS file reference the bold weight of the font `Roboto` but the current page the user is visiting doesn't have any bold text, the font will not be downloaded. This is actually good because it saves the bandwidth and ensure that only the needed font weights/families are downloaded.

Because fonts aren't downloaded until required, this sometimes leads to what we call **Flash of Invisible Text (FOIT)** and **Flash of Unstyled Text (FOUT)**.  In other words, text space being preserved but the text itself is invisible then uses another font. This happens because browsers follow different policies for rendering text while the font is being downloaded. Let's take a look at the font display timeline.

### Font display timeline

The moment the browser tries to use a font for the first time on a page, the font files download timer is started. This timer advances through three periods of time associated with the font:

* **Font block-period**. During this period if the font file is not loaded, The text trying to use that font will use be invisible. If the font is successfully downloaded during the block-period, the font will be used normally.
* **Font swap-period**. During this period, if the font file is not downloaded, the text will be rendered using the fallback font. If the font file is downloaded successfully during, the font will be used normally.
* **Font failure-period**. If the font file is not downloaded the font is marked as a failed load and the fallback font will be used.

Each period varies according to the browsers. Some browsers like IE will render the fallback font first and render the custom font once it is ready. Chrome and Firefox will have a block-period of 3 seconds then either use the fallback font or the custom font. Safari on iOS have a relatively long block-period of 30 seconds before using the fallback font. Because of these variation, a standard was developed (currently a working draft) to control this behavior using the `font-display` descriptor.

### `font-display` descriptor

The `font-display` descriptor is used to control the behavior of the browsers when downloading custom fonts and rendering text. The `font-display` descriptor is used inside the `@font-face` rule and accept one of the following values:

* `auto` is the default value. This will leave the display to the policy defined by browsers. Most of the browsers have a default policy similar to `block`.
* `block` will give a short block-period (3 seconds) and an infinite swap-period. That means that the browser will render invisible text at first if the font is not downloaded for 3 seconds. During this period if the font is downloaded, it will use the font once it is downloaded. If it couldn't download the files, it will use the fallback font.
* `swap` will gives a very short block-period (100ms) and an infinite swap period. That means that the browser will render the text immediately with the fallback if the font files aren't downloaded and will render the text again using the custom font if it is downloaded.
* `fallback` will give a very short block-period (100ms) and a short swap-period (3 seconds). That means that if the text will be rendered with the fallback font at first if the font is not loaded. It will be swapped with the custom font if it is downloaded within 3 seconds. After that, the font will not be used to render the text again and it will keep using the fallback font.
* `optional` will give a very short block-period (100ms) and a zero second swap period. That means that the custom font will only be used if it has been downloaded and cached. Otherwise, the fallback font will be used. The browser can decide to abort downloading the font if the connectivity is low or the browser is operating under the [Data Saving](https://support.google.com/chrome/answer/2392284?hl=en&co=GENIE.Platform%3DDesktop&oco=0) mode. With this option, the custom text will be only used on subsequent visits to the website pages after the font is downloaded and cached.

To see the effect of this descriptor in action, you can check this [YouTube video](https://www.youtube.com/watch?v=PuGmlOe5wRk) using Chrome DevTools and the [network throttling](https://developers.google.com/web/tools/chrome-devtools/network-performance/reference#throttling) options. Perhaps now the question to answer, which one should I use?

* `block` usage should be **limited to a few cases where using the custom font is crucial**. Consider an icon font that uses regular character or unicode characters to render icons. While the font is being downloaded and the `font-display` is set to `swap`; unrelated characters or squares will show up. `block` will be perfect and cause less confusion to the reader.
* `swap` usage should be **limited for short text** (like titles, logos). If we used `swap` on long chunks of text it will be very confusing for the reader to follow the text after the re-render happens. The size of the characters varies across the different font. If the difference in size between the two fonts is noticeable, the text will be rearranged in the page and the reader might lose track of where he stopped before the re-rendering happens.
* `fallback` should be **used for large pieces of text**. Ideally the focus on the body text shouldn't be how the font's *look*. The user shouldn't be disturbed by the text rearrangement when the new font is swapped. The only problem with this technique is to explain to non-techie customers you are building a platform for "why is the body text sometimes looks different".
* `optional` is the best if accessibility is your top priority. It's similar to `fallback` but stresses more on getting the text rendered with the least bandwidth usage.

> The numbers mentioned above (3s and 100ms for blocking) aren't defined by the specs, they are just a recommendation. Google Chrome change this time adaptively to improve performance on slow connections if `font-display` is set to `auto`. This manifest as a console warning/error. You can read more about it [here](https://www.chromestatus.com/feature/5636954674692096).

## TODO

- Different syntax used for loading custom fonts along with its corresponding weights and the local reference
- Fonts and CORS
- Add note about `@font-feature-values` rule
- Identifying rendered font in Chrome
- Optimizing font loading with link preloading
- Font Loading API
- A little bit about what we used to use before custom fonts (cufón, sIFR, image replacement techniques ...)

## Notes

### Tools

* [FontSquirrel](http://fontsquirrel.com) Free fonts library and online web fonts generator.
* [Online font converter](https://onlinefontconverter.com/) A free and reliable online tool to generate web fonts. Very useful if you need to convert a non-latin typeface (like Arabic)
* [FontPlop](https://www.fontplop.com/) Open source web font converter for Mac

### Great resources

* [Web Fonts are Rocket Science](https://vimeo.com/254727749) - SmashingConf London 2018 — Zach Leatherman
* [Zach Leatherman blog](https://www.zachleat.com/web/fonts/) about web fonts, web font loading and typography. One of the best advocates on the topic.

### References

* [Fonts on the web](https://www.w3.org/Fonts/) - W3C
* [Serving and Protecting Fonts on the Web](https://blog.typekit.com/2009/07/21/serving-and-protecting-fonts-on-the-web/) by Typekit
* [Fighting the @font-face FOUT](https://www.paulirish.com/2009/fighting-the-font-face-fout/) by Paul Irish