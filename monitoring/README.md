# NMOS Control Feature Sets: Monitoring

Includes models for control classes and datatypes used for monitoring.

- [NMOS Control Feature Sets: Monitoring](#nmos-control-feature-sets-monitoring)
  - [Datatypes](#datatypes)
    - [NcOverallStatus](#ncoverallstatus)
    - [NcLinkStatus](#nclinkstatus)
    - [NcConnectionStatus](#ncconnectionstatus)
    - [NcTransmissionStatus](#nctransmissionstatus)
    - [NcSynchronizationStatus](#ncsynchronizationstatus)
    - [NcStreamStatus](#ncstreamstatus)
    - [NcEssenceStatus](#ncessencestatus)
    - [NcCounter](#nccounter)
    - [NcMethodResultCounters](#ncmethodresultcounters)
  - [Control classes](#control-classes)
    - [NcStatusMonitor](#ncstatusmonitor)
    - [NcReceiverMonitor](#ncreceivermonitor)
    - [NcSenderMonitor](#ncsendermonitor)

## Datatypes

### NcOverallStatus

```typescript
// Overall status enum data type
enum NcOverallStatus {
    "Inactive",          // 0 Inactive
    "Healthy",           // 1 The overall status is healthy
    "PartiallyHealthy",  // 2 The overall status is partially healthy
    "Unhealthy"          // 3 The overall status is unhealthy
};
```

### NcLinkStatus

```typescript
// Link status enum data type
enum NcLinkStatus {
    "AllUp",        // 1 All the associated network interfaces are up
    "SomeDown",     // 2 Some of the associated network interfaces are down
    "AllDown"       // 3 All the associated network interfaces are down
};
```

### NcConnectionStatus

```typescript
// Connection status enum data type
enum NcConnectionStatus {
    "Inactive",          // 0 Inactive
    "Healthy",           // 1 Active and healthy
    "PartiallyHealthy",  // 2 Active and partially healthy
    "Unhealthy"          // 3 Active and unhealthy
};
```

### NcTransmissionStatus

```typescript
// Transmission status enum data type
enum NcTransmissionStatus {
    "Inactive",          // 0 Inactive
    "Healthy",           // 1 Active and healthy
    "PartiallyHealthy",  // 2 Active and partially healthy
    "Unhealthy"          // 3 Active and unhealthy
};
```

### NcSynchronizationStatus

```typescript
// Synchronization status enum data type
enum NcSynchronizationStatus {
    "NotUsed",                  // 0 Feature not in use
    "Healthy",                  // 1 Locked to a synchronization source
    "PartiallyHealthy",         // 2 Partially locked to a synchronization source
    "Unhealthy"                 // 3 Not locked to a synchronization source
};
```

### NcStreamStatus

```typescript
// Stream status enum data type
enum NcStreamStatus {
    "Inactive",          // 0 Inactive
    "Healthy",           // 1 Active and healthy
    "PartiallyHealthy",  // 2 Active and partially healthy
    "Unhealthy"          // 3 Active and unhealthy
};
```

### NcEssenceStatus

```typescript
// Essence status enum data type
enum NcEssenceStatus {
    "Inactive",          // 0 Inactive
    "Healthy",           // 1 Active and healthy
    "PartiallyHealthy",  // 2 Active and partially healthy
    "Unhealthy"          // 3 Active and unhealthy
};
```

### NcCounter

```typescript
// Counter data type
interface NcCounter {
    attribute NcString     name; // Counter name
    attribute NcUint64     value; // Counter value
    attribute NcString?    description; // Optional counter description
};
```

### NcMethodResultCounters

```typescript
// Counters method result
interface NcMethodResultCounters: NcMethodResult {
    attribute sequence<NcCounter>    value; // Counters
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
    [element("3p1")]    readonly    attribute    NcOverallStatus    overallStatus;      // Overall status property
    [element("3p2")]    readonly    attribute    NcString?    overallStatusMessage;     // Overall status message property
    [element("3p3")]                attribute    NcUint32    statusReportingDelay;      // Status reporting delay property (in seconds, default is 3s and 0 means no delay)
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
    [element("4p1")]     readonly    attribute    NcLinkStatus    linkStatus;    // Link status property
    [element("4p2")]     readonly    attribute    NcString?    linkStatusMessage;    // Link status message property
    [element("4p3")]     readonly    attribute    NcUint64    linkStatusTransitionCounter;    // Link status transition counter property
    [element("4p4")]     readonly    attribute    NcConnectionStatus    connectionStatus;    // Connection status property
    [element("4p5")]     readonly    attribute    NcString?    connectionStatusMessage;    // Connection status message property
    [element("4p6")]     readonly    attribute    NcUint64    connectionStatusTransitionCounter;    // Connection status transition counter property
    [element("4p7")]     readonly    attribute    NcSynchronizationStatus    externalSynchronizationStatus;    // External synchronization status property
    [element("4p8")]     readonly    attribute    NcString?    externalSynchronizationStatusMessage;    // External synchronization status message property
    [element("4p9")]     readonly    attribute    NcUint64    externalSynchronizationStatusTransitionCounter;    // External synchronization status transition counter property
    [element("4p10")]    readonly    attribute    NcString?    synchronizationSourceId;    // Synchronization source id property
    [element("4p11")]    readonly    attribute    NcStreamStatus    streamStatus;    // Stream status property
    [element("4p12")]    readonly    attribute    NcString?    streamStatusMessage;    // Stream status message property
    [element("4p13")]    readonly    attribute    NcUint64    streamStatusTransitionCounter;    // Stream status transition counter property
    [element("4p14")]                attribute    NcBoolean    autoResetCounters;    // Automatic reset counters property (default: true)

    // Gets the lost packet counters
    [element("4m1")]    NcMethodResultCounters GetLostPacketCounters();

    // Gets the late packet counters
    [element("4m2")]    NcMethodResultCounters GetLatePacketCounters();

    // Resets ALL counters
    [element("4m3")]    NcMethodResult ResetCounters();
};
```

### NcSenderMonitor

Sender monitoring class required for expressing statuses of different domains (connectivity, synchronization, essence validation).

Because it derives from the baseline [NcStatusMonitor](#ncstatusmonitor) it will also expose an `overallStatus` property.

It uses the Touchpoint mechanism inherited from NcObject to attach to the correct sender identity.

Sender monitors MUST maintain a 1 to 1 relationship between their role and the touchpoint sender entity they monitor as long as the sender entity hasn't been disposed by the device.

```typescript
// Sender monitor class descriptor
[control-class("1.2.2.2")] interface NcSenderMonitor: NcStatusMonitor {
    [element("4p1")]     readonly    attribute    NcLinkStatus    linkStatus;    // Link status property
    [element("4p2")]     readonly    attribute    NcString?    linkStatusMessage;    // Link status message property
    [element("4p3")]     readonly    attribute    NcUint64    linkStatusTransitionCounter;    // Link status transition counter property
    [element("4p4")]     readonly    attribute    NcTransmissionStatus    transmissionStatus;    // Transmission status property
    [element("4p5")]     readonly    attribute    NcString?    transmissionStatusMessage;    // Transmission status message property
    [element("4p6")]     readonly    attribute    NcUint64    transmissionStatusTransitionCounter;    // Transmission status transition counter property
    [element("4p7")]     readonly    attribute    NcSynchronizationStatus    externalSynchronizationStatus;    // External synchronization status property
    [element("4p8")]     readonly    attribute    NcString?    externalSynchronizationStatusMessage;    // External synchronization status message property
    [element("4p9")]     readonly    attribute    NcUint64    externalSynchronizationStatusTransitionCounter;    // External synchronization status transition counter property
    [element("4p10")]    readonly    attribute    NcString?    synchronizationSourceId;    // Synchronization source id property
    [element("4p11")]    readonly    attribute    NcEssenceStatus    essenceStatus;    // Essence status property
    [element("4p12")]    readonly    attribute    NcString?    essenceStatusMessage;    // Essence status message property
    [element("4p13")]    readonly    attribute    NcUint64    essenceStatusTransitionCounter;    // Essence status transition counter property
    [element("4p14")]                attribute    NcBoolean    autoResetCounters;    // Automatic reset counters property (default: true)

    // Gets the transmission error counters
    [element("4m1")]    NcMethodResultCounters GetTransmissionErrorCounters();

    // Resets ALL counters
    [element("4m2")]    NcMethodResult ResetCounters();
};
```
