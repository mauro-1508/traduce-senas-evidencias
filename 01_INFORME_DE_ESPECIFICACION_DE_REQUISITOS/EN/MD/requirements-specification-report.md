**REQUIREMENTS SPECIFICATION REPORT**

**PROJECT: Traduce Señas**

**TEAM MEMBERS:**

**Luis Alejandro Duarte Aldana**

**Juan Mauricio Suaza Solorzano**

**Juan Camilo Fierro Esquivel**

**NATIONAL LEARNING SERVICE – SENA**

**Software Analysis and Development**

2026

**Table of Contents**

# Introduction

This report presents the requirements specification for the Traduce Señas software, developed as a training project for the Technologist in Software Analysis and Development program at the National Learning Service (SENA). Its purpose is to document, in a verifiable and unambiguous way, what the system must do and at what quality level, so that it serves as an agreement among the development team, the instructor, and future users.

This document follows the structure recommended by the IEEE 830-1998 standard (Institute of Electrical and Electronics Engineers [IEEE], 1998): it first presents the project context, then describes the product in general terms, and finally details the functional requirements, nonfunctional requirements, and use cases. The requirements described here are the basis for the analysis, design, testing, and implementation documents that accompany this deliverable.

# General project information

## Problem statement

Deaf people and people with hearing disabilities face persistent barriers when communicating with the hearing population. In Colombia, the number of certified LSC interpreters is far below demand, and availability is even lower in rural areas, public entities, and unplanned everyday situations (Instituto Nacional para Sordos [INSOR], 2020). As a result, deaf people lose autonomy, and their participation in education, employment, and health services is reduced.

In response to this situation, there is a need for a low-cost technological solution, accessible on a smartphone, that translates signs into text or speech without requiring an interpreter in everyday interactions.

## Justification

The project promotes social inclusion and equal opportunities for deaf people, in line with Ley 982 de 2005, which recognizes LSC and requires the elimination of communication barriers. It also leverages mature computer vision technologies, such as MediaPipe Hands (Zhang et al., 2020), which make it possible to detect the hand in real time from the camera of a common device, making development feasible for a small team.

## General objective

Design and implement a mobile and web application that translates Colombian Sign Language into text or audio in real time, using computer vision and artificial intelligence, to reduce the communication barriers faced by deaf people in everyday settings.

## Specific objectives

- Build a tool that recognizes LSC signs and delivers the translation immediately.

- Convert the recognized signs into written or spoken words that any hearing person can understand.

- Enable communication between deaf and hearing people in situations such as the classroom, a store, or a service counter.

- Validate the tool in real-world scenarios to confirm that it is accurate and useful.

- Contribute to a more inclusive society in which hearing ability does not determine who can communicate.

## Scope

The system comprises an application for Android, iOS, and web browsers that recognizes signs through the camera and delivers the translation as text or audio. It includes account management with roles and permissions, translation history, sign language alphabet lookup (with 3D models), visual notifications, an achievements module, usage statistics for administrators, sample capture and recognition training, an audit trail, error logging, and a configurable password policy.

The following are out of scope for this version: reverse translation (from text or speech to signs), full coverage of the LSC vocabulary, and offline use without an internet connection.

## Personnel involved

**Table 1**

*Project team*

| **Name** | **Role** | **Responsibility** | **Contact** |
|---|---|---|---|
| Juan Mauricio Suaza Solórzano | Technical lead, Scrum Master, analyst, and developer | Analysis, design, backend programming (lexicon and recognition), and coordination | suazasolorzano@gmail.com |
| Luis Alejandro Duarte Aldana | Analyst and developer | Analysis, interface design, and programming | aleosea777@gmail.com |
| Juan Camilo Fierro Esquivel | Analyst and developer | Analysis, product documentation, and programming | fierroesquiveljuancamilo@gmail.com |

*Note.* Apprentices in the Technologist in Software Analysis and Development program at SENA.

