# Workshop: Unsprinkle

**Disclaimer**: This is a copy of my notes from my private repo of notes on the exercise. What's on the private repo is the definitive version of my notes. This is just for anybody who cares to take a look at my work on the exercise directly.

**Goal**: Using what we learned about typography and images in this module, we'll improve the performance, accessibility, and user experience for a photo-sharing application.

## Notes on My Progress

### Exercise 1: Optimize fonts

**Goal**: This project is using a variable font, which is fairly large. Swap it out for an optimized version using Google Fonts.

To do make life easier for us, we can just go ahead and use Raleway, a decent variable font that Josh already showed us the exact code to use to retrieve.

Step one: in `index.html`, add two `<link>` tags, one the `preconnect` and the other the link to the font itself. Because `GlobalStyles` references font weights between `100` and `900`, make sure to fetch all of those font weights.

Step two: in `index.html`, replace the `style @font-face { font-family: 'Inter' ... }` with something appropriate. I think the right thing to do might be to completely eliminate that from `index.html` and instead, in `GlobalStyles`, in the extant `font-family` declaration, change that from `'Inter', sans-serif` to `'Raleway', sans-serif`.

Josh says bonus points for self-hosting the optimized font, but I'm not going to do that.

One question is whether I need to go ahead and fetch the italicized version of the font. Using search to check for ital, it appears that I do not need to do so.
Step one-A: modify the `preconnect` `<link>` to remove the request for the italicized versions of the font.

I *think* this is it.

UPDATE: We're still using Inter as the font, so changing it to Raleway was a mistake on my part. Revert the change in `GlobalStyles` from Inter to Raleway. Restore the commented out text in `index.html`. Change the link in `index.html` to pull Inter instead of Raleway.

### Exercise 2: Improve images

**Goal**: For a photo-heavy site, we can significantly improve the user experience by optimizing images. The images in the photo grid and the hero should implement these optimizations:

1. Server larger images to users on high-DPI displays.
2. Serve `.avif` images instead of `.jpg` images to users on supported browsers (i.e., not IE).

These images already exist, and have a basic naming convention: `.jpg`, `.avif`, `@2x.jpg/.avif`, `@3x.jpg/.avif`. These can be hardcoded for the hero, but should be done dynamically in the photo grid.

The images are also distorted right now. They are all the same size, but don't have the same aspect ratio. Based on Josh's example in the README of what images should look like, they should be cropped and centered.

Step one: in `Hero`, it looks like we'll be doing some things to `HeroImage`. First, change it (a S-C) to a `<picture>` element.
Step two: inside `HeroImage`, create a new `img` with that `src` (and to fix the warning, add for now an `alt=""`. This will be addressed in Exercise 3).

One thing we want to do is support serving multiple file formats. That's why we changed it to a `<picture>` element.

Step three: inside `HeroImage`, add the `srcset` for the `.avif` options.
Step four: inside `HeroImage`, add a similar `srcset` for the `.jpg` options.
Step four-A: change `srcset` to `srcSet` because of JSX.

Now we want to update `PhotoGridItem`.

Step five: in `PhotoGridItem`, put `Image` inside of a `<picture>` element.
Step six: to that `<picture>` element, add `srcSet` for the `.avif` and `.jpg` options. Josh notes in the instructions `src.replace('.jpg', '@2x.avif')`, which is easy to update for each of the six options (3 .avif, 3 .jpg).

Now we want to make sure the images are displayed in the correct aspect ratio.

To do this, we want to update the styling in `PhotoGridItem`.
Step seven: add to `Image`, first `object-fit: cover` so we maintain both the image's aspect ratio and our grid of constant size so we can display three items each.
Step eight: to `Image`, add `object-position: center center`. To maintain the two priorities in `object-fit: cover`, we need to crop some of the image. (Good) photos are centered, so taking the center of the both the horizontal and vertical axes captures the core content of the photo. Plus, this way, the giraffe photo matches what Josh tells us to match, so we know it's right.

I think that completes this exercise.

### Exercise 3: Accessibility issues

**Goal**: There are several images in the application, and none of them have been given an `alt` attribute.

There are 11 images in total. Two of them images are in `Hero`. (Others in the 3x3 grid?)

Update all images so they have an appropriate `alt` attribute. Keep in mind what a good `alt` attribute should have.

Step one: in `PhotoGridItem`, we're passing in an `alt` as a prop, so add that to `Image`. This is coming from `data.js`, and it seems appropriate in all cases as a description of what the photo actually is.

Step two: in `Hero`, `Swoop` is just a decorate SVG and doesn't really need any `alt` text. Give it `alt=""`.

Step three: in `Hero`, `HeroImage` serves as the header for our page and is emblematic of the kind of content we are displaying below. Give it appropriate alt text for that.

Doing a search for 'img' confirms these two files are the only ones where an `img` is used, so the two in `Hero` and the 3x3 grid items appear to be the 11 items Josh references. That means this exercise is short and done.

### Exercise 4: Tag overflow

Each photo has several tags, which are displayed in grey boxes below the image. Some tags are very long.

Update the CSS so the tags always fit on one line. If there is too much text to fit, the final tag should have an ellipsis.

Josh note: **This is a challenging exercise**. You'll need to tweak some of the existing CSS. (Hint: Flexbox might not be the right layout mode for this).

For this exercise, we're clearly working in `PhotoGridItem`. Specifically, we're looking at `Tags` and `Tag`.

To get ellipses on overflow, we need

```CSS
p {
  overflow: hidden;
  text-overflow: ellipsis;
}
```

This one doesn't have the same sort of pure step-by-step process, since I spent a lot more time mucking around trying to see what worked. And I'm not entirely sure what I ended up with that I need and what I don't need. But...

Step one: to `Tags`, add `white-space: nowrap` because we don't want to create a new line when we can't fit everything on the same line.

Step two: to `Tags`, add `overflow: hidden` because we don't want the children tags to be displayed outside of their containing block.
Step three: to `Tags`, add `text-overflow: ellipsis` to get the ellipses when text overflow.

Playing with this now that I've completed the exercise, it appears that I don't really need parts two and three. I'll go ahead and comment them out.

Step four: to `Tags`, we want to identify the last child so use the selector `li:last-child` and give that `overflow: hidden` and `text-overflow: ellipsis`. This does what I want it to do.

I tried adding to `Tag` the `hidden` and `ellipsis`, perhaps with some sort of `last-of-type` selector, but couldn't get it to work. When I did have it, I ended up with not just the last `li` having ellipses but instead more than one of them.

This didn't seem as complicated as I expected it to be based on Josh's comment. I was a lot more uncertain, which reflects my discomfort with the material, but I figured out a solution by screwing around in a relatively short amount of time.
