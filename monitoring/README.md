# NMOS Control Feature Sets: Monitoring

Includes models for control classes and datatypes used for monitoring.

- [NMOS Control Feature Sets: Monitoring](#nmos-control-feature-sets-monitoring)
  - [NcConnectionStatus](#ncconnectionstatus)
  - [NcPayloadStatus](#ncpayloadstatus)
  - [NcReceiverMonitor](#ncreceivermonitor)
  - [NcReceiverMonitorProtected](#ncreceivermonitorprotected)

## NcConnectionStatus

```typescript
// Connection status enum data type
enum NcConnectionStatus {
    "Undefined",        // 0 This is the value when there is no receiver
    "Connected",        // 1 Connected to a stream
    "Disconnected",        // 2 Not connected to a stream
    "ConnectionError"        // 3 A connection error was encountered
};
```

## NcPayloadStatus

```typescript
// Payload status enum data type
enum NcPayloadStatus {
    "Undefined",        // 0 This is the value when there's no connection.
    "PayloadOK",        // 1 Payload is being received without errors and is the correct type
    "PayloadFormatUnsupported",        // 2 Payload is being received but is of an unsupported type
    "PayloadError"        // 3 A payload error was encountered
};
```

## NcReceiverMonitor

Base receiver monitoring worker class.  
It uses the Touchpoint mechanism inherited from NcObject in order to attach to the correct receiver identity.

```typescript
// NcReceiverMonitor class descriptor
[control-class("1.2.3")] interface NcReceiverMonitor: NcWorker {
    [element("3p1")]    readonly    attribute    NcConnectionStatus    connectionStatus;    // Connection status property
    [element("3p2")]    readonly    attribute    NcString?    connectionStatusMessage;    // Connection status message property
    [element("3p3")]    readonly    attribute    NcPayloadStatus    payloadStatus;    // Payload status property
    [element("3p4")]    readonly    attribute    NcString?    payloadStatusMessage;    // Payload status message property
};
```

## NcReceiverMonitorProtected

Derived receiver monitoring worker class for SMPTE ST 2022-7-type receivers.

```typescript
// NcReceiverMonitorProtected class descriptor
[control-class("1.2.3.1")] interface NcReceiverMonitorProtected: NcReceiverMonitor {
    [element("4p1")]    readonly    attribute    NcBoolean    signalProtectionStatus;    // Indicates if signal protection is active
};
```
