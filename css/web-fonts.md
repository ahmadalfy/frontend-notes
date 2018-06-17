# [WORK IN PROGRESS] Web Fonts

Web fonts play a great role on web design today. Thanks to the `@font-face` property, we are able to use all these beautiful fonts we want. It took us so long to get there. For a long time we were limited to a set of fonts that we used to call *web safe* fonts. Suriprisingly the reason wasn't a technical one.

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

The declaration consists of:

* `font-family` describes the font name. This is the name that we will eventually use it when we want to apply that custom font. It's common to wrap custom font names in quotes. According to the [W3C](https://www.w3.org/TR/css-fonts-3/#family-name-value) punctuation characters, digits at the start of the name should be escaped. To avoid escaping errors, it's always recommended to quote custom font names.
* `font-style` tells the style of the font. This can either be normal or italic.
* `font-weight` tells the weight of the font. The range from `thin` towards `black`.
* `src` defines the path to the custom font files. It optionally starts with the local variant of the font then followed by the urls and formats of the provided font files. Why do we need to load all these formats? This is what we are going to discuss on the following section.

## Formats

Different browsers support different formats of font files. This is why we are declaring a list of different formats for the browsers to pick the one it can use. Browsers didn't agree to support a common format because of several reasons like licensing and other stuff. The timeline for the formats usage was like the following:

* Older versions of IE started to support Embedded OpenType (EOT) format. EOT was proprietary to Microsoft then it was submitted to W3C to make it an open Web Standard.
* Until Safari 4 on iOS, SVG fonts was the only supported format. Its file size was the biggest (but compressed very well with gzip) and lacked [font-hinting](https://en.wikipedia.org/wiki/Font_hinting). It had a few bugs related to text selecting but it was the only way to display custom fonts on iPhone and iPad on this time.
* Newer versions of IE (9 and above), Firefox, Chrome and Safari on Mac was supporting OTF and TTF.
* Mozilla, Microsoft and Opera worked on a new standard together and proposed the Web Open Font Format (WOFF) which is OTF/TTF with metadata and compression. This format was created to be used on the web and it has an advantage over the other formats of being smaller in size. This format is widely supported on all modern browsers.
* WOFF2 is the next generation of WOFF and it is 30% smaller in size. It is less widely supported than its predecessor.

We don't use some formats like the EOT and SVG because it's unlikely to find users who still use old browsers that require these formats. You are most likely to encounter these formats in old code base. It's mostly limited now to WOFF2, WOFF and TTF/OTF.

## Generating custom font kits for the web

To be discussed

## Custom fonts loading

The browser doesn't download the font files unless it tries to render some text that require that font. In other words, if your CSS file reference the bold weight of the font `Roboto` but the current page the user is visiting doesn't have any bold text, the font will not be downloaded. This is actually good because it saves the bandwidth and ensure that only the needed font weights/families are downloaded.

Because fonts aren't downloaded until required, this sometimes leads to what we call **Flash of Invisible Text (FOIT)** and **Flash of Unstyled Text (FOUT)**. This phenomena is handled differently across different browsers. Some browsers follow the following approach:

* Hiding the text while preserving its space for sometime until the request timeout or the font loads
* If the request times out due to either a slow connection or failure to load the font, it will fallback to the next font in the font stack defined in the CSS.
* If the font loads after the timeout, it swap the font with the newly downloaded font

The period of the timeout varies between the different browsers. Some browsers like Safari get stuck and hide the text infinitely until the font is downloaded. Other browsers like Chrome gives it three-seconds before falling back to the next font. FireFox doesn't have a timeout, it will attempt to draw the text using the available font till the new font is downloaded.

## TODO

- ~~Different font families (serif, san-serif, monospace ...)~~
- More information about web safe fonts
- Good popular font stacks
- Chrome warning about falling to the next font on slower connections
- The CSS property to control the fallback behavior with screenshots `font-display`
- Verification of the handling of each browser
- Different syntax used for loading custom fonts along with its corresponding weights and the local reference
- Fonts and CORS
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