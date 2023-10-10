# NMOS Control Feature Sets: Monitoring

Includes models for control classes and datatypes used for monitoring.

- [NMOS Control Feature Sets: Monitoring](#nmos-control-feature-sets-monitoring)
  - [Datatypes](#datatypes)
    - [NcConnectionStatus](#ncconnectionstatus)
    - [NcPayloadStatus](#ncpayloadstatus)
  - [Control classes](#control-classes)
    - [NcReceiverMonitor](#ncreceivermonitor)
    - [NcReceiverMonitorProtected](#ncreceivermonitorprotected)

## Datatypes

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

### NcPayloadStatus

```typescript
// Payload status enum data type
enum NcPayloadStatus {
    "Undefined",        // 0 This is the value when the receiver hasn't been instructed to connect to a stream
    "PayloadOK",        // 1 Payload is being received without errors and is the correct type
    "PayloadFormatUnsupported",        // 2 Payload is being received but is of an unsupported type
    "PayloadError"        // 3 A payload error was encountered
};
```

## Control classes

### NcReceiverMonitor

Base receiver monitoring worker class required for expressing connection and payload statuses for an attached stream receiver.  
It uses the Touchpoint mechanism inherited from NcObject to attach to the correct receiver identity.

Receiver monitors MUST maintain a 1 to 1 relationship between their role and the touchpoint receiver entity they monitor as long as the receiver entity hasn't been disposed by the device.

```typescript
// NcReceiverMonitor class descriptor
[control-class("1.2.3")] interface NcReceiverMonitor: NcWorker {
    [element("3p1")]    readonly    attribute    NcConnectionStatus    connectionStatus;    // Connection status property
    [element("3p2")]    readonly    attribute    NcString?    connectionStatusMessage;    // Connection status message property
    [element("3p3")]    readonly    attribute    NcPayloadStatus    payloadStatus;    // Payload status property
    [element("3p4")]    readonly    attribute    NcString?    payloadStatusMessage;    // Payload status message property
};
```

### NcReceiverMonitorProtected

Derived receiver monitoring worker class for SMPTE ST 2022-7-type receivers.

```typescript
// NcReceiverMonitorProtected class descriptor
[control-class("1.2.3.1")] interface NcReceiverMonitorProtected: NcReceiverMonitor {
    [element("4p1")]    readonly    attribute    NcBoolean    signalProtectionStatus;    // Indicates if signal protection is active
};
```
