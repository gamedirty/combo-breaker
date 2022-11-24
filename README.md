# Combo Breaker

[![combo-breaker](https://img.shields.io/maven-central/v/dev.romainguy/combo-breaker.svg?label=combo-breaker&color=brightgreen)](https://search.maven.org/search?q=g:%22dev.romainguy%22%20AND%20a:%22combo-breaker%22)
[![Android build status](https://github.com/romainguy/combo-breaker/workflows/Android/badge.svg)](https://github.com/romainguy/combo-breaker/actions?query=workflow%3AAndroid)

Composable widget for Jetpack Compose that allows to flow text around arbitrary shapes over
multiple columns. The `TextFlow` composable behaves as a `Box` layout and will automatically
flow the text content around its children.

* [Features](#features)
* [Examples](#examples)
* [Maven](#maven)
* [Roadmap](#roadmap)
* [License](#license)
* [Attribution](#attribution)

## Features

- Multi-column layout
- Styled strings (`AnnotatedString`)
- Default rectangular shapes
- Arbitrary shapes (any `Path`)
- Justification
- Hyphenation
- Generate shapes from images
- Compatible with API 29+

## Examples

The following code defines two images to flow text around:

```kotlin
TextFlow(
    SampleText,
    style = TextStyle(fontSize = 14.sp),
    columns = 2
) {
    Image(
        bitmap = letterT.asImageBitmap(),
        contentDescription = "",
        modifier = Modifier
            .flowShape(FlowType.OutsideEnd)
    )

    Image(
        bitmap = badgeBitmap.asImageBitmap(),
        contentDescription = "",
        modifier = Modifier
            .align(Alignment.Center)
            .flowShape(margin = 6.dp)
    )
}
```

![Flow around rectangular shapes](art/screenshot_default_shapes.png)

Any child of `TextFlow` allows text to flow around a rectangular shape of the same dimensions of the
child. The `flowShape` modifier is used to control where text flows around the shape (to the
right/end of the T) and around both the left and right sides of the landscape photo (default
behavior). In addition, you can define a margin around the shape.

The `flowShape` modifier also lets you specify a specific shape instead of a default rectangle.
This can be done by passing a `Path` or a lambda that returns a `Path`. The lambda alternative
is useful when you need to create a `Path` based on the dimensions of the `TextFlow` or the
dimensions of its child.

Here is an example of a `TextFlow` using non-rectangular shapes:

```kotlin
val microphoneShape = microphoneBitmap.toContour(alphaThreshold = 0.5f).asComposePath()
val badgeShape = badgeShape.toContour(alphaThreshold = 0.5f).asComposePath()

TextFlow(
    SampleText,
    style = TextStyle(fontSize = 14.sp),
    columns = 2
) {
    Image(
        bitmap = microphoneBitmap.asImageBitmap(),
        contentDescription = "",
        modifier = Modifier
            .offset { Offset(-microphoneBitmap.width / 4.5f, 0.0f).round() }
            .flowShape(FlowType.OutsideEnd, 6.dp, microphoneShape)
    )

    Image(
        bitmap = badgeBitmap.asImageBitmap(),
        contentDescription = "",
        modifier = Modifier
            .align(Alignment.Center)
            .flowShape(FlowType.Outside, 6.dp, badgeShape)
    )
}
```

Using the extension `Bitmap.toContour` provided by this library, a shape can be extracted from a
bitmap and used as the flow shape for the desired child:

![Flow around non-rectangular shapes](art/screenshot_arbitrary_shapes.png)

`TextFlow` supports multiple text styles and lets you control justification and hyphenation. In
the example below, both justification and hyphenation are enabled:

![Justification and hyphenation](art/screenshot_styles_and_justification.png)

## Maven

```gradle
repositories {
    // ...
    mavenCentral()
}

dependencies {
    implementation 'dev.romainguy:combo-breaker:0.3.0'
}
```

## Roadmap

- Properly support paragraphs with styles of different line heights.
- Backport to earlier API levels.
- Optimizations!
- More comprehensive `TextFlowLayoutResult`.
- Paths with multiple contours are treated as a single shape. A future feature will allow such
  paths to be treated as multiple shapes.
- Add support to ellipsize the last line when the entire text cannot fit in the layout area.
- Add support for text-relative placement of flow shapes.
- Implement margins support without relying on `Path.op` which can be excessively expensive with
  complex paths.
- Reduce allocations during the layout phase.
- BiDi text hasn't been tested yet, and probably doesn't work properly (RTL layouts are however
  supported for the placement of flow shapes and the handling of columns).
- Improve performance of contours extraction from an image (could be multi-threaded for instance).
- Investigate an alternative and simpler way to handle placement around shapes (beam cast instead 
  of the purely geometric approach that currently requires a lot of intersection work).
- Support flowing text inside shapes.

## License

Please see [LICENSE](./LICENSE).

## Attribution

The render of the microphone was made possible thanks to
[RCA 44-BX Microphone](https://skfb.ly/6AKHx) by Tom Seddon, licensed under
[Creative Commons Attribution](http://creativecommons.org/licenses/by/4.0/).

Sample text taken from the [Wikipedia Hyphen article](https://en.wikipedia.org/wiki/Hyphen).
