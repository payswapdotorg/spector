# Spector — Work Item Index

GitHub Issues are the executable implementation queue. The issue number is part of the takeover contract.

| Issue | Work | Lane | Direct predecessors |
|---:|---|---|---|
| #1 | A0 repository/bootstrap | Lead | — |
| #2 | A1 canonical schema | A | #1 |
| #3 | A2 auth/policy | A | #2 |
| #4 | A3 Matter/Work APIs | A | #2,#3 |
| #5 | A4 knowledge/evidence | A | #2,#3 |
| #6 | A5 Legal Work Graph | A | #4,#5 |
| #7 | A6 Case Brain/Firm Brain | A | #6 |
| #8 | A7 event/audit | A | #2,#3 |
| #9 | B1 execution contracts | B | #1 |
| #10 | B2 model gateway | B | #9 |
| #11 | B3 tool registry/policy bridge | B | #9,#3 |
| #12 | B4 Work Compiler | B | #9,#4,#6 |
| #13 | B5 durable workflows | B | #9,#11 |
| #14 | B6 verification | B | #5,#6,#9 |
| #15 | B7 artifacts/approval | B | #13,#14 |
| #16 | B8 AI Workforce | B | #10,#12,#13,#14 |
| #17 | C1 connectors/authority | C | #3,#5 |
| #18 | C2 Legal Inbox | C | #4,#8 |
| #19 | C3 migration/import | C | #4,#5 |
| #20 | C4 Prove-It benchmark | C | #14,#19 |
| #21 | C5 staffing/economics | C | #4,#7,#8 |
| #22 | C6 developer SDK/manifest | C | #1 |
| #23 | C7 app registry/marketplace | C | #22,#3,#8 |
| #24 | C8 app capability tokens | C | #22,#3 |
| #25 | C9 app runtime gateway | C | #24,#11 |
| #26 | C10 install/update/revoke | C | #23,#24,#25 |
| #27 | C11 typed extension points | C | #25,#9,#13 |
| #28 | D1 production UI/API shell | Lead/A/C | #4,#12,#18,#23 |
| #29 | C12 workflow discovery/outcome learning | C | #7,#8,#16 |
| #30 | C13 Matter Twin | C | #7,#12,#14,#16 |
| #31 | D2 integrated proof slices | Lead/all | #7,#16,#26,#28,#29,#30 |

## Three-worker operating rule

At most three implementation issues may be actively dispatched at once, one per lane, unless the tech lead explicitly chooses a lead-owned integration task. Within a lane, run only issues whose direct predecessors are complete.

### Recommended initial dispatch

- Worker A: **#2 A1**
- Worker B: **#9 B1**
- Worker C: **#22 C6**

After #2/#3 are available, C can start #17 and #24. Worker B can proceed through #10/#11 once #9 exists. The lead should continuously keep all three lanes busy without introducing cross-lane private dependencies.

## Review rule

A worker PR must link its issue and report: changed contracts, tests run, negative/security tests, migrations, and any deviation. Deviations that change architecture must be stopped and routed through `ARCHITECTURE_CHANGE_POLICY.md`.