## Definitions, acronyms, and abbreviations

**Table 2**

*Project glossary*

| **Term** | **Definition** |
|---|---|
| LSC | Colombian Sign Language, officially recognized by Ley 982 de 2005. |
| API Gateway | Single entry point to the backend: routes requests, validates the JWT, and limits the request rate. |
| Microservice | Small, independent service with its own database that handles a single business domain. |
| JWT | JSON Web Token. Signed credential that identifies the user and their roles in each request. |
| MediaPipe | Google library that detects the hand in the image and returns 21 anatomical points (landmarks). |
| Landmark | Each of the 21 hand points, with x, y, z coordinates. |
| DTW | Dynamic Time Warping. Algorithm that compares two sequences even when they have different speeds. |
| Template | Saved example of a sign against which the camera input is compared. |
| Threshold | Maximum accepted distance for displaying a sign as recognized (currently 1.1). |
| RabbitMQ | Message broker that carries domain events between microservices. |
| Domain event | Message that reports that something occurred, for example "TranslationProduced". |
| RBAC | Role-based access control (USER, ADMIN, LINGUIST). |
| Expo | Platform that allows compiling the same React Native application for Android, iOS, and web. |
| RF / RNF | Functional requirement / nonfunctional requirement. |
| CU | Use case. |

# Overall description

## Product perspective

Traduce Señas is a new, independent product. It is designed to be educational, accessible, and easy to use, so that anyone, even without prior knowledge, can interact with the deaf community.

Technically, the system consists of a client built with React Native and Expo (running on Android, iOS, and web browsers) and a microservices backend in Node.js, Express, and TypeScript: an API Gateway and five domain services (iam, recognition, lexicon, analytics, and profile), each with its own PostgreSQL database, MongoDB for training samples, and RabbitMQ for asynchronous event-based communication.

## User characteristics

**Table 3**

*User profiles*

| **Profile** | **Training** | **Main activities** |
|---|---|---|
| Deaf user or user with hearing loss | No technical training required | Performs signs in front of the camera, reviews their history and the alphabet, and configures theme and language |
| Hearing user | No LSC knowledge required | Reads or listens to the translation and learns basic signs with the alphabet |
| Administrator | Technologist in Software Analysis and Development | Monitors recognition accuracy, manages the lexicon and users, captures samples, reviews statistics and audit logs |

## Constraints

- **Limited vocabulary:** recognition initially covers the alphabet and a reduced set of words; it does not cover the full LSC.

- **Connectivity:** registration, sign-in, history, and statistics require an internet connection.

- **Hardware:** the device must have a camera and the capacity to process video in real time.

- **Personal data:** the system must comply with Ley 1581 de 2012 and Decreto 1377 de 2013, especially for video samples and hand points.

- **Team and timeline:** three apprentices working part-time and following the SENA academic calendar.

## Assumptions and dependencies

- Deaf and hearing people are willing to use the application to communicate.

- Institutional support is available for pilot testing in real-world settings.

- Public LSC data, such as the LSC-54 dataset (Mora-Zarate et al., 2025), is available to build the recognition templates.

- Users have access to a smartphone or a computer with a camera.

- The system depends on external services: MediaPipe, an SMTP mail server, and, in the future, Firebase Cloud Messaging.

# Specific requirements

## Common interface requirements

### User interfaces

The interface is inclusive, intuitive, and accessible: large buttons (minimum 48 × 48 dp), clear iconography, a minimum contrast of 4.5:1 in light and dark themes, and no information conveyed by sound alone. It contains a live video area for recognition, a translation panel, accessibility controls, and an alphabet section.

### Hardware interfaces

- Device camera with a minimum resolution of 720p.

- Speaker or audio output for voice mode.

- Stable internet connection.

- Processor capable of running computer vision inference in real time.

### Software interfaces

- Android 9 (API 28) or later.

- iOS 14 or later.

