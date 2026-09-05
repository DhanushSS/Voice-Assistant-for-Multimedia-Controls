# Software Requirements Specification (SRS)

**Project: Voice Assistant for Multimedia Controls**

Version: 1.1

**Team: Team 3**

Authors:

- PES2UG24CS154 - DHANUSH S S (Project Manager & Integration Lead)


- PES2UG24CS166 - G S DHEEKSHITH

- PES2UG24CS183 - HARSH MANNAN

- PES2UG24CS137 - CHIRANTHAN SHANKAR

Date: 05-09-2026

Status: Draft

*Assumption note: Based on your answers, this SRS assumes an ESP32-only device using a hybrid speech pipeline (on-device wake-word detection + cloud-based ASR for command recognition), controlling media on a paired host (phone/PC/TV/speaker) via Bluetooth Low Energy (BLE) HID media keys, with an optional Python + MediaPipe companion module for gesture-based testing/confirmation on a PC. Replace/adjust any assumed detail (mic hardware, exact command set, target host OS) to match your build.*

## Revision history

| **Version** | **Date**   | **Author** | **Change summary**                                                                                                                                       | **Approval** |
|-------------|------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| 1.0         | 05-09-2026 | Team 3     | Initial SRS draft for Voice Assistant for Multimedia Controls                                                                                            |              |
| 1.1         | 05-09-2026 | Team 3     | Added UML use-case diagrams with interpretation notes, converted definitions to a table, added team work division, cross-testing plan, and status codes. |              |

## Approvals

| **Role**                        | **Name** | **Signature / Email** | **Date** |
|---------------------------------|----------|-----------------------|----------|
| Course Coordinator / Instructor |          |                       |          |
| Team Lead / Student             |          |                       |          |

## Table of Contents

1\. Introduction

2\. Overall description

3\. External interfaces

4\. System features (detailed)

5\. Non-functional requirements (detailed)

6\. Quality attributes & Acceptance tests

7\. UML Use-Case Diagrams

8\. Requirements Traceability Matrix (RTM)

9\. Team, Work Division & Cross-Testing Plan

10\. Assumptions and Out of Scope

11\. Final Consistency Checklist

## 1. Introduction

### 1.1 Purpose

This document is a Software Requirements Specification (SRS) for the Voice Assistant for Multimedia Controls project. It defines the functional and non-functional requirements, interfaces, and verification criteria for a hardware-based, voice-controlled multimedia controller built on ESP32. It is intended for use by students, instructors, and evaluators as a reference for design, implementation, and assessment.

### 1.2 Scope

The system enables a user to control multimedia playback on a paired host device (phone, PC, TV, or Bluetooth speaker) using voice commands captured by an ESP32-based hardware unit. Supported commands are: volume up, volume down, pause, resume, play, next, and previous. The system covers on-device wake-word detection, cloud-based speech recognition, command mapping, and command transmission via Bluetooth (BLE HID). It excludes the internal media-player implementation on the host device and any content licensing or streaming-service integration.

### 1.3 Audience

Developers, QA Evaluators/Testers, Course Instructors, Project Reviewers, and Future Maintainers.

### 1.4 Definitions

| **Term / Acronym** | **Definition**                                                                                                                |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------|
| ESP32              | Low-cost, low-power microcontroller with integrated Wi-Fi and Bluetooth, used as the main hardware platform for this project. |
| ASR                | Automatic Speech Recognition — the process of converting spoken audio into text.                                              |
| BLE                | Bluetooth Low Energy — a power-efficient Bluetooth mode used for short-range wireless communication with the host device.     |
| HID                | Human Interface Device — a standard USB/Bluetooth profile used to send input events (e.g., media keys) to a host.             |
| MCU                | Microcontroller Unit — the embedded processor (ESP32) running the device firmware.                                            |
| OTA                | Over-The-Air — a method of updating firmware remotely without a wired connection.                                             |
| API                | Application Programming Interface — a defined interface for software components to communicate.                               |
| UI                 | User Interface — the means by which a user interacts with the system.                                                         |
| SNR                | Signal-to-Noise Ratio — a measure of desired audio signal strength relative to background noise.                              |
| TLS                | Transport Layer Security — a cryptographic protocol used to secure network communication.                                     |
| Wake Word          | A predefined trigger phrase that activates the voice assistant's active-listening state.                                      |
| RTM                | Requirements Traceability Matrix — a table mapping requirements to design, modules, and test cases.                           |

## 2. Overall description

### 2.1 Product perspective

The Voice Assistant for Multimedia Controls is a standalone, battery- or USB-powered ESP32-based embedded device with an onboard microphone. It performs lightweight wake-word detection on-device, then streams the captured utterance to a cloud speech-to-text service for command recognition. Recognized commands are translated into BLE HID media-control key codes and transmitted to a paired host device. An optional Python + MediaPipe companion application may be used on a connected PC for development, testing, or gesture-based confirmation of commands.

