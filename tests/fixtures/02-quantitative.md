# A reorder trigger for a small parts store

Author: Project test-fixture authors. Date: 2026-10-29. Original synthetic technical article released under this project's MIT license; not a validated inventory policy.

## Definitions

This rule assumes a stable average demand rate d measured in units/day and a deterministic lead time L measured in days. A planner supplies a safety stock S measured in units. d, L and S must be nonnegative. The article does not provide a method for choosing S.

## Compute the trigger

The reorder point is R = d × L + S, measured in units. If input time units differ, convert them only when the conversion is stated or unambiguous. Missing time units require clarification.

Inventory position P = on_hand + on_order − allocated, all in units. Reorder when P ≤ R. A negative P means a shortage; it is not invalid solely because it is negative. Show both R and P and the comparison that leads to the decision.

## Scope

This is a trigger, not an order-sizing formula. It does not specify an order quantity, supplier choice, holding cost or service-level guarantee. Do not use it to invent those outputs. Variable lead time or rapidly changing demand falls outside the stated model; request a suitable policy rather than silently extending this one.
