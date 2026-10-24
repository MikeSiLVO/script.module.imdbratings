# IMDb Ratings

`script.module.imdbratings` gives Kodi add-ons IMDb ratings and vote counts from IMDb's ratings
dataset. Every add-on that uses it reads the same copy on disk.

## Dependency

```xml
<import addon="script.module.imdbratings" version="1.0.0"/>
```

## Look up a rating

```python
from imdbratings import get_rating

get_rating('tt0111161')  # (9.3, 3256723)
```

Returns the rating and vote count, or `None` for an id the dataset doesn't have. The first lookup on
an install downloads the dataset and waits for it. After that, lookups pick up newer datasets on
their own.

## Update on a schedule

For an add-on that updates the library when IMDb publishes new data:

```python
from imdbratings import dataset_date, refresh

refresh()
if dataset_date() != last_processed:
    ...  # update the library, then store dataset_date() as last_processed
```

`refresh()` checks IMDb and downloads a newer dataset if there is one, returning `True` when that
call downloaded one. Another add-on may have downloaded it first, so compare `dataset_date()` with
the date you last processed rather than relying on the return value.

Checks are shared by every add-on using the module:

- At most one check every 15 minutes. A call inside that window returns `False` without checking.
- After a failed download, checks pause for 6 hours.

Optional arguments:

| Argument | Behavior |
|---|---|
| `abort` | Called during a download. Return `True` to stop it; the previous ratings stay. |
| `on_download` | Called once, before a download begins. |

A download also stops when Kodi shuts down.

## Dataset date

`dataset_date()` returns the date IMDb published the loaded dataset, as IMDb sends it
(`Sat, 24 Oct 2026 00:52:05 GMT`), or `None` before the first download.

## Attribution

Information courtesy of IMDb (https://www.imdb.com). Used with permission.

The data comes from IMDb's non-commercial datasets.
