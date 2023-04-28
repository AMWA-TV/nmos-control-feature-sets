# NMOS Control Feature Sets: Common

Includes general-purpose models of control classes and datatypes.

- [NMOS Control Feature Sets: Common](#nmos-control-feature-sets-common)
  - [NcDB](#ncdb)
  - [NcGain](#ncgain)
  - [NcLevelSensor](#nclevelsensor)

## NcDB

A ratio expressed in dB.

```typescript
typedef NcFloat32    NcDB; // A ratio expressed in dB.
```

## NcGain

The model for a simple gain control.

```typescript
// NcGain class descriptor
[control-class("1.2.1.1.1")] interface NcGain: NcActuator {
    [element("5p1")]                attribute    NcDB    gainValue;    // Gain value
};
```

## NcLevelSensor

The model for a simple level sensor that reads in dB

```typescript
// NcLevelSensor class descriptor
[control-class("1.2.1.2.1")] interface NcLevelSensor: NcSensor {
    [element("5p1")]    readonly    attribute    NcDB    reading;    // Level sensor reading in DB
};
```
