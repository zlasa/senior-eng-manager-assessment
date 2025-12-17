1. Customer logs into SPA
   - Views subscription

2. Clicks "Cancel subscription"
   - SPA → DELETE /api/subscriptions/123
   - Shows: "Are you sure? Reason: [dropdown]"
   
3. Customer confirms cancellation

4. Rails API processes
   - Update subscription status to 'cancelled'
   - Logs cancellation reason
   
5. Rails queues cleanup job
   - SubscriptionCancelledJob.perform_later(subscription_id)
   
6. Sidekiq Email Worker sends confirmation
   - Canceled Message
   
7. Customer sees confirmation
   - No charges applied