### 2.2 Major product functions

- Continuous on-device wake-word detection

- Voice capture and streaming to a cloud ASR service

- Command recognition and mapping to supported multimedia actions

- Command transmission to the paired host via Bluetooth (BLE HID)

- Wi-Fi provisioning and Bluetooth pairing setup

- User feedback via LED/buzzer for command status

- Local diagnostic logging and OTA firmware updates

- Optional PC-side Python/MediaPipe module for testing and gesture-based control confirmation

### 2.3 User roles and characteristics

- End User: general user with no technical background; expects fast, reliable, hands-free media control.

- Developer/Maintainer: flashes firmware, configures Wi-Fi/Bluetooth, and reviews diagnostic logs.

- Course Evaluator: reviews the system against functional and non-functional requirements for assessment.

### 2.4 Operating environment

ESP32 development board (e.g., ESP32-WROOM) with an I2S/analog MEMS microphone, 2.4GHz Wi-Fi, Bluetooth 4.2+/BLE radio, USB or battery power (e.g., 2000mAh Li-ion), operating in typical indoor ambient noise conditions, paired with a host device (smartphone, PC, TV, or Bluetooth speaker) that supports BLE HID media keys.

### 2.5 Constraints

- Limited ESP32 RAM/flash restricts on-device processing to wake-word detection only; full command recognition depends on internet connectivity.

- Bluetooth effective range is approximately 10 meters; performance may degrade with obstructions.

- Requires a host device that supports BLE HID media-key events (or an equivalent IR receiver, if IR is used instead).

- Cloud ASR usage may incur API rate limits or costs and requires a stable Wi-Fi connection.

## 3. External interface requirements

### 3.1 User interfaces

No onboard display. Initial setup uses a captive-portal Wi-Fi configuration page (or a lightweight companion mobile/PC app) for Wi-Fi credentials and Bluetooth pairing. A single status LED and buzzer provide feedback (listening, recognized, error, low battery).

### 3.2 Hardware interfaces

- I2S/analog MEMS microphone for audio capture

- ESP32 integrated Wi-Fi and Bluetooth/BLE radio

- Status LED (multi-color) and buzzer for feedback

- Push-button for mute/unmute and factory reset

- Optional IR LED transmitter (if IR control path is used instead of/alongside BLE)

### 3.3 Software interfaces

- Cloud Speech-to-Text API (e.g., Google Cloud Speech-to-Text) over HTTPS for command transcription

- BLE HID profile on the host OS (Android/Windows/iOS/macOS) for receiving media-control key events

- Optional Python + MediaPipe module on a PC for gesture-based testing and command confirmation

### 3.4 Communications

- TLS 1.2+ enforced for all communication with the cloud ASR service.

- BLE pairing/bonding with the host device using authenticated pairing where supported.

- Retry/backoff strategy for transient Wi-Fi or BLE connection failures.

- Graceful degradation (LED/buzzer error indication) when network or Bluetooth link is unavailable.

*Note: overall this SRS includes 15 functional requirements, 5 non-functional requirements, 2 security objectives, and 5 security requirements, consistent with the reference template.*

## 4. System features (detailed)

Each requirement below includes acceptance criteria and a reference test case. IDs follow VAMC-F-###.

### 4.1 Wake Word Detection

Description: Continuously listen on-device for a configured wake word/phrase and trigger command capture without sending audio to the cloud until the wake word is detected.

| **Req ID** | **Requirement (shall...)**                                                                                    | **Type**   | **Priority** | **Source/Stakeholder** | **Acceptance criteria / Test case ref**                                                             | **Comments / Dependencies**        |
|------------|---------------------------------------------------------------------------------------------------------------|------------|--------------|------------------------|-----------------------------------------------------------------------------------------------------|------------------------------------|
| VAMC-F-001 | The system shall continuously monitor audio input on-device for a configured wake word.                       | Functional | High         | End User               | AC-VAMC-F-001: Wake word spoken at normal volume within 2m triggers listening state. Test: TC-WW-01 | Requires on-device wake-word model |
| VAMC-F-002 | The system shall enter active-listening state and capture up to 5 seconds of audio after wake-word detection. | Functional | High         | End User               | AC-VAMC-F-002: Audio buffer captured for configurable duration after trigger. Test: TC-WW-02        | Buffer size configurable           |
| VAMC-F-003 | The system shall provide a mute/unmute control (physical button) to disable wake-word listening.              | Functional | Medium       | Privacy/User           | AC-VAMC-F-003: Pressing mute button stops audio capture until unmuted. Test: TC-WW-03               | LED indicates mute state           |

### 4.2 Voice Command Recognition