- Up-to-date desktop browsers with WebAssembly and camera access support (Chrome, Edge, Firefox).

- Server with Docker and Docker Compose for the backend.

### Communication interfaces

- HTTPS for all client-server connections.

- REST API with data exchange in JSON format.

- JWT authentication in the "Authorization: Bearer" header.

- AMQP messaging (RabbitMQ) between microservices, only within the internal network.

## Functional requirements

Table 4 summarizes the eleven functional requirements of the system and their specifications (ERF). The detailed catalog follows, dividing each requirement into atomic, verifiable functions numbered by module.

**Table 4**

*System functional requirements*

| **Code** | **Requirement** | **Specifications** | **Priority** |
|---|---|---|---|
| RF1 | User management | ERF1.1 Registration · ERF1.2 Sign-in (own account, Google, Facebook) · ERF1.3 Password recovery | High |
| RF2 | Site navigation | ERF2.1 Navigation bar visible on all devices | High |
| RF3 | Sign language translation | ERF3.1 Translation mode activation · ERF3.2 Conversion of signs to text/audio | High |
| RF4 | Visual notifications | ERF4.1 On-screen notifications | Medium |
| RF5 | Profile management | ERF5.1 View profile · ERF5.2 Light/dark theme · ERF5.3 Change password · ERF5.4 Language · ERF5.5 Recent translations | High |
| RF6 | Usage statistics (administrator only) | ERF6.1 Access to statistics | Medium |
| RF7 | Sign language alphabet | ERF7.1 Alphabet display | High |
| RF8 | Gamification | ERF8.1 Achievement catalog and progress · ERF8.2 Achievement unlock and notice | Low |
| RF9 | Internal audit | ERF9.1 Audit trail query | Medium |
| RF10 | Error handling | ERF10.1 Error classification and query | Medium |
| RF11 | Password policy | ERF11.1 Policy configuration | Medium |

### Identity module (iam-service)

**Table 5**

*Detailed requirements — Identity (iam-service)*

| **ID** | **Description** | **Source** | **Priority** |
|---|---|---|---|
| FR-001 | Register a user with name, date of birth, username, email, and a valid password; the account is created as INACTIVE until the email is verified | RF1 | Must |
| FR-002 | Reject registration unless the terms are accepted, and store the accepted date and version | RF1 | Must |
| FR-003 | Verify the email with a 6-digit code | RF1 | Must |
| FR-004 | Store codes only as hashes, with expiration, single use, and an attempt counter | RF1 | Must |
| FR-005 | Authenticate with email and password | RF1 | Must |
| FR-006 | Temporarily lock the account after failed attempts (1, 5, and 10 minutes) | RF1 | Must |
| FR-007 | Permanently lock the account when the escalation is exhausted; only the administrator can restore it | RF1 | Must |
| FR-008 | Registration and sign-in with Google and Facebook | RF1 | Must |
| FR-010 | Recover the password with a single-use code that expires | RF1 | Must |
| FR-012 | Rotate refresh tokens and detect their reuse | RF1 | Must |
| FR-014 | Delete the account by anonymizing the data (Ley 1581 de 2012) | RF1, RF5 | Should |
| FR-015 | The administrator lists, locks, restores, and deactivates accounts | RF1, RF6 | Should |

*Note.* MoSCoW priority (Must, Should, Could). The complete catalog contains 79 requirements (04-requirements/functional.md); these tables show the representative ones for each module.

### Authorization module (iam-service)

**Table 6**

*Detailed requirements — Authorization (iam-service)*

| **ID** | **Description** | **Source** | **Priority** |
|---|---|---|---|
| FR-101 | Role-based access control for USER, ADMIN, and LINGUIST | RF6 | Must |
| FR-102 | Permissions are assigned to roles, never directly to users | RF6 | Must |
| FR-103 | Deny administration functions to anyone without the permission | RF6 | Must |
| FR-104 | Do not allow deleting a role that is assigned to a user | RF6 | Must |

