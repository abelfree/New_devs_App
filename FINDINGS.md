# Assignment Findings - Abel Takele

Loom walkthrough: https://www.loom.com/share/b17a3de428ea498a8889ef34fb857c39

## Bugs fixed

1. Cross-tenant cache leak (backend/app/services/cache.py)
   The cache key was scoped only by property_id, so tenants sharing a property
   id served each other's cached revenue. The DB query was correctly filtered
   by tenant, but the cache sat in front of it. Fixed by including tenant_id
   in the cache key.

2. Timezone month boundaries (backend/app/services/reservations.py)
   Month ranges were built as naive datetimes while check_in_date is stored in
   UTC, so bookings near month edges fell into the wrong month for non-UTC
   properties. properties.timezone existed in the schema but was unused. Fixed
   by building boundaries in the property's timezone and converting to UTC.

3. Float precision (backend/app/api/v1/dashboard.py)
   Exact Decimal totals were cast to float before returning, reintroducing
   binary rounding error. Fixed by keeping Decimal and returning an exact
   string.

## Found but not fixed

Currency is hardcoded to USD while reservations carry multiple currencies, and
totals are summed without conversion. This needs a decision on exchange rate
source and date, so it is flagged rather than guessed at.