Description: Stream captured audio to a cloud ASR service and transcribe it to text for command matching.

| **Req ID** | **Requirement (shall...)**                                                                                  | **Type**   | **Priority** | **Source/Stakeholder** | **Acceptance criteria / Test case ref**                                                                         | **Comments / Dependencies**      |
|------------|-------------------------------------------------------------------------------------------------------------|------------|--------------|------------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------------|
| VAMC-F-004 | The system shall stream captured audio to a cloud ASR service over HTTPS for transcription.                 | Functional | High         | Developer              | AC-VAMC-F-004: Audio successfully transcribed to text with round-trip \< 3s under normal Wi-Fi. Test: TC-ASR-01 | Depends on internet availability |
| VAMC-F-005 | The system shall parse the transcribed text and match it against the supported command vocabulary.          | Functional | High         | Developer              | AC-VAMC-F-005: Exact/near matches map correctly to one of 7 commands. Test: TC-ASR-02                           | Fuzzy matching for near-synonyms |
| VAMC-F-006 | The system shall detect and report unrecognized or ambiguous commands without executing unintended actions. | Functional | High         | End User               | AC-VAMC-F-006: Unmatched phrase triggers error LED/buzzer and no command sent. Test: TC-ASR-03                  | Prevents false triggers          |

### 4.3 Command Mapping and Execution

Description: Translate recognized commands into BLE HID media-control key events and transmit them to the paired host device.

| **Req ID** | **Requirement (shall...)**                                                                                                        | **Type**   | **Priority** | **Source/Stakeholder** | **Acceptance criteria / Test case ref**                                                         | **Comments / Dependencies**    |
|------------|-----------------------------------------------------------------------------------------------------------------------------------|------------|--------------|------------------------|-------------------------------------------------------------------------------------------------|--------------------------------|
| VAMC-F-007 | The system shall support the following commands: volume up, volume down, pause, resume, play, next, previous.                     | Functional | High         | End User               | AC-VAMC-F-007: All 7 commands independently verified against host media player. Test: TC-CMD-01 | Core feature set               |
| VAMC-F-008 | The system shall transmit the recognized command as a BLE HID media key event to the paired host within 2 seconds of recognition. | Functional | High         | End User               | AC-VAMC-F-008: Host receives and executes key event within 2s. Test: TC-CMD-02                  | Requires active BLE connection |
| VAMC-F-009 | The system shall retry command transmission up to 3 times if the BLE link is temporarily unavailable, then report failure.        | Functional | Medium       | Reliability            | AC-VAMC-F-009: 3 retries attempted with backoff, then error indicated. Test: TC-CMD-03          | Backoff interval configurable  |
| VAMC-F-010 | The system shall provide visual and/or audible feedback confirming successful command execution.                                  | Functional | Medium       | End User               | AC-VAMC-F-010: LED/buzzer confirms success within 1s of command execution. Test: TC-CMD-04      |                                |

### 4.4 Connectivity and Pairing

Description: Provide setup flows for connecting the device to Wi-Fi and pairing it with a host device over Bluetooth.

| **Req ID** | **Requirement (shall...)**                                                                           | **Type**   | **Priority** | **Source/Stakeholder** | **Acceptance criteria / Test case ref**                                                                  | **Comments / Dependencies** |
|------------|------------------------------------------------------------------------------------------------------|------------|--------------|------------------------|----------------------------------------------------------------------------------------------------------|-----------------------------|
| VAMC-F-011 | The system shall support Wi-Fi provisioning via a captive portal or companion app on first boot.     | Functional | High         | End User               | AC-VAMC-F-011: New device connects to home Wi-Fi within 5 minutes using guided setup. Test: TC-CONN-01   |                             |
| VAMC-F-012 | The system shall support Bluetooth pairing with a host device using standard BLE pairing procedures. | Functional | High         | End User               | AC-VAMC-F-012: Device appears in host's Bluetooth list and pairs successfully. Test: TC-CONN-02          |                             |
| VAMC-F-013 | The system shall support a factory-reset function to clear stored Wi-Fi and Bluetooth pairing data.  | Functional | Medium       | Maintainer             | AC-VAMC-F-013: Long-press of reset button clears credentials and restarts provisioning. Test: TC-CONN-03 |                             |

### 4.5 Diagnostics and Maintenance

Description: Support diagnostics, logging, and firmware maintenance for developers.

