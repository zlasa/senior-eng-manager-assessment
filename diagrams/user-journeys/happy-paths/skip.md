
1. Customer logs into SPA
   - GET /api/subscriptions/me → Shows upcoming delivery

2. Customer clicks "Skip next delivery"
   - SPA → PATCH /api/subscriptions/123/skip
   
3. Rails API validates request
   - Checks: Can this subscription be skipped?
   
4. Rails updates database
   - Update subscription with the next billing cycle
   - Skips one billing cycle
   
5. Rails queues confirmation job
   - SubscriptionUpdatedJob.perform_later(subscription_id, 'skipped')
   
6. Sidekiq Email Worker sends email
   - Skip email
   
7. Customer sees confirmation
   