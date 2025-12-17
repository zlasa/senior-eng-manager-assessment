1. Customer visits website (SPA on Cloudflare)

2. Browses product catalog
   - SPA → GET /api/products → Rails API → Postgres
   - Products cached in Redis for fast loading

3. Selects a subscription plan
   - Monthly + products
   - Clicks "Subscribe"

4. Enters payment information
   - SPA → POST /api/subscriptions
   - Rails validates and calls Stripe to create customer + payment method

5. Rails creates subscription in database
   - Subscription record: {user_id, product_id, interval: 'monthly', next_billing_date: '2026-01-16'}
   - Status: 'active'

6. Rails queues background job
   - SubscriptionWelcomeJob.perform_later(subscription_id)
   - Job goes into Redis queue

7. Sidekiq Email Worker picks up job
   - Sends welcome email via SendGrid
   - "Thanks for subscribing! First delivery coming soon."

8. Customer sees confirmation page
   - "Subscription confirmed! You'll be charged on Jan 16."