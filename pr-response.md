# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:**
Renamed save_to_watchlist() to add_to_watchlist() in services/watchlist_service.py so it matches the verb_to_noun convention the rest of the codebase uses (add_to_collection is the example given in CONTRIBUTING.md). Updated the one place that called it, routes/watchlist/watchlist.py, both the import line and the actual call. Also tweaked the first line of the docstring since it still said "Save a film..."

**How I verified:**
Searched the whole repo for save_to_watchlist after the rename, nothing came back, so no call sites were missed. Also ran pytest tests/ just to make sure I didn't break anything else — still 4 passing.

## Comment 2 — Deduplication
**What I did:**
Added the same dedup check that add_to_collection() has, just for watchlist. Made a new AlreadyInWatchlistError exception, and before add_to_watchlist() inserts a new WatchlistEntry it now checks if one already exists for that user_id/film_id and raises if so.

**How I verified:**
Ran pytest tests/ again, still passing, but that's not really a real test of this since there's no watchlist test file yet (doing that for comment 3). Mostly just compared it line by line against add_to_collection()/AlreadyInCollectionError to make sure I copied the pattern correctly. One thing I noticed while doing this: the watchlist route doesn't actually catch FilmNotFoundError or this new AlreadyInWatchlistError the way collection.py does, so right now both would just 500 instead of returning a proper 404/409. Leaving that alone for now since it's not what this comment asked for, but it'll probably need fixing.

## Comment 3 — Missing test
**What I did:**
Created tests/test_watchlist.py since it didn't exist before. Copied the app/sample_user/sample_film fixtures straight from test_collection.py so the setup matches the rest of the codebase. Wrote test_add_to_watchlist_nonexistent_film_raises, the watchlist equivalent of test_add_to_collection_nonexistent_film_raises — it asserts add_to_watchlist() raises FilmNotFoundError when given a film_id that isn't in the db. Used an int for the fake id (99999) instead of a UUID since Film.id is still an integer on this branch, not a UUID yet.

**How I verified:**
Ran pytest tests/test_watchlist.py -v and it passed. Right now that's the only test in the file CONTRIBUTING.md says new service functions need a happy path test and a duplicate/conflict test too, so this file is still incomplete against that bar. I left the imports for get_watchlist and AlreadyInWatchlistError in there even though they're unused yet, planning to add those tests next.

## Comment 4 — Default visibility
**My position:**
**Reasoning:**
**Tradeoff acknowledged:**

## Comment 5 — Sort order
**My position:**
**Reasoning:**
**Engagement with reviewer's point:**

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->
