# AMWA NMOS Control Feature Sets

[![Lint Status](https://github.com/AMWA-TV/nmos-control-feature-sets/workflows/Lint/badge.svg)](https://github.com/AMWA-TV/nmos-control-feature-sets/actions?query=workflow%3ALint)
[![Render Status](https://github.com/AMWA-TV/nmos-control-feature-sets/workflows/Render/badge.svg)](https://github.com/AMWA-TV/nmos-control-feature-sets/actions?query=workflow%3ARender)

<!-- INTRO-START -->

### What does it do?

The feature sets register contains opt in models for features which devices might implement.

All the control class models in the feature sets register will always be derived directly or indirectly from an [NMOS Control framework](https://specs.amwa.tv/ms-05-02/) class model.

The feature sets in the register will contain either control classes or datatypes or both.

Where a device employs features covered by a feature set in the register it MUST comply with the model definitions specified in the particular feature set.

### Why does it matter?

It provides an extensibility mechanism to the [NMOS Control framework](https://specs.amwa.tv/ms-05-02/).

### How does it work?

It is organised by feature sets that contain models for either control classes or datatypes or both.

<!-- INTRO-END -->

> The list of feature sets is now generated on demand from `.render/_data/feature-sets.yml` and rendered at <https://specs.amwa.tv/nmos-control-feature-sets>:

