```
sequenceDiagram
    participant Pub as Publisher
    participant Redis as Memorystore Redis
    participant SubA as Subscriber A<br/>(SUBSCRIBE events)
    participant SubB as Subscriber B<br/>(PSUBSCRIBE event*)
    participant SubC as Subscriber C<br/>(Disconnected)

    Pub->>Redis: PUBLISH "events" → "user_created"

    Redis-->>SubA: Deliver (exact channel match)
    Redis-->>SubB: Deliver (pattern match)

    Note over SubC: Disconnected<br/>No delivery<br/>No buffering<br/>Message lost

    Note over Redis: Filtering only by channel/pattern<br/>No content-based filtering
```