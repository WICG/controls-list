# Controls List API

## Problem Statement

The Web platform offers two solutions for websites with regards to media controls:
* use the browser provided ones (aka. native controls) without any control of the available features, user experience or UI;
* implement custom controls which creates complexity, maintainance burden and isn't future forward.

This situation pushes websites to extreme: they have to either not care about their media controls experience or have to fully commit to it. This lack of middle ground pushes websites to use custom controls as soon as they have one need that isn't compatible with the default browser experience. This is harming the ecosystem because custom controls can often offer lower quality implementation and prevents the browser from creating a homogenious experience for its users or integrate new system features into the website controls.

# Main Requirements

This API is intended to help solve part of this problem by providing a solution for websites not interested to implement their own media controls but still interested to tailor the experience for their users. It will not offer an API for websites to customise the look and feel of the control but will allow them to pick which components of the controls should be available to the users.

## Solution outline

The solution is described in details in the [explainer](explainer.md).

It is introducing a new attribute, `controlslist` that allows websites to blacklist or whitelist controls that should appear. The solution is meant to be incremental and start by only blacklisting of a few components and add more as requirements emerge.
