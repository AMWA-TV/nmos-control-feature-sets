# NMOS Control Feature Sets: Device configuration

Includes models for control classes and datatypes used for device configuration (see [NMOS IS-14](https://specs.amwa.tv/is-14/)).

- [NMOS Control Feature Sets: Device configuration](#nmos-control-feature-sets-device-configuration)
  - [Datatypes](#datatypes)
    - [NcPropertyValueHolder](#ncpropertyvalueholder)
    - [NcObjectPropertiesHolder](#ncobjectpropertiesholder)
    - [NcBulkValuesHolder](#ncbulkvaluesholder)
    - [NcObjectPropertiesSetValidation](#ncobjectpropertiessetvalidation)
    - [NcMethodResultBulkValuesHolder](#ncmethodresultbulkvaluesholder)
    - [NcMethodResultObjectPropertiesSetValidation](#ncmethodresultobjectpropertiessetvalidation)
  - [Control classes](#control-classes)
    - [NcBulkPropertiesManager](#ncbulkpropertiesmanager)

## Datatypes

### NcPropertyValueHolder

```typescript
// Property value holder descriptor
interface NcPropertyValueHolder {
    attribute NcPropertyId    id; // Property id
    attribute NcString    name; // Property name
    attribute NcName?    typeName; // Property type name. If null it means the type is any
    attribute NcBoolean?    isReadOnly; // Is the property ReadOnly?
    attribute any?    value; // Property value
};
```

### NcObjectPropertiesHolder

```typescript
interface NcObjectPropertiesHolder {
    attribute NcRolePath    rolePath; // Object role path
    attribute sequence<NcPropertyValueHolder>    values; // Object properties values
};
```

### NcBulkValuesHolder

```typescript
interface NcBulkValuesHolder {
    attribute NcString?    validationFingerprint; // Optional vendor specific fingerprinting mechanism used for validation purposes
    attribute sequence<NcObjectPropertiesHolder>    values; // Values by rolePath
};
```

### NcObjectPropertiesSetValidation

```typescript
interface NcObjectPropertiesSetValidation {
    attribute NcRolePath    rolePath; // Object role path
    attribute NcMethodStatus    status; // Validation status
    attribute NcString?    statusMessage; // Validation status message
};
```

### NcMethodResultBulkValuesHolder

```typescript
interface NcMethodResultBulkValuesHolder: NcMethodResult {
    attribute NcBulkValuesHolder    value; // Bulk values holder value
};
```

### NcMethodResultObjectPropertiesSetValidation

```typescript
interface NcMethodResultObjectPropertiesSetValidation: NcMethodResult {
    attribute sequence<NcObjectPropertiesSetValidation>    value; // Object properties set path validations
};
```

## Control classes

### NcBulkPropertiesManager

The `BulkPropertiesManager` offers a central model for getting and setting properties of multiple role paths.
It also allows pre-validation of a data set before attempting to use in setting multiple properties of multiple role paths.

```typescript
// NcBulkPropertiesManager class descriptor
[control-class("1.3.3", "BulkPropertiesManager")] interface NcBulkPropertiesManager: NcManager {

    // Get bulk object properties by given path
    [element("3m1")]    NcMethodResultBulkValuesHolder GetPropertiesByPath(
        NcRolePath rolePath,    // The target role path
        NcBoolean recurse    // If true will return properties on specified path and all the nested paths
    );

    // Validate bulk properties for setting by given paths
    [element("3m2")]    NcMethodResultObjectPropertiesSetValidation ValidateSetPropertiesByPath(
        NcBulkValuesHolder dataSet,    // The values offered (this may include read-only values and also paths which are not the target role path)
        NcRolePath rolePath,    // The target role path
        NcBoolean recurse    // If true will validate properties on target path and all the nested paths
    );

    // Set bulk properties by given paths
    [element("3m3")]    NcMethodResultObjectPropertiesSetValidation SetPropertiesByPath(
        NcBulkValuesHolder dataSet,    // The values offered (this may include read-only values and also paths which are not the target role path)
        NcRolePath rolePath,    // The target role path
        NcBoolean recurse    // If true will set properties on target path and all the nested paths
        NcBoolean allowPartial    // If true will allow the device to restore partially only the role paths which pass validation
    );
};
```
