# The `content-size` CSS property

This is an explainer for why the `content-size` property is needed. A draft of the spec for this property is [here](http://tabatkins.github.io/specs/css-content-size/). The corresponding spec issue is [here](https://github.com/w3c/csswg-drafts/issues/4229).

`content-size` represents the intrinsic layout sizing of a DOM subtree. Intrinsic sizing is an input to layout of ancestors, and is an important input to the layout constraint algorithms of the web. As one example, if an element has [`width: max-content`](https://drafts.csswg.org/css-sizing-3/#valdef-width-max-content) CSS on it, then it is sized to the [max-content inline size](https://drafts.csswg.org/css-sizing-3/#max-content-inline-size), which is the maximum intrinsic width of elements in the subtree. 
Currently, such intrinsic sizes are determined implicitly from other factors. For example, a div with `width: 200px` has a `200px` intrinsic width that is communicated upwards in layout algorithms in order to provide sizing constraints to ancestors. Another example is that an image with a `width=200px` attribute will have a `200px` intrinsic width; if it instead has a `src` attribute pointing at an image resource that has a `200px` image resolution width, the image will also have a `200px` intrinsic width once the image is loaded.

This proposal exposes these intrinsic sizes explicitly. (In that sense, one motivation for the `content-size` property could be [explaining](https://extensiblewebmanifesto.org) these algorithms. One practical application could be its use in testing their behavior.

## Application to non-rendered subtrees

A more compelling and direct motivation for exposing `content-size` is to use it in cases when the layout of a subtree is *not available*. The layout can be unavailable in situations such as:
* The subtree is not yet fully loaded from the network, or rendered into DOM by a custom element or framework
* The User Agent has skipped rendering for the subtree as an optimization

Note that these two cases can be seen as the same use case, if the concept of "rendering" is extended to include asynchronous, scheduled factors such as the network, a DOM rendering framework [1], a [custom element](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements), dynamic replaced elements such as embedded SVG documents, images and videos. In other words, the [rendering event loop](https://github.com/chrishtr/rendering/blob/master/rendering-event-loop.md) is only one source of potential asynchrony in an expanded concept of rendering.

In such cases, the developer may still wish for the element to participate in the intrinsic sizing aspects of layout, by providing placeholder sizing, because otherwise there may be an undesirable, jarring [shift of the layout](https://web.dev/layout-instability-api) of the page as the content renders.

In addition, the height of an overflow-scrolling region depends on the [layout overflow size](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flow_Layout/Flow_Layout_and_Overflow) of its content, which includes intrinsic sizing. This allows a web developer to avoid spending time rendering scrollable content which is not visible to the user, but still have it take up space in the scroller.

See also the [main explainer](https://github.com/WICG/display-locking/blob/master/README.md) for display-locking for more motivating examples along these lines.

## Examples of `content-size`

[Here](https://wicg.github.io/display-locking/sample-code/contain-size-block-flow-examples.html) is a page demonstrating some simple block-flow layout use-cases.

[Here](https://wicg.github.io/display-locking/sample-code/contain-size-flexbox-examples.html) is a page demonstrating a basic flexbox use-case.

## Alternatives considered

Elements can already be sized by `width`, `min-width`, and various other sizing CSS properties. However, these properties fall short, because they all affect not just intrinsic sizing, but other layout inputs as well. The examples section gives several examples of this.

[1] Examples: React, Angular, Vue, ...