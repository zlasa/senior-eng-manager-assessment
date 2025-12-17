
1. Payment FAILS ✗
   - Stripe returns: status: 'failed', code: 'card_declined'

2. Worker updates subscription
   - Update subscription status with past due date and number of failed payment counts

3. Worker queues retry job (delayed)
   - PaymentRetryJob.set(wait: 24.hours).perform_later(subscription_id)
   - Job scheduled for later date
   
4. Worker queues notification job
   - PaymentFailedJob.perform_later(subscription_id)
   
5. Sidekiq Email Worker sends email
    - Payment failed message
    - Includes link to update card
    
6. Customer receives email
    - Clicks link → SPA → Updates card in Stripe
    
7. Next day
    - Sidekiq Retry Worker picks up retry job
    - Calls Stripe with new payment method
    - Payment succeeds
    - Creates order and continues as normal