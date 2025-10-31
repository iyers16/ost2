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

### Example

Imagine a small community forum where people post messages and replies. If the site simply shows whatever someone types without first cleaning it, an attacker could post a message that contains a tiny bit of JavaScript. When other users open that thread, their browsers will run that script as if it came from the forum itself. The script could quietly steal a user’s session token, read personal profile information, or make hidden requests using the victim’s identity. The user doesn’t notice anything unusual, but their account or private data can be taken.

### Mitigation

Always treat any text coming from users as untrusted and transform it before showing it back to others. Use libraries or templates that automatically escape content for the place it will appear (inside HTML, inside attributes, inside JavaScript). Limit what pages are allowed to run with browser security rules and avoid returning raw user input in places that execute code. Finally, keep sensitive session tokens out of reach of scripts by using browser features that make them inaccessible to JavaScript.

## Type confusion

## Overview

Quite descriptively, this is when we treat one data/structure type as if it were another. This can make the program interpret data as the wrong type, leading to bizarre code execution braches and out of bounds access.

### Example

Think of a program that expects to receive a phone number object but instead is handed a plain number that was crafted by an attacker. If the program blindly treats that number as the phone object and starts pulling fields off it, it can end up reading or writing parts of memory it shouldn't touch. From the outside this looks like the program suddenly doing weird things, crashing, or revealing pieces of its internal state. An attacker can probe by trying different values and learn which addresses are safe to touch, slowly mapping what the program has in memory.

### Mitigation

At every boundary where one part of a system hands data to another, check that the data is actually the kind you expect. Convert or copy incoming values into well-defined, owned structures before using them, and prefer simple, explicit interfaces that avoid passing raw internal pointers or handles across trust boundaries. When possible, use higher-level, memory-safe constructs instead of raw casts and unchecked dereferences.

## UAF - Use after free

### Overview

This happens after the code frees an object but we still dereference a pointer to that address. It's dangerous becasue the allocator can hand that same memory to an attacker-controlled allocation. This leads to leaks, writing corrupt ACID, crashes, or arbitrary code execution.

### Example

Imagine a shared lookup table where a program stores pointers to temporary objects. If one code path removes and frees an object but forgets to clear its entry in the table, another code path can later read that same pointer, thinking it’s still valid. If an attacker arranges for their own data to be allocated at that freed address in the meantime, the stale pointer now points at attacker-controlled content and the program will happily read or execute it. The result can be anything from leaked secrets to full takeover of the process.

### Mitigation

When you free something that can be observed by other parts of the program, make sure to remove or replace any references to it under the same lock or synchronization so nobody else sees a stale pointer. Prefer reference counting or well-tested reclamation schemes that only free memory when no readers remain. Initialize memory on allocation and sanitize freed memory during testing so accidental reuse is easier to spot, and validate handles before you use them instead of blindly trusting stored pointers.