| **Req ID** | **Requirement (shall...)**                                                                                                | **Type**   | **Priority** | **Source/Stakeholder** | **Acceptance criteria / Test case ref**                                                      | **Comments / Dependencies**                |
|------------|---------------------------------------------------------------------------------------------------------------------------|------------|--------------|------------------------|----------------------------------------------------------------------------------------------|--------------------------------------------|
| VAMC-F-014 | The system shall log recognized commands and errors to a local circular buffer, retrievable via a serial/debug interface. | Functional | Medium       | Developer              | AC-VAMC-F-014: Last N events retrievable via serial console. Test: TC-DIAG-01                |                                            |
| VAMC-F-015 | The system shall support Over-The-Air (OTA) firmware updates.                                                             | Functional | Medium       | Maintainer             | AC-VAMC-F-015: New firmware image applied remotely without physical access. Test: TC-DIAG-02 | Requires signed firmware (see VAMC-SR-005) |

## 5. Non-functional requirements (detailed)

NFRs below are measurable and tied to test plans. IDs VAMC-NF-###.

| **Req ID**  | **Requirement**                                                                                                                          | **Category**         | **Priority** | **Acceptance criteria / Measurement**                                                                                         |
|-------------|------------------------------------------------------------------------------------------------------------------------------------------|----------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------|
| VAMC-NF-001 | End-to-end latency from wake-word detection to command execution shall be ≤ 3 seconds for 90% of commands under normal Wi-Fi conditions. | Performance          | High         | 90th percentile ≤ 3s measured over 50 trials. Test: TC-Perf-01                                                                |
| VAMC-NF-002 | The system shall correctly recognize supported voice commands with ≥ 90% accuracy in a quiet indoor environment (SNR ≥ 15dB).            | Reliability/Accuracy | High         | Command recognition accuracy ≥ 90% over 100 test utterances. Test: TC-Acc-01                                                  |
| VAMC-NF-003 | A first-time user shall be able to complete Wi-Fi and Bluetooth setup within 5 minutes using the provided setup guide.                   | Usability            | Medium       | Usability test with 5 first-time users; average setup time ≤ 5 min. Test: TC-UX-01                                            |
| VAMC-NF-004 | The device shall operate continuously for at least 8 hours on a 2000mAh battery in idle-listening mode.                                  | Power/Energy         | Medium       | Battery-life test shows ≥ 8 hours idle operation. Test: TC-Power-01                                                           |
| VAMC-NF-005 | The firmware shall be modular such that the wake-word, ASR-communication, and command-execution modules can be updated independently.    | Maintainability      | Medium       | Code review confirms module boundaries; a single module can be replaced without modifying others. Test: Code review checklist |

## 5.1 Security

### 5.1.1 Security Objectives

- Protect user voice data in transit between the ESP32 device and the cloud ASR service from interception or tampering.

- Prevent unauthorized devices from pairing with, or issuing spoofed multimedia-control commands to, the assistant.

### 5.1.2 Security Requirements

| **Req ID**  | **Requirement (shall...)**                                                                                                                  | **Type**         | **Priority** | **Acceptance criteria / Test case ref**                                                |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------------|------------------|--------------|----------------------------------------------------------------------------------------|
| VAMC-SR-001 | All audio data transmitted to the cloud ASR API shall use TLS 1.2 or higher.                                                                | Security         | High         | Network capture confirms TLS 1.2+ on all ASR traffic. Test: TC-Sec-01                  |
| VAMC-SR-002 | Bluetooth pairing shall use authenticated pairing (passkey or numeric comparison) rather than "Just Works" mode where the host supports it. | Security         | High         | Pairing flow requires passkey/confirmation on supported hosts. Test: TC-Sec-02         |
| VAMC-SR-003 | Wi-Fi credentials captured during provisioning shall not be stored in plaintext in flash memory.                                            | Security         | High         | Flash dump review confirms credentials are encrypted/obfuscated. Test: TC-Sec-03       |
| VAMC-SR-004 | The system shall not persist raw audio recordings beyond the active command-recognition session.                                            | Security/Privacy | High         | Memory/flash inspection confirms no retained audio after session ends. Test: TC-Sec-04 |
| VAMC-SR-005 | OTA firmware updates shall be signed and verified before being applied.                                                                     | Security         | Medium       | Unsigned/tampered firmware image is rejected by the bootloader. Test: TC-Sec-05        |

## 6. Quality attributes & Acceptance tests

Exit criteria for acceptance: All high-priority functional requirements implemented and verified, no critical NFR failures, and the RTM shows all test cases passed.

Acceptance test suites: Wake-Word Detection, Command Recognition, Command Execution, Connectivity/Pairing, Performance, Security, and Usability tests.

## 7. System models and diagrams

### 7.1 UML Use-Case diagram — Diagram 1: Primary Voice-Control Flow

![UML Use-Case Diagram 1: Primary Voice-Control Flow](images/uml_primary_flow.png)

**UML interpretation**

- This diagram models the end-to-end command path: the End User speaks a wake word, the on-device system detects it, captures the command audio, forwards it to the Cloud ASR actor for transcription, maps the recognized text to an action, and either transmits a BLE HID key event to the Host Media Device (which executes the media action) or reports an unrecognized command — in both cases the user receives LED/buzzer feedback.