### Profile and notifications module (profile-service)

**Table 7**

*Detailed requirements — Profile and notifications (profile-service)*

| **ID** | **Description** | **Source** | **Priority** |
|---|---|---|---|
| FR-201 | View and edit profile data | RF5 | Must |
| FR-202 | Change the password in compliance with the current policy | RF5 | Must |
| FR-203 | Choose a light or dark theme | RF5 | Should |
| FR-204 | Choose the interface language (Spanish / English) | RF5 | Should |
| FR-205 | Enable or disable all notifications with a single toggle | RF4, RF5 | Must |
| FR-502 | Deliver notifications only through visual channels (push, in-app, email) | RF4 | Must |
| FR-507 | Mark a notification as read | RF4 | Must |

### Lexicon module (lexicon-service)

**Table 8**

*Detailed requirements — Lexicon (lexicon-service)*

| **ID** | **Description** | **Source** | **Priority** |
|---|---|---|---|
| FR-301 | Present the sign catalog organized by category | RF7 | Must |
| FR-302 | Search signs by name | RF7 | Must |
| FR-303 | Show the details of a sign with its multimedia resources | RF7 | Must |
| FR-304 | Present the alphabet (A–Z and Ñ) without repeated letters | RF7 | Must |
| FR-305 | Each sign has a stable code that does not change when published | RF7 | Must |
| FR-307 | The administrator creates, edits, publishes, and withdraws signs | RF7 | Must |
| FR-310 | Only ACTIVE signs are visible to the end user | RF7 | Must |

### Translation and AI module (recognition-service)

**Table 9**

*Detailed requirements — Translation and AI (recognition-service)*

| **ID** | **Description** | **Source** | **Priority** |
|---|---|---|---|
| FR-401 | Capture signs with the camera and translate them into text in real time | RF3 | Must |
| FR-402 | Provide audio output, always accompanied by visible text | RF3 | Should |
| FR-403 | Record the model that produced each translation and its confidence | RF3 | Must |
| FR-405 | Visually warn when a sign is not recognized, instead of showing an incorrect translation | RF3 | Must |
| FR-406 | View one's own translation history, from most recent to oldest | RF3, RF5 | Must |
| FR-407 | Delete history entries (logical deletion) | RF5 | Should |
| FR-601 | Capture gesture samples grouped by capture session | RF3 | Must |
| FR-602 | Do not store any sample without verifiable consent | — | Must |
| FR-605 | Maintain a registry of models with their training metrics | RF3 | Must |

### Usage, achievements, audit, and configuration module

**Table 10**

*Detailed requirements — Usage, achievements, audit, and configuration*

| **ID** | **Description** | **Source** | **Priority** |
|---|---|---|---|
| FR-701 | Record an event each time the user enters a section | RF6 | Must |
| FR-703 | Show the administrator the most visited sections | RF6 | Must |
| FR-706 | Statistics are aggregated and require permission | RF6 | Must |
| FR-801 | Maintain an achievements catalog with a target greater than zero | RF8 | Could |
| FR-803 | An achievement is unlocked only once per user | RF8 | Could |
| FR-901 | Maintain an audit trail with values before and after each change | RF9 | Must |
| FR-904 | Classify errors by type (validation, authentication, permission, database, etc.) | RF10 | Must |
| FR-905 | Configure the password policy; only one policy may be active | RF11 | Should |

## Nonfunctional requirements

**Table 11**

*Nonfunctional requirements and their acceptance metric*

