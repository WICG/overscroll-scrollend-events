# Overscroll Customization
Overscroll customization refers to a set of scrolling events that can be used in the overscrolling scenarios.
This repository contains an [explainer](https://github.com/WICG/overscroll-scrollend-events/blob/gh-pages/README.md) and a [proposed specification](https://wicg.github.io/overscroll-scrollend-events/) for a new scroll events called overscroll and scrollend to address the need for customizing overscroll and complete the existing [`scroll` event](https://drafts.csswg.org/cssom-view/#eventdef-document-scroll) and expose more information to the content that is being scrolled.

If the incubation around this API is successful (e.g. is shipped by multiple browsers) then this spec hopefully will transition to [W3C CSS specification](https://github.com/w3c/csswg-drafts/issues/3801).

## Problem
With [CSS overscroll behavior](https://drafts.csswg.org/css-overscroll/) property can prevent user-agent defined [default boundary actions](https://drafts.csswg.org/css-overscroll/#boundary-default-action). Overscroll affordance (e.g. rubber-banding or glow effect), pull to refresh, and history swipe navigation are examples of those default boundary actions.

However, in order to implement customized behaviors, they still need to register a combination of scroll, touch, and wheel event listeners to find out when overscrolling has started and trigger their customized scroll boundary behavior. This approach has the following disadvantages/limitations:

  * Needs to be implemented for different methods of scrolling separately (touchscreen, wheel, keyboard, etc.)
  * If the developer wants to trigger overscroll animation at the end of the scroll gesture, touchend event listener can be used for touchscreen scrolling. However for other methods of scrolling (e.g. touchpad scrolling) this won’t be possible using the currently existing events.
  * In presence of a cross origin iframe the embedder that might get scrolled might not get the touchmove/wheel events at all if the touch starts on the cross origin iframe and that frame doesn't have any scrollable content.

## Solution
We are proposing two new event types:
  * `overscroll`: To be fired at the scrolling element when it reaches its scrolling extent and while it overscrolls.
  * `scrollend`: To be fired at the scrolled/overscrolled element indicating that the scroll sequence has ended and any scroll offset changes have been applied.

Note that during scroll, developers can always check the offset of the scrolling element to get what they need. But that is not the case for the overscrolling case as the offset of the scrolling element remain the same. For that we need to also expose `deltaX` and `deltaY` attributes on overscroll event to indicates how much the element has overscrolled. You can look at the exact idl in the [proposed specification](https://wicg.github.io/overscroll-scrollend-events/).

## Feature Detection

These events can be detected by the existence of their global listeners.

```javascript
  if (window.onoverscroll !== undefined) {
    // ... UA supports overscroll event.
  }
  if (window.onscrollend !== undefined) {
    // ... UA supports scrollend event.
  }    
```

## References
* [Chromium bug](https://bugs.chromium.org/p/chromium/issues/detail?id=907601)
* [CSS spec issue](https://github.com/w3c/csswg-drafts/issues/3801)
* [Chrome platform status](https://www.chromestatus.com/feature/5650553247891456)
* [WICG discourse](https://discourse.wicg.io/t/proposal-new-events-for-overscroll-and-scrollend/3481)

## Status
* This feature is now implemented behind Chrome "Experimental Web Platform features" flag to get more feedback from developers.
