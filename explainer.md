# HTMLMediaElement controlsList explained

## [Pull request to whatwg/html](https://github.com/whatwg/html/pull/2426)

## Objective

Offers a way to control the native controls elements/buttons that are being
shown by the user agent in order to be able to remove some features that do not
make sense or are not part of the expected user experience or only whitelist a
limited amount of features.

## Background

The current control web developers have over the `HTMLMediaElement` controls is
via the `controls` attribute which is a boolean attribute. In other words, web
developers can tell the user agent whether controls should be shown.
Historically, specific use cases have been handled with "custom controls" which
are implemented by having no native controls a set of elements added on top of
the video.

With the media experience on the web getting richer, more controls are being
added to the native user agent controls. These controls sometimes can look out
of place or not work properly. For example, fullscreen and remote playback
buttons might not always make sense. Similarly, browser adding download buttons
have to implement heuristics that might sometimes fail, thus add a button that
will not download usable content. Switching to custom controls can be a hard
change for websites and their users, requiring user experience analysis,
cross-browser implementation and accessibility considerations. Furthermore,
native controls help user agents to build a tailored experience to their users
based on their environment, their device and their habits in a way that custom
controls can’t always achieve.

## Design

The proposed design is to have a whitelist/blacklist ability on native controls
with the ability to set them directly from HTML content. A new content attribute
`controlslist` is to be added as a space separated list of keywords being
reflected as `controlsList`, having an effect only if  the `controls` attribute
is set.

### Blacklisting

The main use case is to able to remove some features/elements from the native
controls. Blacklisting elements could be done with keywords starting with `no`
such as `nofullscreen` or `nodownload`. For example, `controlslist="nofullscreen
nodownload"`. It would tell the user agent to show the usual native control
 without a fullscreen nor a download button.

### Whitelisting

A less common use case that could be resolved within the scope of this API
change would be to allow a website to specify the exhaustive list of controls to
show. In order to do so, the list of keywords will have to only contain
whitelisted elements (without a no prefix). For example, `controlslist="play
timeline volume"` would allow a website to request the user agent to show native
controls with only a play button, a timeline, and a volume control.

The whilelisting proposal is here for completeness. The feature can be
implemented and specified incrementally, allowing only for blacklisting of some
elements to be supported first.

### Edge cases

Blacklisting could be done such as no button would be visible. The specification
should disallow this by either enforcing the play/pause button to always be
available or by adding a play/pause button if all the elements have been
removed.

Blacklist and whitelists can technically be put together (ie.
`controlslist="play nodownload"`). In the spirit of blacklisting being the main
feature of this proposal, blacklisting should always be used if one element is
blacklisted, thus ignoring all whitelist keywords.

### Backward compatibility

The backward compatibility will be preserved given that a user agent that does
not support the `controlsList` feature will just ignore
`controlslist="foo bar"`.

### Keywords and feature detection

The available keywords should be defined per spec. The `controlsList` will
reflect the controls attribute as a `DOMTokenList` for which the supported
tokens will be the keywords (eg. `nodownload`). The available keywords should be
defined in the specification but some user agents might not support some of
them. For example, a remote feature might not be implemented by all user agents.
Looking at the `controlsList` tokens will allow authors to do feature detection.

### Incremental

The feature is meant to be implemented and designed incrementally. The ability
to blacklist buttons is the main focus of this proposal and should be able to
work independently of the whitelist approach. In addition, all buttons are not
supposed to be supported initially. The spirit being that keywords should be
added to the specification with user agents implementing support for
blacklisting of new parts of the native controls.

### IDL and attribute reflection

```javascript
partial interface HTMLMediaElement {
  [CEReactions, SameObject, PutForwards=value] readonly attribute DOMTokenList controlsList;
};
```

The `controlsList` property will [reflect](https://html.spec.whatwg.org/multipage/infrastructure.html#reflect)
the `controlslist` attribute as a `DOMTokenList` with a list of [supported
tokens](https://dom.spec.whatwg.org/#concept-supported-tokens).

### Examples

Usage in HTML:
```html
<video controls controlslist="nofullscreen nodownload noremote foobar"></video>
```

Usage in Javascript:
```javascript
var video = document.querySelector('video');
video.controls; // true
video.controlsList; // "nofullscreen nodownload noremote" - "foobar" not present
video.controlsList.remove('noremote');
video.controlsList; // "nofullscreen nodownload" - "noremote" not present
video.getAttribute('controlslist'); // "nofullscreen nodownload"
```
