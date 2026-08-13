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

**Money path không double-book**  
ABBank — 7 endpoint + webhook biến động số dư. Xác thực hai lớp (Basic + RSA) trên *raw bytes*, fail-closed. Dedup bằng transaction id bất biến + `UNIQUE`. Ledger và outbox ghi trong cùng transaction.

**E-invoice không phát hành trùng**  
Idempotency 3 lớp: unique index · workflow check · provider key. Batch lỗi một phần → retry đúng item chưa confirm, không phát hành lại cả lô.

**At-least-once, exactly-once effect**  
Transactional outbox. Relay worker claim batch bằng pessimistic lock, bỏ qua row đang bị giữ. Reaper thu hồi lock worker chết — không mất message, không chặn nhau.

**Concurrency đúng isolation**  
Trừ hạn mức hóa đơn / xuất kho tuần tự bằng row lock. Read Committed không chặn lost update. Redis auth invalidate qua RabbitMQ, TTL làm chốt, Redis chết thì fail-closed.

**Ranh giới module là luật CI**  
Legacy .NET 8 → 5 bounded context sau YARP. 3 cơ chế auth gom về Keycloak. `NetArchTest` fail build nếu service reference chéo hoặc credential lọt config.

## FlashSale

```text
  stock ........ 5 000
  orders ....... 5 000
  oversell ..... 0
  requests ..... 377K @ 1 000 VU
  tests ........ 174  ·  83 integration trên Postgres / Redis / RabbitMQ thật
```

Redis Lua shard + cross-shard fallback. Payment SAGA hoàn tồn kho về **đúng shard đã trừ**. 3 API + 2 worker sau NGINX. Trace 24 span `API → RabbitMQ → Worker`.

## Stack

`C#` `ASP.NET Core` `EF Core` `PostgreSQL` `Redis` `RabbitMQ` `MassTransit`  
`Outbox` `SAGA` `Keycloak` `YARP` `Polly` `Testcontainers` `OpenTelemetry`

<p align="center">
  <a href="mailto:trungquocnguy@gmail.com"><code>trungquocnguy@gmail.com</code></a>
</p>
