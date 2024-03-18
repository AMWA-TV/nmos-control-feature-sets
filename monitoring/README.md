# NMOS Control Feature Sets: Monitoring

Includes models for control classes and datatypes used for monitoring.

- [NMOS Control Feature Sets: Monitoring](#nmos-control-feature-sets-monitoring)
  - [Datatypes](#datatypes)
    - [NcOverallStatus](#ncoverallstatus)
    - [NcLinkStatus](#nclinkstatus)
    - [NcConnectionStatus](#ncconnectionstatus)
    - [NcSynchronizationStatus](#ncsynchronizationstatus)
    - [NcStreamStatus](#ncstreamstatus)
    - [NcMethodResultCounter](#ncmethodresultcounter)
  - [Control classes](#control-classes)
    - [NcStatusMonitor](#ncstatusmonitor)
    - [NcReceiverMonitor](#ncreceivermonitor)

## Datatypes

### NcOverallStatus

```typescript
// Overall status enum data type
enum NcOverallStatus {
    "Healthy",        // 1 The overall status is healthy
    "PartiallyHealthy",        // 2 The overall status is partially healthy
    "Unhealthy"        // 3 The overall status is unhealthy
};
```

### NcLinkStatus

```typescript
// Link status enum data type
enum NcLinkStatus {
    "AllDown",        // 1 All the associated network interfaces are down
    "SomeDown",        // 2 Some of the associated network interfaces are down
    "AllUp"        // 3 All the associated network interfaces are up
};
```

### NcConnectionStatus

```typescript
// Connection status enum data type
enum NcConnectionStatus {
    "Undefined",        // 0 This is the value when the receiver hasn't been instructed to connect to a stream
    "Connected",        // 1 Connected to a stream
    "Disconnected",        // 2 Not connected to a stream
    "ConnectionError"        // 3 A connection error was encountered
};
```

### NcSynchronizationStatus

```typescript
// Synchronization status enum data type
enum NcSynchronizationStatus {
    "NotUsed",        // 0 Feature not in use
    "BasebandLocked",        // 1 Locked from baseband
    "BasebandPartiallyLocked",        // 2 Partially locked from baseband
    "NetworkLocked",        // 3 Partially locked from network
    "NetworkPartiallyLocked",        // 3 Partially locked from network
    "NotLocked"        // 3 Not locked
};
```

### NcStreamStatus

```typescript
// Stream status enum data type
enum NcStreamStatus {
    "Inactive",        // 0 Inactive
    "ValidStream",        // 1 Stream is valid
    "InvalidStream"        // 2 Stream is invalid
};
```

### NcMethodResultCounter

```typescript
// Counter method result
interface NcMethodResultCounter: NcMethodResult {
    attribute NcUint64    value; // Counter value
};
```

## Control classes

### NcStatusMonitor

This is the baseline status monitoring class that all feature status monitors use.
The goal is to make it easy for clients to find a very high level `overallStatus`.
The other goal is that it makes it easy to find all status monitors in a device model by using the [FindMembersByClassId](https://specs.amwa.tv/ms-05-02/latest/docs/Blocks.html#search-methods) inside block classes.

```typescript
// Baseline status monitoring class
[control-class("1.2.2")] interface NcStatusMonitor: NcWorker {
    [element("3p1")]    readonly    attribute    NcOverallStatus    overallStatus;    // Overall status property
};
```

### NcReceiverMonitor

Receiver monitoring class required for expressing statuses of different domains (connectivity, synchronization, stream validation).

Because it derives from the baseline [NcStatusMonitor](#ncstatusmonitor) it will also expose an `overallStatus` property.

It uses the Touchpoint mechanism inherited from NcObject to attach to the correct receiver identity.

Receiver monitors MUST maintain a 1 to 1 relationship between their role and the touchpoint receiver entity they monitor as long as the receiver entity hasn't been disposed by the device.

```typescript
// Receiver monitor class descriptor
[control-class("1.2.2.1")] interface NcReceiverMonitor: NcStatusMonitor {
    [element("4p1")]    readonly    attribute    NcLinkStatus    linkStatus;    // Link status property
    [element("4p2")]    readonly    attribute    NcString?    linkStatusMessage;    // Link status message property
    [element("4p3")]    readonly    attribute    NcConnectionStatus    connectionStatus;    // Connection status property
    [element("4p4")]    readonly    attribute    NcString?    connectionStatusMessage;    // Connection status message property
    [element("4p5")]    readonly    attribute    NcSynchronizationStatus    synchronizationStatus;    // Synchronization status property
    [element("4p6")]    readonly    attribute    NcString?    synchronizationStatusMessage;    // Synchronization status message property
    [element("4p7")]    readonly    attribute    NcString?    grandMasterClockId;    // Grand master clock id property
    [element("4p8")]    readonly    attribute    NcStreamStatus    streamStatus;    // Stream status property
    [element("4p9")]    readonly    attribute    NcString?    streamStatusMessage;    // Stream status message property

    // Gets the lost packets
    [element("4m1")]    NcMethodResultCounter GetLostPackets();

    // Gets the late packets
    [element("4m2")]    NcMethodResultCounter GetLatePackets();

    // Resets the packet counters
    [element("4m3")]    NcMethodResult ResetPacketCounters();
};
```
