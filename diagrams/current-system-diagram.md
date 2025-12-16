```mermaid
flowchart LR
    subgraph TP1[Third Party System 1]
        BP["Billing Provider<br/>Subscriptions/Billing"]
    end

    subgraph TP2[Third Party System 2]
        3PL["3PL Company<br/>Fulfillment/Shipping"]
    end

    subgraph TP3[Third Party System 3]
        EC[E-commerce Platform<br/>Products, Orders, APIs]
    end

    subgraph I[Internal System]
        BE["Backend System<br/>Subscription Logic<br/>Order Orchestration"]
    end


    %% Data Flows
    BP -->|"Subscription Events<br/>(Webhooks)"| BE
    BE -->|"Create / Update Orders"| EC
    EC -->|"Nightly Order Export"| 3PL

    %% Feedback / Gaps
    3PL -.->|"Limited Status Feedback."| EC
    EC -.->|"Order Status Mismatch"| BE


    classDef dashed stroke-dasharray: 10 4;
    class TP1 dashed;
    class TP2 dashed;
    class TP3 dashed;
    class I dashed;


```
 ***Challenges with existing system***
 * Current subscription logic is managed by third party. Potential issues with timing and duplications
 * Source of truth. Who manages our product? The properties we have control over might not be able to flow into the third party system
 * Too many connections to manager. E-Commerce is being used as the source of truth for 3PL. Might have constraints how much data can go into it and what properties
 * Debugging problems. What happens when one of these connections fail or the events flow is disturbed. Would be hard to integrate observability of the whole system
 * Too many chained events. Each system talks to the next and information/event might get lost/fail along the way
 * No single source of truth
 * Engineering is constantly chasing syncing issues. Too many teams will might have to get involved to get the failed events out.
 * New features might be hard to integrate based on the feature. Perhaps because third party system cant accomodate it.
 * Potential scaling challenges
 * Customer issues -> custumer support bottlenecks. What happens when complaints arrive
 * Team burnout


