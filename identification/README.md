# NMOS Control Feature Sets: Identification

Includes models for control classes and datatypes used for identification.

- [NMOS Control Feature Sets: Identification](#nmos-control-feature-sets-identification)
  - [Control classes](#control-classes)
    - [NcIdentBeacon](#ncidentbeacon)

## Control classes

### NcIdentBeacon

The identification beacon model may be used in different ways subject to implementation:

- The active state is changed remotely and activates a visual or audio beacon element on the device
- A physical button or trigger on the device changes the active state which can be monitored remotely

```typescript
// NcIdentBeacon class descriptor
[control-class("1.2.1")] interface NcIdentBeacon: NcWorker {
    [element("3p1")]                attribute    NcBoolean    active;    // Indicator active state
};
```