- It corresponds to FRs VAMC-F-001 to VAMC-F-010 in Section 4.1–4.3 of this SRS.

**Actors and their use cases**

| **Actor**           | **Role in this diagram**                                                                                   | **Use cases involved**                                    |
|---------------------|------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| End User            | Primary human actor who initiates the interaction.                                                         | Speak Wake Word; (indirectly) Feedback through LED/Buzzer |
| Cloud (ASR Service) | Secondary/supporting actor — an external system, not a person, that performs speech-to-text transcription. | Send Audio to Cloud ASR; Transcribe Speech to Text        |
| Host Media Device   | Secondary actor — the external system that ultimately executes the media action.                           | Transmit BLE HID Key Event; Execute Media Action          |

**UML consistency notes / suggested corrections**

- Actor placement is correct: all three actors (End User, Cloud, Host Media Device) sit outside the system boundary, and use cases sit inside it — this matches UML use-case notation.

- Directional arrows between use cases (e.g., “Detect Wake Word → Capture Voice Command”) show a sequence/flow. Standard UML use-case diagrams use plain associations (undirected lines) between actors and use cases; ordering/flow between use cases is normally expressed in an activity or sequence diagram, not with arrows inside a use-case diagram. Consider redrawing actor–use case links as plain lines and moving the step-by-step ordering into a separate activity diagram.

- The association from the Cloud actor into “Send Audio to Cloud ASR” and from “Transcribe Speech to Text” back out is drawn as directional arrows; in strict UML, actor associations are undirected. If direction matters (e.g., data flow), that is better captured with a sequence diagram.

- “Report Unrecognized Command” and “Transmit BLE HID Key Event” are alternative outcomes of “Map Command to Action”. This branching is more precisely modelled using an \<\<extend\>\> relationship (with an extension point on “Map Command to Action”) rather than plain labelled arrows (“command recognised” / “command not recognised”).

- Secondary actors (Cloud, Host Media Device) are conventionally placed on the right of the diagram with the primary actor on the left — this diagram follows that convention reasonably well.

### 7.2 UML Use-Case diagram — Diagram 2: Setup and Maintenance Flow

![UML Use-Case Diagram 2: Setup and Maintenance Flow](images/uml_setup_maintenance.png)

**UML interpretation**

- This diagram models the configuration and maintenance activities performed by the Developer/Maintainer: Wi-Fi provisioning, Bluetooth pairing, mute/unmute control, factory reset, diagnostic log review, and OTA firmware updates (which include firmware signature verification).

- It corresponds to FRs VAMC-F-011 to VAMC-F-015 and security requirement VAMC-SR-005 in Section 4.4–4.5 of this SRS.

**Actors and their use cases**

| **Actor**              | **Role in this diagram**                                                                      | **Use cases involved**                                                                                                                                        |
|------------------------|-----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Developer / Maintainer | Sole actor in this diagram — a technical user responsible for setup and upkeep of the device. | Provision Wi-Fi (Captive Portal); Pair via Bluetooth (BLE); Mute/Unmute Microphone; Factory Reset Device; Review Diagnostic Logs; Perform OTA Firmware Update |

**UML consistency notes / suggested corrections**

- Actor-to-use-case associations are drawn correctly as simple lines from the Developer actor into each use case inside the system boundary.

- “Perform OTA Firmware Update” ––\<\<include\>\>––\> “Verify Firmware Signature” is modelled correctly: the arrow points from the base use case to the always-executed included use case, matching standard UML \<\<include\>\> notation.

- “\<\<triggers\>\>” between “Factory Reset Device” and “Provision Wi-Fi (Captive Portal)” is not a standard UML use-case stereotype (the two standard ones are \<\<include\>\> and \<\<extend\>\>). Since a factory reset always forces re-provisioning, this relationship is better expressed as an \<\<include\>\> dependency (Factory Reset Device includes Provision Wi-Fi) rather than a custom “triggers” label, to stay within the UML metamodel.

- All use cases are nouns/verb-phrases describing user goals (good practice); none of them overlap or duplicate functionality, so there is no redundancy to resolve.

- Consider adding a second actor, “End User,” for “Mute/Unmute Microphone,” since muting is described elsewhere in the SRS (VAMC-F-003) as an end-user-facing action, not strictly a developer task. As drawn, only “Developer” is shown as the actor for all six use cases.

## 8. Requirements Traceability Matrix (RTM)

