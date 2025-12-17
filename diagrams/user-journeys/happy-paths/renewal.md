TIME: January 16, 2026, at some specified time

1. Heroku Scheduler runs (every hour)
   - Executes: rake subscriptions:process_renewals

2. Rake task queries Postgres
   - Finds customer's subscription (due today)

3. Rails queues renewal job
   - SubscriptionRenewalJob.perform_later(subscription_id)
   - Job → Redis queue
   
4. Sidekiq Renewal Worker picks up job
   - Loads subscription from Postgres
   - Checks: Is customer still active? Any pauses or skips?
   
5. Worker calls Stripe API
   - Charge account
   - Stripe processes payment

6. Payment succeeds 
   - Stripe returns: payment_intent_id, status: 'succeeded'

7. Worker creates order
   - Order includes product + shipping addresss
   
8. Worker updates subscription
   - Update next billing date
   
9. Worker queues fulfillment job
   - FulfillmentJob.perform_later(order_id)
   - Job → Redis queue
   
10. Sidekiq Fulfillment Worker picks up job
    - Calls 3PL API: POST /api/shipments
    - Sends: order details, shipping address, SKUs
    
11. 3PL warehouse receives order
    - Picks order
    - Packs box
    - Creates shipping label
    
12. Worker queues email job
    - OrderShippedJob.perform_later(order_id)
    
13. Sidekiq Email Worker sends notification 
    - Customer receives email