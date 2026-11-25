Tool: none; lab kit draft by Claude Haiku 5.5
Date: 2026-11-24 (one run, not edited)
Prompt: kit s19 prompt + kit s17 intent

I can't write `spec.md` to disk because I have no file-writing tool in this session. The full spec is below so you can save it as `spec.md`.

```markdown
# Spec: Filter family-appropriate movies by rating

Source: intent.md (draft). Scope: domain classes Movie, Rating and MovieCatalog. Plain Java, in memory, no database, no login, no personal data.

## 1. Functional requirements

- **REQ-1**: The system shall recognise exactly five ratings: G, PG, PG-13, R, NC-17.
- **REQ-2**: Every movie in the catalog shall have exactly one rating from the set in REQ-1. A movie with any other rating value shall be rejected when it is added to the catalog.
- **REQ-3**: Given one rating, the filter operation shall return every catalog movie whose rating equals that rating.
- **REQ-4**: The result of the filter in REQ-3 shall contain no movie whose rating differs from the selected rating.
- **REQ-5**: The filter operation shall accept exactly one rating per call.
- **REQ-6**: The unfiltered movie list shall be ordered by release date, newest first.
- **REQ-7**: The filtered list shall be ordered by release date, newest first, using the same rule as REQ-6.
- **REQ-8**: Where two movies have the same release date, their relative order shall be deterministic (the same input always gives the same order). *[Tie-break rule is an assumption; see §4.]*
- **REQ-9**: If the selected rating matches no movie, the filter operation shall return an empty list and shall not throw an exception. *[Pending open question; see §4.]*
- **REQ-10**: If the selected rating is not one of the five values in REQ-1 (including null and empty input), the filter operation shall reject the call with an error that names the five allowed ratings.
- **REQ-11**: The filter operation shall not modify the catalog. Calling it twice with the same rating shall return equal results.
- **REQ-12**: The system shall not require a login, and shall not store or request personal data of any user.
- **REQ-13**: The catalog and all filtering shall run in memory using plain Java, with no database access.
- **REQ-14**: The filter result shall include each matching movie's title, rating and release date.

## 2. User stories

### Story 1: Filter by rating
**As a** parent choosing a movie for my 8-year-old,
**So that** I see only the movies I'm willing to let my child watch, without opening each one,
**I want** to select one rating and see only the movies with that rating.

Acceptance criteria:
- AC-1.1: Selecting G, PG, PG-13, R or NC-17 returns only movies with that rating (REQ-3, REQ-4).
- AC-1.2: Every catalog movie with the selected rating appears in the result (REQ-3).
- AC-1.3: Selecting a value outside the five ratings is rejected with an error naming the allowed values (REQ-10).
- AC-1.4: Selecting a rating with no matching movie returns an empty result without an exception (REQ-9).
- AC-1.5: The selection takes one call and needs no login (REQ-5, REQ-12).

Gherkin (Story 1):

```gherkin
Feature: Filter movies by rating

  Scenario: Parent sees only movies with the chosen rating (happy path)
    Given the catalog contains movie "Alpha" rated G released 2020-05-01
    And the catalog contains movie "Bravo" rated G released 2023-03-15
    And the catalog contains movie "Charlie" rated PG released 2022-07-10
    And the catalog contains movie "Delta" rated R released 2021-11-20
    When the parent selects rating "G"
    Then the result contains "Bravo" and "Alpha" in that order
    And the result contains no movie rated PG or R

  Scenario: Chosen rating has no matching movie (sad path)
    Given the catalog contains no movie rated NC-17
    When the parent selects rating "NC-17"
    Then the result is an empty list
    And no exception is raised

  Scenario: Selection is not one of the five ratings (sad path)
    Given the catalog contains movies with valid ratings
    When the parent selects rating "PG-14"
    Then the selection is rejected
    And the error names G, PG, PG-13, R and NC-17 as the allowed ratings
```

### Story 2: Newest first
**As a** parent or moviegoer browsing the catalog,
**So that** I see recent releases before older films,
**I want** both the full list and any filtered list to show the newest movies first.

Acceptance criteria:
- AC-2.1: The full list is ordered by release date, newest first (REQ-6).
- AC-2.2: A filtered list uses the same order (REQ-7).
- AC-2.3: Movies with the same release date appear in the same order on every call (REQ-8).

### Story 3: Clear empty result
**As a** parent who selected a rating with no movies,
**So that** I know the problem is the rating and not a system fault, and can choose another rating,
**I want** an empty result, not an error, when no movie matches.

Acceptance criteria:
- AC-3.1: An empty match returns an empty list (REQ-9).
- AC-3.2: The empty result is distinguishable from a rejected selection (REQ-10).
- AC-3.3: After an empty result, a new selection works without restarting the system (REQ-11).

## 3. Open questions (from intent) and how this spec treats them

- **Q1: What should the parent see when no movie has the chosen rating?** Not decided. This spec assumes an empty list (REQ-9). Change REQ-9 if the team chooses a message instead.
- **Q2: Can several ratings be selected at once?** Not decided. This spec allows one rating per call (REQ-5). Multi-select would need a new requirement and a new acceptance test.

## 4. Assumptions not stated in the intent

1. Each movie in the catalog has exactly one rating and a release date (needed for REQ-2 and REQ-6).
2. "Newest" means the release date, not the date the movie was added to the catalog.
3. Ratings are matched as exact values. Case and spacing rules (e.g. "pg-13") are not defined; this spec treats them as invalid (REQ-10).
4. The tie-break for equal release dates is not in the intent. REQ-8 requires determinism only, not a specific rule.
5. The filtered list is ordered the same way as the full list (REQ-7). The intent only mentions the full list.
6. The catalog is loaded before filtering and does not change during a filter call.
7. The three acceptance stories are tested at the domain-class level, because there is no web page yet (from intent).
8. The rating is taken as the only measure of suitability. The spec does not judge content.
9. The existing Movie, Rating and MovieCatalog classes from Lab 2 can be extended to meet these requirements.
10. No ratings beyond the five in the constraints exist, including TV or unrated labels.

## 5. Areas of concern

- **Suitability is not the same as rating.** A G or PG film may still contain content a parent objects to. The spec only filters by rating, and the parent may expect more.
- **Unrated movies.** The catalog may hold movies with no rating. REQ-2 rejects them, but the team should decide whether they should be excluded or fixed on entry.
- **Missing or future release dates.** Ordering by "newest" fails if the date is absent or is in the future. No rule is defined.
- **"Quickly" is not measured.** The problem says choosing takes too long, but there is no time target or size limit for the list.
- **Success is not measured.** The intent does not say how the team will know parents stopped giving up. No metric or validation with real parents is planned.
- **Single customer proxy.** The intent is written for one proxy parent. Requirements may not match the needs of parents in general.
- **Ratings can change.** The five labels and their meanings may change over time, which would break REQ-1 and REQ-2.
- **Open questions Q1 and Q2 are unresolved.** The chosen answers determine REQ-5, REQ-9 and Story 3.
- **In-memory limits.** Plain Java with no database limits catalog size and makes persistence impossible. This is acceptable for the week but should be recorded as a known limit.
```
