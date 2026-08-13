```text
trung@hcm  ·  .NET backend
────────────────────────────────────────
payments · e-invoice · reliable messaging
```

Bank retry. Worker chết. Invoice timeout. Hệ thống vẫn ghi **một lần**.

```diff
+ assert unique(transaction_id)
+ assert signature.verify(raw_bytes) || reject
+ assert ledger_debit == ledger_credit
+ assert orders + redis_remaining == stock
```

## Strengths

`Money path` `E-invoice` `Messaging` `Concurrency` `Boundaries`

## FlashSale

```text
  stock ........ 5 000
  orders ....... 5 000
  oversell ..... 0
  requests ..... 377K @ 1 000 VU
  tests ........ 174  ·  83 integration trên Postgres / Redis / RabbitMQ thật
```

## Stack

`C#` `ASP.NET Core` `EF Core` `PostgreSQL` `Redis` `RabbitMQ` `MassTransit`  
`Outbox` `SAGA` `Keycloak` `YARP` `Polly` `Testcontainers` `OpenTelemetry`

<p align="center">
  <a href="mailto:trungquocnguy@gmail.com"><code>trungquocnguy@gmail.com</code></a>
</p>
