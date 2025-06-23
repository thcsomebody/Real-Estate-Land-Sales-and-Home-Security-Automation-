Workflow 1 (Real Estate Polling):

Trigger: Cron node to run periodically.
Data Fetch: HTTP Request node to poll the real estate API.
Processing: Split In Batches to handle multiple property updates at once.
State Management: A Function node will be used to simulate writing to a persistent store. We'll use expressions to format the property ID as a key.
Workflow 2 (Security Event Handler):
Trigger: Webhook node listens for POST requests from Home Assistant.
State Retrieval: A Function node simulates retrieving the property's current status from the persistent store using the propertyId sent in the webhook.
Conditional Logic: An IF node routes the workflow based on the combination of the security_event_type and the retrieved property_status.
Actions: Various nodes (HTTP Request for Home Assistant API, Telegram, Google Drive) are triggered based on the logic path.
Visual Design
Workflow 1: Real Estate Status Polling & Caching
Generated code
     ┌────────┐     ┌───────────────┐     ┌─────────────────┐     ┌──────────────────┐
│ [Cron] │───> │ [HTTP Request]│───> │[SplitInBatches] │───> │[Function Node]   │
│ Every  │     │ Get Real      │     │ Process Each    │     │ Write to State   │
│ Hour   │     │ Estate Data   │     │ Property        │     │ Store (DB/Cache) │
└────────┘     └───────────────┘     └─────────────────┘     └──────────────────┘
   
Workflow 2: Security Event Handler
Generated code
     ┌──────────┐     ┌──────────────────┐     ┌──────────────────────────────────────────────┐
│ [Webhook]│───> │ [Function Node]  │───> │ [IF Node]                                    │
│ From     │     │ Get Property     │     │ "Property Sold & Motion?" "New Listing &...?"│
│ Home Asst│     │ Status from      │     └┬──────────────────┬──────────────────┬────────┘
└──────────┘     │ State Store      │      │                  │                  │
                 └──────────────────┘      │(output 1)        │(output 2)        │(default)
                                           ▼                  ▼                  ▼
                                     ┌───────────┐      ┌───────────┐      ┌──────────┐
                                     │[Telegram] │      │[HTTP Req] │      │[Log]     │
                                     │Notify New │      │Record     │      │Ignore/  │
                                     │Owner      │      │Video      │      │Log Event│
                                     └───────────┘      └───────────┘      └──────────┘
   
