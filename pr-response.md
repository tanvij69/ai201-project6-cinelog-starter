# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:** Renamed save_to_watchlist() to add_to_watchlist() in services/watchlist_service.py, 
updated the docstring, and updated the one call site in routes/watchlist/watchlist.py.
**How I verified:** Ran Get-ChildItem -Recurse -Filter *.py | Select-String -Pattern "save_to_watchlist" 
to confirm no references remained, then ran pytest tests/ -v — all 4 tests passed.

## Comment 2 — Deduplication
**What I did:** Added an `AlreadyInWatchlistError` exception and a duplicate check in 
`add_to_watchlist()`, mirroring the pattern in `add_to_collection()`: query for an existing 
`WatchlistEntry` with the same `user_id` and `film_id` before creating a new one, and raise 
if one is found.
**How I verified:** Ran `pytest tests/ -v` — all 4 existing tests passed. Committed separately 
from the rename (Comment 1) as instructed.

## Comment 3 — Missing test
**What I did:** Created tests/test_watchlist.py with test_add_to_watchlist_nonexistent_film_raises,
mirroring test_add_to_collection_nonexistent_film_raises in structure — same fixtures 
(app, sample_user, sample_film), same fake UUID pattern, same pytest.raises assertion.
**How I verified:** Ran pytest tests/test_watchlist.py -v — passed. Ran full pytest tests/ -v 
to confirm no regressions.

## Comment 4 — Default visibility
**My position:** Watchlists should default to `public=True`.
**Reasoning:** My reasoning is based on what I'm optimizing for. Even though CineLog is 
primarily a film tracker rather than a social network, one benefit of having public watchlists 
is that users can discover movies through other people's lists without any extra setup. Making 
watchlists public by default reduces friction for users who want to share recommendations or 
compare what they're planning to watch, while still allowing users to change the visibility 
if they prefer.
**Tradeoff acknowledged:** The main downside is that a watchlist can reveal a user's future 
viewing interests, which some people may consider more personal than the films they've already 
watched. Those users would need to manually change the visibility setting. I think that's an 
acceptable tradeoff because my decision prioritizes discoverability and sharing, but I 
recognize that choosing `public=False` would better prioritize privacy from the start.

**AI usage note:** Used AI to stress-test my draft by asking what counterargument a reviewer 
would raise. It pointed out my first draft assumed "most users want discovery" without 
justifying that, and that I hadn't distinguished why a watchlist (future intent) might feel 
more private than a collection (past behavior). I revised to remove the unsupported "most 
users" claim and add the watchlist-vs-collection distinction.

## Comment 5 — Sort order
**My position:** I agree with changing the default sort order to date added.
**Reasoning:** For a watchlist, users are usually keeping track of what they want to watch 
next rather than maintaining a permanent catalog. Showing the most recently added films first 
makes it easier to find the movies they just discovered or saved without having to search 
through the list. An alphabetical order is predictable, but it doesn't reflect how a watchlist 
changes over time.
**Engagement with reviewer's point:** I agree with the maintainer's point that many users will 
want to see what they added recently. Since a watchlist is updated whenever users discover new 
movies, recency is more closely tied to how they use the feature day to day. Alphabetical 
sorting can still be useful, but I think it would make more sense as an optional sorting 
preference rather than the default.

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->