| **Code** | **Attribute** | **Requirement and metric** |
|---|---|---|
| RNF1 | Performance | End-to-end response of 5 s or less; P95 latency < 300 ms in critical services (login, translations, lexicon) with 50 requests per second; ≥ 15 frames per second during capture |
| RNF2 | Usability | Simple interface; a new user completes a translation without help in less than 2 minutes |
| RNF3 | Compatibility | Android 9+, iOS 14+, and up-to-date desktop browsers |
| RNF4 | Security | Passwords hashed with bcrypt; mandatory JWT with a secret of 32+ characters; progressive lockout; request rate limit on /api/auth; HTTPS in production |
| RNF5 | Adaptability / scalability | 500 concurrent users and 5,000 registered; each microservice scales independently |
| RNF6 | Documentation | Technical, installation, and user manuals; OpenAPI contracts for each service |
| RNF7 | Fault tolerance | A failure in one service does not stop the others; the event bus reconnects automatically |
| RNF8 | Personal data protection | Explicit authorization with date and version; right of erasure; samples disassociated from identity |
| RNF9 | Accessibility | WCAG 2.1 Level AA as the target; no information conveyed by sound alone; contrast 4.5:1; touch targets of 48 dp |
| RNF10 | Observability | GET /health endpoint in each service; structured logs and classified errors |
| RNF11 | Maintainability | Hexagonal architecture; unit test coverage ≥ 80%; commit and branch conventions |
| RNF12 | Disaster recovery | Daily database backups; documented restoration in less than 4 hours |

*Note.* Accessibility is treated as a primary constraint because the primary users are deaf people (World Wide Web Consortium [W3C], 2018).

# Use case requirements

## UML use case diagram

**Figure 1**

*General use case diagram*

![01-01.png](capturas/01-01.png)

*Note.* Own elaboration.

## Use case characterization

**Table 12**

*Characterization of use case CU-01 Sign in*

| **Element** | **Description** |
|---|---|
| Actors | End user, Administrator |
| Requirement | RF1 / ERF1.2 |
| Precondition | The account exists and is ACTIVE |
| Main flow | 1. The user opens the app and selects "Sign in". 2. Enters email and password. 3. The system validates the credentials. 4. The system displays the main dashboard. |
| Alternative flows | 3a. Invalid credentials: displays an error and adds a failed attempt. 3b. Attempts are exceeded: the account is temporarily locked. |
| Postcondition | The user has an active session with their token |

**Table 13**

*Characterization of use case CU-02 Sign up*

| **Element** | **Description** |
|---|---|
| Actors | End user |
| Requirement | RF1 / ERF1.1 |
| Precondition | The email is not registered |
| Main flow | 1. The user selects "Sign up". 2. Fills out the form and accepts the terms. 3. The system creates the account and sends a code. 4. The user enters the code and the account becomes active. |
| Alternative flows | 2a. Invalid data or terms not accepted: the system displays the errors. 4a. Expired code: the user requests that it be resent. |
| Postcondition | ACTIVE account, profile, and preferences created |

**Table 14**

*Characterization of use case CU-03 Translate signs to text*

| **Element** | **Description** |
|---|---|
| Actors | End user |
| Requirement | RF3 / ERF3.1, ERF3.2 |
| Precondition | Signed-in session and camera permission |
| Main flow | 1. The user opens "Translation" and taps "Start camera". 2. Performs the sign in front of the camera. 3. The system recognizes the sign and displays the text. 4. Optionally plays the voice output. 5. The system saves the translation to the history. |
| Alternative flows | 1a. No camera permission: the system requests it. 3a. Sign not recognized: visual notice; no word is displayed. |
| Postcondition | The translation is stored in the user's history |

**Table 15**

*Characterization of use case CU-04 Consult LSC alphabet*

| **Element** | **Description** |
|---|---|
| Actors | End user |
| Requirement | RF7 / ERF7.1 |
| Precondition | None (also available in the demo) |
| Main flow | 1. The user opens "Alphabet". 2. The system displays the letters. 3. The user selects a letter and views its 3D model and description. |
| Alternative flows | 2a. No connection: the letters saved in the cache are displayed. |
| Postcondition | The user views the selected sign |

**Table 16**

*Characterization of use case CU-05 Consult usage statistics*

