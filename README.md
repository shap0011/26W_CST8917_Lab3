# Lab 3: FleetBook — Vehicle Booking with Service Bus, Logic Apps & Functions

## CST8917 — Serverless Applications | Winter 2026

**St: Olga Durham**
**St#: 040687883**

---

### Deliverables

| Link                                      | Item                          | Description                                                   |
| ----------------------------------------- | ----------------------------- | ------------------------------------------------------------- |
| [**Function Code**](/src/function_app.py) | `function_app.py`             | The Azure Function code (provided — should be in your repo)   |
| [**Requirements**](/src/requirements.txt) | `requirements.txt`            | Python dependencies                                           |
| [**Test Function**](/)                    | `test-function.http`          | REST Client test requests                                     |
| [**Client**](/)                           | `client.html`                 | FleetBook web app                                             |
| [**Settings Template**](/)                | `local.settings.example.json` | Settings template with placeholder values (NOT the real keys) |
| [**README**](/README.md)                  | `README.md`                   | Brief setup instructions for your project                     |
| [**Demo Video Link**](https://youtu.be/)  | **Demo Video Link**           | YouTube link (in your README or submitted separately)         |

---

### Overview

FleetBook is a serverless vehicle booking system built using Azure services.
In this lab, I implemented an event-driven workflow where booking requests are processed through Service Bus, evaluated by an Azure Function, and orchestrated by a Logic App to produce booking confirmations or rejections.

---

### Architecture + How It Works

```mermaid
flowchart TD
    Client(["Web App\n(FleetBook)"])
    Queue["Service Bus Queue\n(booking-queue)"]
    Logic["Logic App\n(process-booking)"]
    Func["Azure Function\n(check-booking)"]
    Cond{"Confirmed\nor\nRejected?"}
    EmailC["Send Confirmation\nEmail"]
    EmailR["Send Rejection\nEmail"]
    Topic["Service Bus Topic\n(booking-results)"]
    ConfSub["confirmed-sub\n(label = 'confirmed')"]
    RejSub["rejected-sub\n(label = 'rejected')"]

    Client -- "1. POST booking" --> Queue
    Queue -- "2. Triggers" --> Logic
    Logic -- "3. Calls function" ---> Func
    Func -- "4. Returns decision\n+ pricing" ---> Logic
    Logic -- "5. Evaluates status" --> Cond
    Cond -- "confirmed" --> EmailC
    Cond -- "rejected" --> EmailR
    EmailC -- "6a. Publish with\nlabel=confirmed" ---> Topic
    EmailR -- "6b. Publish with\nlabel=rejected" ---> Topic
    Topic --> ConfSub
    Topic --> RejSub

    style Client fill:#4a9eff,stroke:#2d7ad4,color:#fff
    style Queue fill:#e67e22,stroke:#d35400,color:#fff
    style Logic fill:#9b59b6,stroke:#7d3c98,color:#fff
    style Func fill:#f5a623,stroke:#d48b1a,color:#fff
    style Cond fill:#34495e,stroke:#2c3e50,color:#fff
    style EmailC fill:#2ecc71,stroke:#27ae60,color:#fff
    style EmailR fill:#e74c3c,stroke:#c0392b,color:#fff
    style Topic fill:#1abc9c,stroke:#16a085,color:#fff
    style ConfSub fill:#2ecc71,stroke:#27ae60,color:#fff
    style RejSub fill:#e74c3c,stroke:#c0392b,color:#fff
```

The system follows an event-driven architecture:

1. A user submits a booking through the web client
2. The request is sent to a Service Bus queue
3. A Logic App is triggered and calls an Azure Function
4. The function checks availability and calculates pricing
5. The Logic App decides:
   - If confirmed → send confirmation email
   - If rejected → send rejection email
6. The result is published to a Service Bus topic
7. Subscriptions filter confirmed and rejected bookings

---

### Services Used

- **Azure Service Bus (Queue & Topic)** — handles messaging and routing
- **Azure Logic Apps** — orchestrates the workflow and decision logic
- **Azure Functions** — processes booking logic and pricing
- **Outlook Connector** — sends email notifications
- **Web Client (HTML)** — submits booking requests

---

### Screenshots

#### Service Bus Setup

_Figure 1. - Service Bus Queue_

![Service Bus Queue](screenshots/service-bus/service-bus-queue.png)

_Figure 2. - Service Bus Topic_

![Service Bus Topic](screenshots/service-bus/service-bus-topic.png)

_Figure 3. - Service Bus Confirmed Filter_

![Service Bus Confirmed Filter](screenshots/service-bus/service-bus-confirmed-filter.png)

_Figure 4. - Service Bus Rejected Filter_

![Service Bus Rejected Filter](screenshots/service-bus/service-bus-rejected-filter.png)

#### Azure Function

![Function](screenshots/function/terminal-running-function.png)

#### Deployed API Health Result

![Deployed API Health Result](screenshots/function/deployed-api-health-result.png)

#### Logic App Workflow

![Logic App](screenshots/logic-app/placeholder.png)

#### Confirmed Booking Flow

![Confirmed](screenshots/confirmed-flow/placeholder.png)

#### Rejected Booking Flow

![Rejected](screenshots/rejected-flow/placeholder.png)
