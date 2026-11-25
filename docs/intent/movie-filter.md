# Intent: find family-appropriate movies quickly
Author: ten101popo and bankjunjan, for the customer proxy (a parent of an 8-year-old). Status: draft.

## Problem
A parent choosing a movie for a child has to open the movies one by one to check each rating.
The list is long, so choosing takes too long and the parent gives up.

## Proposed outcome
The parent picks one rating and sees only the movies with that rating.
In the full list the newest movies come first.

## Affected users and systems
Parents first, then every moviegoer. The movie catalog of Rotten Potatoes (Movie, Rating and
MovieCatalog from L2). There is no web page yet: the behaviour is checked through the domain classes.

## Constraints
The ratings are exactly G, PG, PG-13, R and NC-17. No login and no personal data.
Plain Java this week, no database.

## Open questions
What should the parent see when no movie has the chosen rating?
Should several ratings be selectable at once?
