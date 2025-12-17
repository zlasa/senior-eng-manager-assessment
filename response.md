# Systems Thinking & Architecture Exercise  
**Candidate Response**

---

If you are submitting your response in **Markdown**, please write it below.

If you are submitting your response in **another format** (PDF, PowerPoint, Keynote, Canva export, etc.), please:

1. Add the file to this repository (in the root folder or a `/submission` folder)  
2. Add a short note here indicating the filename, for example:

**Submitted file:** `my_submission.pdf`

---

## 1. Decision Framework  
Describe how you would evaluate whether the current e-commerce platform should remain in the architecture.  
Include 1–2 tools, platforms, or techniques you would use to support this evaluation.

**Your answer here (or reference your file).**

---

## 2. Options Analysis  
Discuss the strengths and limitations of each option:

1. Keep the current platform  
2. Replace it with another platform  
3. Build an internal service  

Include 1–2 tools or platforms you might consider for each option.

1. Keep Current Platform
    - Strengths 
        - Established infrastructure (somewhat working)
        - Team knows the system
        - E-commerce functionality works well
        - No custom development needed
            - No building from scratch

    - Limitations
        - Not ideal subscription based setup
        - 3 systems to manage subscriptions
        - Sync failures hard to debug. No atomic transactions
        - Custom feature request can be hard to fulfill based on thirs party offerings
        - Data misalignment. What is the source of truth here
        - Subscription state is not persisted clearly
        - Tight coupling with 3rd party. Might lead to cost spikes down the road

2. Replace with another platform/s
Swapping to a different e-commerce platform would simply trade one set of limitations for another. The current integration challenges—order sync failures, product data misalignment, and complex subscription workflows—stem from fundamental architectural constraints, not the specific vendor we're using. 

3. Build an internal service
To support long-term growth and rapidly ship new features, we should invest in building our own order and product management system.
Trade-offs:

Higher upfront cost: # months of development instead of 1-2 months migrating platforms
Long-term payoff: Full control over our data model, business logic, and integrations
Strategic advantage: No vendor limitations blocking new features
This would eliminaty sync issues between multiple systems. We'd easily be able to integrate observability to monitor different set of services. We'd be able to easily customize what a subscription model needs. Have full control over the data and single source of truth over our products and subscriptions.  Will be able to ship and debug much faster.

---

## 3. Recommended Approach  
Provide your recommendation and explain why.  
Include 1–2 tools or technologies that would support this direction.

This builds on what I said in the previous section.

---

## 4. High-Level Architecture (Optional)  
Describe what the future architecture *might* look like.  
Optionally include 1–2 tools or technologies you would consider.
[New System Architecture Diagram](/diagrams/new-system-diagram.md)
I have also included [User Journeys](/diagrams/user-journeys/) that outlines basic user workflows for happy/error paths

---

## 5. Migration Strategy  
Explain how you would minimize risk during migration.  
Include 1–2 tools or processes to support rollout.

1. Starts with running both systems in parallel. The data would be written to both systems. Old system would serve users at the beginning.

2. Comparing systems for data parity and ensuring the information that is communicated to the users is captured in the new system.

3. Slowly start directing % of traffic. Could employ something like Canary/Launch Darkly routing. This could help us with stress testing the new solution and address bottlenecks/issues quickly through observability platform like Sentry/New Relic/Data Dog/Scout APM

4. We could also integrate product related observability tools to see how the users are using the new workflows and based on the behavior improve the UI if necessary.

5. After X amount of weeks, we route 100% of traffic to the new system. Still keeping old as the backup


---

## 6. Communication  
Explain how you would communicate your recommendation to Product, Ops, CS, and Leadership.  
Optionally mention tools for documentation or decision tracking.

Leadership/Product: Current architecture blocks growth and prevents us from maximazing revenue due to data sync issues. Debugging takes a long time and there needs to be manual intervention when something goes wrong. Building in-house gives us full control over our data. We'd pay the cost of building this initially, x amount of months, but would be reaping the benefitst later when it comes to feature offerings.

Operations: No more manual interventions. Responsibility shifts to the in-house engineering team.

Customer Support: Will have more insight with the order state and status. Won't have to log into multiple systems to track what went wrong and process  reimbursement

---

## Final Notes (Optional)

Given the limited information available, I made informed assumptions about the subscription model based on common patterns in subscription commerce. Most subscription-based systems share 90% of the same core challenges: recurring billing, order orchestration, fulfillment coordination, and customer self-service.
I drew on my experience working at Home Chef—a subscription meal kit service—to shape recommendations that apply to subscription businesses broadly. While specific details may differ, the fundamental problems (sync failures, product complexity, subscription lifecycle management) and solutions (owned core systems, gradual migrations, feature flags) translate well across subscription commerce companies.
If I had the opportunity to ask clarifying questions, I would refine these recommendations based on your specific subscription model, order volume, and technical constraints.