| **Element** | **Description** |
|---|---|
| Actors | Administrator |
| Requirement | RF6 / ERF6.1 |
| Precondition | Session with ADMIN role |
| Main flow | 1. The administrator opens "Statistics". 2. The system displays translations per day, the most visited sections, and the most used signs. |
| Alternative flows | 1a. User without ADMIN role: the system denies access (403). |
| Postcondition | Aggregated statistics visible |

**Table 17**

*Characterization of use case CU-06 Capture samples and train*

| **Element** | **Description** |
|---|---|
| Actors | Administrator |
| Requirement | RF3 / FR-601…606 |
| Precondition | Session with ADMIN role and signer consent |
| Main flow | 1. The administrator opens "Training". 2. Selects the sign and captures samples. 3. The system saves the samples. 4. The administrator generates and publishes the templates. |
| Alternative flows | 3a. No consent: the sample is rejected. 2a. Discards the entire capture session. |
| Postcondition | New templates available for recognition |

# Traceability

Each functional requirement is linked to the user story that plans it and to the tables that persist it. The complete traceability matrix is maintained in the documentation repository (04-requirements/traceability-matrix.md). Table 18 summarizes the current coverage.

**Table 18**

*Traceability coverage summary*

| **Relationship** | **Coverage** |
|---|---|
| SRS requirements with at least one detailed requirement | 15 of 15 |
| Detailed requirements with user story | 79 of 79 |
| Detailed requirements persisted in the data model | 74 of 79 (the remaining 5 do not persist data) |
| Data model tables used by at least one requirement | 25 of 29 |

# Conclusions

The specification defines eleven functional requirements, broken down into 79 detailed requirements, and twelve nonfunctional requirements with measurable metrics. Accessibility and personal data protection are established as primary constraints due to the nature of the users and of the biometric information the system handles.

This document is the baseline for analysis, design, and testing; any change in scope must be recorded here and in the traceability matrix before it is implemented.

# References

Congreso de la República de Colombia. (2005, August 2). *Ley 982 de 2005, por la cual se establecen normas tendientes a la equiparación de oportunidades para las personas sordas y sordociegas*. Diario Oficial No. 45.995.

Congreso de la República de Colombia. (2012, October 17). *Ley Estatutaria 1581 de 2012, por la cual se dictan disposiciones generales para la protección de datos personales*. Diario Oficial No. 48.587.

Institute of Electrical and Electronics Engineers. (1998). *IEEE recommended practice for software requirements specifications* (IEEE Std 830-1998). IEEE. https://doi.org/10.1109/IEEESTD.1998.88286

Instituto Nacional para Sordos. (2020). *Observatorio social de la población sorda colombiana*. INSOR. https://www.insor.gov.co/observatorio/

Mora-Zarate, J. E., Garzón-Castro, C. L., & Castellanos Rivillas, J. A. (2025). LSC-54: A landmark-based dataset for Colombian Sign Language. *Data in Brief*. https://doi.org/10.1016/j.dib.2025.112145

Presidencia de la República de Colombia. (2013, June 27). *Decreto 1377 de 2013, por el cual se reglamenta parcialmente la Ley 1581 de 2012*. Diario Oficial No. 48.834.

Pressman, R. S., & Maxim, B. R. (2020). *Software engineering: A practitioner's approach* (9th ed.). McGraw-Hill Education.

Sommerville, I. (2016). *Software engineering* (10th ed.). Pearson.

World Wide Web Consortium. (2018). *Web Content Accessibility Guidelines (WCAG) 2.1*. W3C. https://www.w3.org/TR/WCAG21/

Zhang, F., Bazarevsky, V., Vakunov, A., Tkachenka, A., Sung, G., Chang, C.-L., & Grundmann, M. (2020). *MediaPipe Hands: On-device real-time hand tracking* (arXiv:2006.10214). arXiv. https://doi.org/10.48550/arXiv.2006.10214