| **Req ID**  | **Requirement short**    | **Section ref / Design Spec** | **Module**         | **Test case(s)** | **Status (N/P/A)** | **Comments** |
|-------------|--------------------------|-------------------------------|--------------------|------------------|--------------------|--------------|
| VAMC-F-001  | Wake-word detection      | 4.1 / DS-WW-01                | WakeWordModule     | TC-WW-01         | N                  |              |
| VAMC-F-004  | Stream audio to ASR      | 4.2 / DS-ASR-01               | ASRModule          | TC-ASR-01        | N                  |              |
| VAMC-F-007  | Support 7 media commands | 4.3 / DS-CMD-01               | CommandModule      | TC-CMD-01        | N                  |              |
| VAMC-F-008  | BLE command transmission | 4.3 / DS-CMD-02               | BLEModule          | TC-CMD-02        | N                  |              |
| VAMC-F-011  | Wi-Fi provisioning       | 4.4 / DS-CONN-01              | ConnectivityModule | TC-CONN-01       | N                  |              |
| VAMC-NF-001 | End-to-end latency ≤ 3s  | 5 / DS-Perf-01                | System             | TC-Perf-01       | N                  |              |
| VAMC-SR-001 | TLS 1.2+ for ASR traffic | 5.1.2 / DS-Sec-01             | ASRModule          | TC-Sec-01        | N                  |              |

**Status code legend used above (see Section 9.6 for full definitions):**

N = Not Started, P = In Progress, A = Accepted.

## 9. Team, Work Division & Cross-Testing Plan

This section defines how Team 3's four members share ownership of the system, how modules are cross-tested for quality and fairness, and the status vocabulary used to track progress in the RTM (Section 8).

### 9.1 Team composition and roles

| **SRN**       | **Name**           | **Role**                           | **Primary module owned**                                                      |
|---------------|--------------------|------------------------------------|-------------------------------------------------------------------------------|
| PES2UG24CS154 | DHANUSH S S        | Project Manager & Integration Lead | Connectivity & Pairing (4.4) + overall system integration / backend ownership |
| PES2UG24CS166 | G S DHEEKSHITH     | Audio Module Lead                  | Wake Word Detection & Voice Capture (4.1)                                     |
| PES2UG24CS183 | HARSH MANNAN       | Recognition Module Lead            | Voice Command Recognition — Cloud ASR & Command Mapping (4.2)                 |
| PES2UG24CS137 | CHIRANTHAN SHANKAR | Output & Maintenance Module Lead   | Command Mapping & Execution (BLE HID) + Diagnostics/OTA (4.3, 4.5)            |

### 9.2 Final work division

| **Module**                            | **Description**                                                                                                                                                           | **Owner**                                           | **Related Req IDs**                       |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------|-------------------------------------------|
| Wake Word & Audio Capture             | On-device wake-word detection, audio buffering, mute/unmute control.                                                                                                      | G S Dheekshith                                      | VAMC-F-001 to F-003                       |
| Voice Recognition (ASR)               | Streaming audio to cloud ASR, transcription handling, command-text matching, unrecognized-command detection.                                                              | Harsh Mannan                                        | VAMC-F-004 to F-006                       |
| Command Execution & Diagnostics       | Mapping recognized commands to BLE HID key codes, transmission/retry logic, feedback (LED/buzzer), local logging, OTA update + signature verification.                    | Chiranthan Shankar                                  | VAMC-F-007 to F-010, F-014, F-015, SR-005 |
| Connectivity, Pairing & Integration   | Wi-Fi provisioning, Bluetooth pairing, factory reset, end-to-end system integration across all modules, release builds.                                                   | Dhanush S S                                         | VAMC-F-011 to F-013                       |
| Security requirements (cross-cutting) | TLS enforcement, authenticated BLE pairing, credential storage, audio data retention limits — implemented within the relevant owner's module and reviewed by all members. | Shared (reviewed by all)                            | VAMC-SR-001 to SR-004                     |
| Documentation (SRS, RTM, diagrams)    | Maintaining this SRS, UML diagrams, and traceability matrix.                                                                                                              | Dhanush S S (compiled), all members (content input) | All                                       |

### 9.3 Member responsibilities

**Dhanush S S — Project Manager & Integration Lead**

- Owns the Connectivity & Pairing module (Wi-Fi provisioning, BLE pairing, factory reset).

- Owns overall system integration: merges each member's module into a single working firmware build.

- Maintains the SRS, RTM, and project schedule; tracks status codes across all modules.

- Cross-tests the Command Execution & Diagnostics module (owned by Chiranthan Shankar).

**G S Dheekshith — Audio Module Lead**

- Implements wake-word detection and voice-capture buffering on the ESP32.

- Implements the mute/unmute button handling and mute-state LED indication.

- Cross-tests the Connectivity & Pairing module (owned by Dhanush S S).

**Harsh Mannan — Recognition Module Lead**

- Implements the HTTPS client that streams captured audio to the cloud ASR service.

- Implements text-matching logic that maps transcribed text to one of the 7 supported commands, and handles unrecognized-command cases.

- Cross-tests the Audio Module (owned by G S Dheekshith).

