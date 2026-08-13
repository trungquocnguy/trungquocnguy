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

**Money path**  
Tích hợp cổng thanh toán và webhook. Chữ ký fail-closed. Retry từ bên kia không ghi sổ hai lần.

**E-invoice**  
Phát hành hóa đơn điện tử idempotent. Timeout hay retry chỉ gửi phần chưa confirm.

**Messaging**  
Giao nhận at-least-once, effect exactly-once. Worker chết không mất message, không chặn nhau.

**Concurrency**  
Tuần tự hóa chỗ không được lost update. Cache chết thì fail-closed, không đoán.

**Boundaries**  
Ranh giới module và credential không nằm ở convention — CI chặn.

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
