# IMCS — Software Requirements Specification

**Project:** Industrial Monitoring and Control System (IMCS)  
**Document Version:** 0.1  
**Status:** Draft  
**Last Updated:** 2026-11-02

## 1. Introduction

### 1.1 Purpose

This document defines the initial functional and quality requirements for the Industrial Monitoring and Control System (IMCS).

IMCS is a distributed hardware and software system designed to demonstrate industrial sensor monitoring, telemetry acquisition, communication, fault detection, and eventually remote equipment control.

The system is intended as an engineering prototype and portfolio project, not as a production-ready industrial safety or control system.

### 1.2 Scope

The initial development phase will implement a sensor node and Linux gateway capable of collecting, transmitting, and validating environmental measurements.

The initial system will consist of:

- An STM32L476RG microcontroller-based sensor node.
- A BME280 environmental sensor.
- A serial communication interface between the sensor node and a Linux gateway.
- A gateway application that receives and validates telemetry.

Future development phases may introduce persistent storage, a backend API, an operator dashboard, alarm management, and remote control capabilities.

### 1.3 Terminology

| Term | Definition |
|---|---|
| Sensor node | Embedded device responsible for collecting measurements |
| Gateway | Linux application responsible for receiving and processing sensor telemetry |
| Telemetry | Measurement and status information transmitted by a device |
| Sampling interval | Time between successive measurement acquisition attempts |
| Reporting interval | Time between scheduled telemetry transmissions |
| Valid message | A message satisfying the defined communication protocol |

## 2. System Overview

IMCS will initially use the following architecture:

STM32 Sensor Node → Serial Connection → Linux Gateway

The sensor node will acquire measurements from the BME280 and transmit telemetry to the gateway. The gateway will validate received messages and report communication or data errors.

Detailed implementation decisions and interface definitions will be maintained separately in `docs/architecture.md` and `docs/protocol.md`.

## 3. Functional Requirements

### 3.1 Sensor Node

**IMCS-FW-001 — Peripheral Initialization**

The sensor node shall initialize all peripherals required for sensor acquisition and telemetry transmission during startup.

**IMCS-FW-002 — Sensor Identification**

The sensor node shall verify the identity of the connected BME280 sensor before accepting measurements from it.

**IMCS-FW-003 — Environmental Measurements**

The sensor node shall acquire temperature, relative humidity, and atmospheric pressure measurements from the BME280.

**IMCS-FW-004 — Periodic Sampling**

The sensor node shall attempt to acquire a new set of environmental measurements at a configurable sampling interval.

**IMCS-FW-005 — Telemetry Transmission**

The sensor node shall transmit telemetry messages containing the most recent measurement results and their acquisition status at a configurable reporting interval.

**IMCS-FW-006 — Message Identification**

Each telemetry message shall include a device identifier and a sequence number.

**IMCS-FW-007 — Sensor Failure Reporting**

The sensor node shall report a sensor communication or acquisition failure without presenting the failed acquisition as a valid measurement.

### 3.2 Linux Gateway

**IMCS-GW-001 — Telemetry Reception**

The gateway shall receive telemetry messages from the sensor node through a serial interface.

**IMCS-GW-002 — Message Validation**

The gateway shall validate incoming telemetry messages against the defined communication protocol before accepting them for processing.

**IMCS-GW-003 — Invalid Message Handling**

The gateway shall reject malformed telemetry messages and record a diagnostic event identifying the rejection.

**IMCS-GW-004 — Reception Timestamp**

The gateway shall associate each accepted telemetry message with a reception timestamp.

**IMCS-GW-005 — Connection Monitoring**

The gateway shall report the sensor node as disconnected when no valid telemetry has been received within a configurable timeout period.

## 4. Quality and Constraint Requirements

**IMCS-PERF-001 — Telemetry Reporting Interval**

During normal operation, the sensor node shall transmit telemetry at intervals of 1,000 milliseconds ±100 milliseconds, measured over 100 consecutive transmissions.

**IMCS-REL-001 — Gateway Recovery**

Following an unexpected serial disconnection, the gateway shall attempt to restore communication without requiring an application restart.

**IMCS-DATA-001 — Measurement Units**

The telemetry protocol shall identify temperature in degrees Celsius, relative humidity in percent, and atmospheric pressure in pascals.

## 5. Assumptions and Dependencies

- The sensor node uses an STM32L476RG microcontroller.
- The initial environmental sensor is a BME280-compatible module.
- The initial gateway operates on Linux.
- The sensor node communicates with the gateway over a wired serial connection.
- The prototype is intended for laboratory and development use.
- The prototype is not intended for safety-critical equipment control.

## 6. Verification Approach

Each requirement shall be associated with a verification procedure documented in `docs/verification-matrix.md`.

Verification methods may include:

- Automated unit tests.
- Hardware integration tests.
- Communication fault injection.
- Timing measurements.
- Manual inspection and demonstrations.

Verification results shall be recorded with sufficient information to reproduce the test conditions.

## 7. Deferred Requirements

The following capabilities are planned for later development phases but are not yet specified in detail:

- Persistent telemetry storage.
- Backend REST API.
- Operator dashboard.
- Configurable alarms and alarm acknowledgment.
- Remote equipment control.
- Authentication and authorization.
- System-wide integration and performance testing.

These capabilities will receive individual requirements before implementation.

## 8. Revision History

| Version | Date | Description |
|---|---|---|
| 0.1 | 2026-11-02 | Initial draft covering sensor acquisition and gateway communication |

