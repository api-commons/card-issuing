# Card Issuing

The common card issuing vocabulary -- cards, the people who hold them, the authorizations
and transactions on them, the controls on what they can spend and the programmes they belong
to -- distilled from the APIs that already exist. Part of
[Schema Commons](https://github.com/api-commons/schema-commons), published by the
[API Commons](https://apicommons.org).

Built from the providers tagged Card Issuing, Issuing, Corporate, Virtual, Prepaid, Debit,
Commercial or Fuel Cards, Spend or Expense Management, Banking as a Service, Embedded
Finance, Neobank or Payments in [APIs.io](https://apis.io) that publish their own OpenAPI.
Only harvested contracts count -- nothing API Evangelist generated. Every number below is a
count of providers.

## What the market already agrees on

<!-- commons:summary -->
| Concept | Expose it | Readable schema | Core properties (share) | Collection named |
| --- | ---: | ---: | --- | --- |
| card | 65 | 60 | status 73%, id 67%, last4 60%, type 58% | cards 54, card 15, physical_cards 1, physical-cards 1 |
| cardholder | 28 | 27 | email 89%, last_name 74%, id 67%, first_name 63%, phone 59% | users 15, employees 6, user 6, cardholder 3 |
| authorization | 16 | 16 | amount 75%, id 50%, merchant 50% | authorizations 9, authorization 6, authorisations 3, authorisation 1 |
| card_transaction | 47 | 44 | amount 68%, id 68%, status 61%, merchant 59%, created_at 55%, type 50% | transactions 45, transaction 10 |
| spending_control | 8 | 8 | id 50% | limits 6, spend_limits 1, velocitycontrols 1, authcontrols 1 |
| card_program | 12 | 12 | created_at 75%, id 75%, name 67%, updated_at 50% | products 8, programs 3, card_programs 1, cardproducts 1 |

Cohort: 740 providers with harvested OpenAPI, 65 crosswalks. Of the providers exposing a card: 71% create, 80% list, 77% fetch, 40% update, 25% delete. Generated 2026-11-10 by build.py.
<!-- /commons:summary -->

Shares are of the providers with a readable resource schema for that concept. The
extended tier and the long tail of every concept are in [profile.yml](profile.yml).

Spending controls are the thinnest concept here: eight providers expose them as a resource
of their own, and their only shared property is `id` -- most issuers attach limits to the
card instead (`spending_controls` on the card is an extended property).

## Artifacts

- [openapi.yml](openapi.yml) -- OpenAPI 3.1 of the shared operations. No one serves it; it
  is the shape to map to. Lints clean against the
  [naming ruleset](https://github.com/api-commons/schema-commons/blob/main/ruleset/schema-commons-naming.yaml).
- [schema/](schema) -- JSON Schema 2020-12 per concept; each property carries
  `x-commons-tier`, `x-commons-share`, `x-commons-providers` and the spellings seen.
- [crosswalk/](crosswalk) -- one per provider, common name -> their name, with the harvested
  spec and operations each mapping was read from.
- [adopters.yml](adopters.yml) -- who exposes which concept, linked to their APIs.io page.
- [concepts.yml](concepts.yml) -- the only hand-written input: concepts, path segments with
  their qualifiers, aliases, reviewed exclusions and merged duplicates.
- [census/](census) -- the cohort and the raw census the profile is built from.

## Decisions

These are calls, not measurements, recorded so they can be revisited as the method matures.

- **This context exists because payments handed it over.** Payments found `/cards` returning
  `cvv`, `card_number` and a status -- issued cards, not stored ways to pay.
- **The path decides whether a card is issued or saved.** A card under a customer
  (`/customers/{id}/cards`: Stripe, Square, Omise, PAY.JP, Pin, Shift4) is a payment method
  and counts in payments; a card anywhere else is an issued card and counts here. The rule is
  written as a segment qualifier (`cards!customers|...`) in [concepts.yml](concepts.yml), and
  the providers whose bare `/cards` is still a saved or one-off card are excluded by hand
  with the reason.
- **The Payments tag is in the cohort** -- it is where the issued cards were found. It cannot
  flood the context: every concept but `card` either has an issuing-only segment or
  `requires: [card]`.
- **A cardholder is `/cardholders`, though issuers say `users`.** Twenty-one providers name
  them `users` and six `employees`; three say `cardholders`. `/users` counts here only at a
  provider that also issues cards, and the common path keeps the role name so it cannot be
  mistaken for every other API's users -- the same call as `requester` in help desk.
- **Card transactions are `/transactions`.** Issuers keep them top-level (45 providers), and
  `requires: [card]` means a bank ledger without cards never counts.
- **A card programme is `/card-programs`, though eight providers say `products`.** `products`
  means something else in every other context.
- **Affirm's single-use card**, minted for one checkout, is a payment instrument, not an
  issuing programme, and is excluded. Bread Financial's `/virtual-card/issuance` is issuing
  and stays.

## Rebuild

```bash
../schema-commons/scripts/run.sh .
```

## License

Artifacts CC BY-NC-SA 4.0 ([LICENSE](LICENSE)); code Apache 2.0 ([LICENSE-CODE](LICENSE-CODE)).
