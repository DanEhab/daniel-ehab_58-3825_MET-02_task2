# Task 2 — Contracts, nested data and a star schema

- **Name:** Daniel Ehab
- **ID:** 58-3825
- **Major and lab:** MET-02

## What I did

I read the 283 lines of bookings.jsonl and validated each against the contract's Pydantic models. 24 lines went to quarantine.jsonl: 4 with a malformed student.id, 4 whose total was not the sum of qty × price, 3 with a ticket type outside the list, 3 with a qty below 1, 3 booked after their event's date, 2 with a null event, 2 with no tickets, and 3 duplicate copies of a booking_id. That leaves 259 bookings. The duplicate check runs after validation because keeping the first copy only makes sense among copies that are real bookings: run first, it could keep a broken copy, quarantine the corrected re-send as a duplicate, and count broken copies under the wrong reason. Swapping them changes no count here, since all 3 re-sent copies passed the contract; a two-line test in the notebook shows when it would. The drifting field is promo_code, missing from the brief's example. It first appears in B0125, booked 2026-10-20 01:05 UTC, and every valid booking after that has it. Documents without it still pass, because the model declares it optional, and get a null promo_code. The booking's total is not a column of fact_ticket_sales because the fact has one row per ticket entry, and total belongs to the whole booking. SUM(total) over the fact gives 42,000 EGP instead of the real 31,285, because the 55 bookings with two ticket entries are counted twice. Summing amount gives the right 31,285.
