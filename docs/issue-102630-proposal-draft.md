# Internal proposal draft for Expensify/App#102630

**Not submitted upstream.** The issue is currently marked `Help Wanted`, but the required full reproduction has not been run with the account used in the issue. Several existing proposals already address the same root cause. Expensify requires any new proposal to be *meaningfully different*, and the author to be selected before an upstream PR. This document is a working draft, not an accepted bounty application.

## Proposal

### What is the root cause of that problem?

The address page's state and country fields have two competing sources: local values synchronized from the saved Onyx address, and URL parameters consumed by child state/country selectors. A child can apply `?state=MO` and clear it before the parent sync restores a saved state such as `CA`. When the saved/default country is not US, `AddressForm` renders a text state field rather than `StateSelector`, so the URL state is not consumed at all. A country callback also falls through to ZIP handling when the country is unchanged.

### What changes do you think we should make in order to solve the problem?

1. Let `AddressPage` read and prioritize validated route country/state values before rendering the form. A US state should imply US unless a conflicting explicit country is provided.
2. Protect the URL override and user-edited values from a later saved-address sync. Handle route params arriving after initial page render.
3. Return from *every* country change, even if it selects the current country, to avoid accidentally overwriting ZIP.
4. Add UI coverage for saved non-US address + valid US state, saved US address + URL state followed by Onyx updates, same-country callbacks, and conflicting URL params.

### What alternative solutions did you explore? (Optional)

A guard in `StateSelector` alone cannot handle non-US addresses, because that component is not mounted. Forcing the country to US alone misses the saved-address synchronization race. An unconditional suppression of Onyx updates risks ignoring unrelated late-loading saved values; this needs focused tests before an upstream submission.

## Existing proposal overlap and unresolved requirements

The page-level URL-priority fix, cross-country override, ZIP fallthrough, and late-Onyx behavior have already been proposed by multiple contributors, including @cocvn157, @MannA-001, and @cwwjacobs. **This draft does not currently provide a distinct enough approach to meet Expensify's proposal rules.** Do not post it as a competing proposal without new evidence and a meaningful differentiator.

- Preliminary branch: https://github.com/HamzaZahid172/App/tree/wip/102630-address-url-precedence
- Upstream issue: https://github.com/Expensify/App/issues/102630
- Manual staging validation: not completed
- Focused Jest regression tests: added but not executed
- Upstream PR: not opened
- Bounty assignment: not granted
