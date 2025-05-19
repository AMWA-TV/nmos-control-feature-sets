# NMOS Control Feature Sets: Device configuration

Includes models for control classes and datatypes used for device configuration (see [NMOS IS-14](https://specs.amwa.tv/is-14/)).

- [NMOS Control Feature Sets: Device configuration](#nmos-control-feature-sets-device-configuration)
  - [Datatypes](#datatypes)
    - [NcRestoreMode](#ncrestoremode)
    - [NcPropertyValueHolder](#ncpropertyvalueholder)
    - [NcObjectPropertiesHolder](#ncobjectpropertiesholder)
    - [NcBulkValuesHolder](#ncbulkvaluesholder)
    - [NcRestoreValidationStatus](#ncrestorevalidationstatus)
    - [NcPropertyRestoreNoticeType](#ncpropertyrestorenoticetype)
    - [NcPropertyRestoreNotice](#ncpropertyrestorenotice)
    - [NcObjectPropertiesSetValidation](#ncobjectpropertiessetvalidation)
    - [NcMethodResultBulkValuesHolder](#ncmethodresultbulkvaluesholder)
    - [NcMethodResultObjectPropertiesSetValidation](#ncmethodresultobjectpropertiessetvalidation)
  - [Control classes](#control-classes)
    - [NcBulkPropertiesManager](#ncbulkpropertiesmanager)

## Datatypes

### NcRestoreMode

```typescript
// Restore mode enumeration
enum NcRestoreMode {
    "Modify",   // 0 Restore mode is Modify
    "Rebuild"   // 1 Restore mode is Rebuild
};
```

### NcPropertyValueHolder

```typescript
// Property value holder descriptor
interface NcPropertyValueHolder {
    attribute NcPropertyId    id; // Property id
    attribute NcString    name; // Property name
    attribute NcName?    typeName; // Property type name. If null it means the type is any
    attribute NcBoolean    isReadOnly; // Is the property ReadOnly?
    attribute any?    value; // Property value
};
```

### NcObjectPropertiesHolder

```typescript
interface NcObjectPropertiesHolder {
    attribute NcRolePath    path; // Object role path
    attribute sequence<NcRolePath>    dependencyPaths; // Sequence of role paths which are a dependency for this object (helpful to inform clients which objects need to be restored together)
    attribute sequence<NcClassId>    allowedMembersClasses; // Sequence of class ids allowed as members of the block (non-block objects have this as an empty sequence)
    attribute sequence<NcPropertyValueHolder>    values; // Object properties values
    attribute NcBoolean    isRebuildable; // Describes if the object is rebuildable
};
```

### NcBulkValuesHolder

```typescript
interface NcBulkValuesHolder {
    attribute NcString?    validationFingerprint; // Optional vendor specific fingerprinting mechanism used for validation purposes
    attribute sequence<NcObjectPropertiesHolder>    values; // Values by rolePath
};
```

### NcRestoreValidationStatus

```typescript
// Restore validation status enumeration
enum NcRestoreValidationStatus {
    "Ok",                   // 200 Restore was successful
    "Failed",               // 400 Restore failed
    "NotFound",             // 404 Restore failed because the role path is not found in the device model or the device cannot create the role path from the data set
    "DeviceError",          // 500 Restore failed due to an internal device error preventing the restore from happening
};
```

### NcPropertyRestoreNoticeType

```typescript
// Property restore notice type enumeration
enum NcPropertyRestoreNoticeType {
    "Warning",      // 300 Warning property restore notice
    "Error",        // 400 Error property restore notice
};
```

### NcPropertyRestoreNotice

```typescript
interface NcPropertyRestoreNotice {
    attribute NcPropertyId    id;                           // Property id
    attribute NcName    name;                               // Property name
    attribute NcPropertyRestoreNoticeType    noticeType;    // Property restore notice type
    attribute NcString    noticeMessage;                    // Property restore notice message
};
```

### NcObjectPropertiesSetValidation

```typescript
interface NcObjectPropertiesSetValidation {
    attribute NcRolePath    path;                                   // Object role path
    attribute NcRestoreValidationStatus    status;                  // Validation status
    attribute sequence<NcPropertyRestoreNotice>    notices;         // Validation property notices
    attribute NcString?    statusMessage;                           // Validation status message
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

The `BulkPropertiesManager` offers a central model for getting and setting multiple properties on multiple role paths.
It also allows pre-validation of a data set before attempting these get and set operations.

```typescript
// NcBulkPropertiesManager class descriptor
[control-class("1.3.3", "BulkPropertiesManager")] interface NcBulkPropertiesManager: NcManager {

    // Get bulk object properties by given path
    [element("3m1")]    NcMethodResultBulkValuesHolder GetPropertiesByPath(
        NcRolePath path,    // The target role path
        NcBoolean recurse    // If true will return properties on specified path and all the nested paths
    );

    // Validate bulk properties for setting by given paths
    [element("3m2")]    NcMethodResultObjectPropertiesSetValidation ValidateSetPropertiesByPath(
        NcBulkValuesHolder dataSet,    // The values offered (this may include read-only values and also paths which are not the target role path)
        NcRolePath path,    // The target role path
        NcBoolean recurse,    // If true will validate properties on target path and all the nested paths
        NcRestoreMode restoreMode  // Defines the restore mode to be applied
    );

    // Set bulk properties by given paths
    [element("3m3")]    NcMethodResultObjectPropertiesSetValidation SetPropertiesByPath(
        NcBulkValuesHolder dataSet,    // The values offered (this may include read-only values and also paths which are not the target role path)
        NcRolePath path,     // The target role path
        NcBoolean recurse,    // If true will set properties on target path and all the nested paths
        NcRestoreMode restoreMode  // Defines the restore mode to be applied
    );
};
```
