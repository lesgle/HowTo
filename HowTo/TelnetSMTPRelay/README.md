# Test Anonymous Relay via Telnet

```text
telnet mail.example.local 25
220 mail.example.local Microsoft ESMTP MAIL Service ready at Tue, 5 Jun 2012 12:59:50 -0400
HELO
250 mail.example.local Hello [192.168.0.1]
MAIL FROM:user1@example.local 
250 2.1.0 Sender OK
RCPT TO:user2@example.local 
250 2.1.5 Recipient OK
DATA
354 Start mail input; end with <CRLF>.<CRLF>
Message body
.
250 2.6.0 <444b0952-92d9-4c32-8cba-e093d0c450ab@mail.example.local> [InternalId=2257334] Queued mail for delivery
QUIT
221 2.0.0 Service closing transmission channel
```