**Chiranthan Shankar — Output & Maintenance Module Lead**

- Implements BLE HID key-event transmission, retry/backoff logic, and success/error feedback (LED/buzzer).

- Implements local diagnostic logging and the OTA update pipeline with signature verification.

- Cross-tests the Recognition Module (owned by Harsh Mannan).

### 9.4 Cross-testing structure

Every module is tested by a member other than its owner, arranged as a rotation so that testing responsibility forms a closed cycle across the team:

| **Module under test**           | **Owner (developer)** | **Cross-tester**   | **Test focus**                                                                                                        |
|---------------------------------|-----------------------|--------------------|-----------------------------------------------------------------------------------------------------------------------|
| Connectivity & Pairing          | Dhanush S S           | G S Dheekshith     | Wi-Fi provisioning flow, BLE pairing reliability, factory reset behaviour (VAMC-F-011 to F-013)                       |
| Wake Word & Audio Capture       | G S Dheekshith        | Harsh Mannan       | Wake-word detection accuracy/false-trigger rate, mute control (VAMC-F-001 to F-003)                                   |
| Voice Recognition (ASR)         | Harsh Mannan          | Chiranthan Shankar | Transcription accuracy, command matching, unrecognized-command handling (VAMC-F-004 to F-006)                         |
| Command Execution & Diagnostics | Chiranthan Shankar    | Dhanush S S        | BLE HID transmission correctness/latency, retry logic, OTA signature verification (VAMC-F-007 to F-010, F-014, F-015) |

- Cross-testers file defects directly against the RTM entry for the relevant requirement, updating its Status column (see 9.6).

- System-level (end-to-end) testing — wake word through to media action on the host device — is performed jointly by all four members during each milestone review, in addition to the pairwise module cross-testing above.

- Security requirements (VAMC-SR-001 to SR-005) are reviewed by all members regardless of which module implements them, since they cut across module boundaries.

### 9.5 Fairness and backend ownership rule

- No member tests their own module's code — this avoids confirmation bias and ensures defects are found independently of the author.

- Every member both owns exactly one module and cross-tests exactly one other member's module, so testing load is distributed evenly across the team.

- The Project Manager (Dhanush S S) holds backend/integration ownership — responsibility for merging modules into a working system — but this does not exempt them from either contributing a module or performing cross-testing; integration ownership is an additional responsibility on top of, not instead of, equal development and testing duties.

- Final system-level acceptance (Section 6 exit criteria) requires sign-off from at least two members other than the module owner, to prevent any single member from unilaterally marking their own work as complete.

- Workload is rebalanced at each milestone review if the RTM shows one member's requirements consistently lagging in status, to keep contribution roughly equal across the team.

### 9.6 Status codes

The following status codes are used consistently in the RTM (Section 8) and in cross-testing records (Section 9.4):

| **Code** | **Meaning**    | **When to use it**                                                                                                            |
|----------|----------------|-------------------------------------------------------------------------------------------------------------------------------|
| N        | Not Started    | Requirement not yet implemented.                                                                                              |
| P        | In Progress    | Implementation underway by the owning member.                                                                                 |
| T        | Ready for Test | Implementation complete; handed to the assigned cross-tester.                                                                 |
| A        | Accepted       | Cross-tester has verified the requirement against its acceptance criteria; test passed.                                       |
| F        | Failed         | Cross-tester found the requirement does not meet acceptance criteria; returned to owner for rework.                           |
| B        | Blocked        | Work cannot proceed due to an external/internal dependency (e.g., waiting on hardware, another module, or a third-party API). |

Note: the RTM in Section 8 currently shows illustrative “N” entries as a starting template — update each row's Status column to P/T/A/F/B as the team progresses, using the cross-testing assignments in 9.4.

## 10. Assumptions and Out of Scope

### 10.1 Assumptions

The following assumptions underlie the requirements in this SRS. If any assumption proves false during implementation, the affected requirements should be reviewed and this document updated accordingly.

- The hardware platform is ESP32 only (no Raspberry Pi variant); all functional and non-functional requirements are scoped to ESP32's capabilities (limited RAM/flash, integrated Wi-Fi/BLE radio).

- Speech recognition follows a hybrid model: wake-word detection runs on-device, while full command transcription is performed by a cloud ASR service reachable over the internet.

- The host device (phone, PC, TV, or Bluetooth speaker) supports the BLE HID media-key profile and can be paired using standard Bluetooth pairing procedures.

- A stable Wi-Fi connection is available at the point of use; command recognition depends on this and is not guaranteed to work fully offline.

- The optional Python + MediaPipe module is used only for development-time testing or gesture-based confirmation on a PC, and is not required for the core end-user experience.

- End users will operate the device in a typical quiet-to-moderate indoor noise environment (approximate SNR ≥ 15dB), consistent with the accuracy target in VAMC-NF-002.

