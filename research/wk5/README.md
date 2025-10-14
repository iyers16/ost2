# Research question on vulnerabilities

## SQL injection

### Overview

SQL injection is where ACID is embedded into SQL queries, channeled towards the vulnerable database in order to leak or modify data by bassing some level of authentication.

### Example

This is very common in beginner CTF problems, where the login form is very weak (by design) and the challenge is to be authenticated as an admin. Usually in this case, we would start with a username like admin and in the password field injecting an SQL query that would essentially take advantage of the fact that this poorly constructed app probably doesn't sanitize user input and compares it directly to the database. In this case, our ACID query in the password field would be constructed in a way that it always results in a TRUE operation and then bypass the weak auth mechanism completely, leading us to the admin dashboard.

### Mitigation

Some solutions to this include using parameterized queries, having pre-prepped queries, validating user string input against SQL syntax, having levels of DB privilege so that even if something falls through, it is met with the least amount of leakage, and monitoring DB access activity.

## Race conditions

### Overview

A race occurs when the relative timing of concurrent operations are essential for the execution of a program but it's natural flow is bypassed by attackers influencing that timing. This leads to attackers taking advantage of privileged momentary access to files or data or even well-timed updates that disrupt the flow of the program.

### Example

This is very easily seen by event payment platform examples, for high-volume events like concerts, race conditions are the biggest problem for these platforms because many people are paying all at the same time. A race condition is most classically seen when an impatient user clicks the final Pay button twice quickly, inadvertently resulting in two requests being sent to the server by the same user, which might then be processed and send us two invoices of payment. The other case is where two users are battling time to reserve the same seat and their requests are sent at the same time. This can result in overbooking the seat, a problem which will only arise when both participants arrive at the venue and start fighting over their ticket.

### Mitigation

Using short, atomic operations with locks (mutexes, semaphores) can mitigate access and destructive access to constrained ressources. Implement state synchronization, and stress testing can also mitigate race condition vulnerabilities at scale.

## XSS - Cross site scripting

### Overview

Attackers can inject malicious js scripts into webpages that others will use so that the script runs on the victims' browsers with their own privileges and can steal data or perform malicious actions.

## Type confusion

## Overview

Quite descriptively, this is when we treat one data/structure type as if it were another. This can make the program interpret data as the wrong type, leading to bizarre code execution braches and out of bounds access.

## UAF - Use after free

### Overview

This happens after the code frees an object but we still dereference a pointer to that address. It's dangerous becasue the allocator can hand that same memory to an attacker-controlled allocation. This leads to leaks, writing corrupt ACID, crashes, or arbitrary code execution.