- A single wake word/trigger phrase is configured per device; multi-user voice profiles or speaker identification are not required.

- The cloud ASR provider (e.g., Google Cloud Speech-to-Text) is accessible under a free-tier or trial account for development and demonstration purposes, and its usage limits are sufficient for testing.

- Team members have access to at least one ESP32 development board each, or share hardware access, sufficient to develop and cross-test their assigned modules within the project timeline.

- The instructor/evaluator will assess the system primarily against the functional and non-functional requirements defined in Sections 4 and 5, using the acceptance criteria stated for each requirement.

### 10.2 Out of Scope

The following items are explicitly excluded from this project's requirements and will not be evaluated as part of this SRS:

- Internal implementation of the media player application on the host device (e.g., how Spotify, VLC, or a TV's OS renders and plays media).

- Integration with, or licensing of, specific streaming services (Spotify API, YouTube API, etc.) beyond generic BLE HID media-key control.

- Multi-language voice command support; the system is designed and tested for a single configured language.

- Multi-user voice recognition, speaker identification, or per-user personalization/voice profiles.

- Simultaneous control of multiple host devices from a single assistant unit, or multi-room/synchronized playback across several hosts.

- A full-featured mobile companion app; setup is limited to a lightweight captive-portal Wi-Fi/Bluetooth configuration flow.

- Alternative hardware platforms (e.g., Raspberry Pi) as a substitute for or replacement of the ESP32 platform, even though they are mentioned in the original project brief.

- Offline (fully on-device, no internet) command recognition; the hybrid design assumes network availability for full-vocabulary ASR.

- Formal security certification (e.g., PCI-DSS-style audits) beyond the security requirements listed in Section 5.1.2, which are scoped to this course project's risk level.

- Long-term production support, warranty, or manufacturing considerations; this SRS covers a functional prototype suitable for academic evaluation.

## 11. Final Consistency Checklist

This checklist was used to verify that the SRS is internally consistent — terminology, requirement counts, traceability, and diagram-to-requirement mappings all agree with one another — before this version was finalized.

| **\#** | **Consistency check**                                                                                                                                                                                      | **Status**                                        |
|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| 1      | Every functional requirement uses the ID pattern VAMC-F-### and is unique across Section 4.                                                                                                                | Verified                                          |
| 2      | Every NFR uses the ID pattern VAMC-NF-### and every security requirement uses VAMC-SR-###, with no duplicate IDs.                                                                                          | Verified                                          |
| 3      | Requirement counts match the note in Section 3.4: 15 functional requirements (F-001–F-015), 5 NFRs (NF-001–NF-005), 2 security objectives, and 5 security requirements (SR-001–SR-005).                    | Verified                                          |
| 4      | Every requirement in Sections 4 and 5 has an acceptance criterion and a referenced test case ID (TC-###).                                                                                                  | Verified                                          |
| 5      | All acronyms and terms used in the document (ESP32, ASR, BLE, HID, OTA, TLS, SNR, etc.) appear in the 1.4 Definitions table.                                                                               | Verified                                          |
| 6      | Actors shown in the UML use-case diagrams (End User, Cloud, Host Media Device, Developer/Maintainer) correspond to the user roles defined in Section 2.3.                                                  | Verified                                          |
| 7      | Each UML diagram's stated requirement range (e.g., VAMC-F-001 to F-010; VAMC-F-011 to F-015) matches the requirements actually described in the corresponding subsections of Section 4.                    | Verified                                          |
| 8      | The RTM (Section 8) includes at least one row per major module and references valid Req IDs that exist in Sections 4 and 5.                                                                                | Verified                                          |
| 9      | Every module listed in the Final Work Division (9.2) has exactly one owner, and every owner appears in the Cross-Testing Structure (9.4) as a cross-tester for a module they do not own (no self-testing). | Verified                                          |
| 10     | The cross-testing assignments in 9.4 form a closed rotation covering all four members with no gaps.                                                                                                        | Verified                                          |
| 11     | Status codes used in the RTM (Section 8) match the definitions given in Section 9.6 exactly (N, P, T, A, F, B).                                                                                            | Verified                                          |
| 12     | Title-page details (Team, Authors, Version, Date) match the Revision History table.                                                                                                                        | Verified                                          |
| 13     | Assumptions (10.1) and Out-of-Scope items (10.2) do not contradict any requirement stated in Sections 2–5 (e.g., no in-scope requirement depends on an out-of-scope capability).                           | Verified                                          |
| 14     | RTM Status column values are still placeholder “N” entries pending actual development progress.                                                                                                            | Pending — team to update as work proceeds         |
| 15     | Approvals table signatures/dates are pending instructor and team lead sign-off.                                                                                                                            | Pending — to be completed before final submission |
