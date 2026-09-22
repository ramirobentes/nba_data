# NBA Data

Free, public NBA data covering 30 seasons (1996-97 through 2025-26), for both the regular season and the playoffs. The current season is updated daily.

All files are in [Parquet](https://parquet.apache.org/) format and can be loaded directly from a URL in R, Python, or any tool that reads Parquet. No account or API key needed.

## What's included

| Table | Description |
|---|---|
| `pbp` | Play-by-play: one row per game event (shots, fouls, turnovers, substitutions, etc.) |
| `possessions` | One row per possession |
| `lineup_stats` | One row per stint: each uninterrupted stretch of a game with the same ten players on the floor.

Each table has one file per season and season type. Every file includes `season` and `season_type` columns, and within each table all files share exactly the same columns and types, so seasons can be combined directly.

## File naming

```
{table}_{season_type}_{season}.parquet
```

- `season_type` is `regular` or `playoffs`
- `season` is the year the season **ends** (e.g. `2025` = the 2024-25 season)

Examples: `pbp_regular_2025.parquet`, `possessions_playoffs_2024.parquet`

All files are listed under this repo's [Releases](https://github.com/ramirobentes/nba_data/releases), with one release per table.

## How to load the data

Every file can be downloaded from:

```
https://github.com/ramirobentes/nba_data/releases/download/{table}/{file}
```

### R

```r
library(arrow)

url <- "https://github.com/ramirobentes/nba_data/releases/download/pbp/pbp_regular_2025.parquet"
pbp <- read_parquet(url)
```

Several seasons at once:

```r
library(arrow)
library(purrr)

base <- "https://github.com/ramirobentes/nba_data/releases/download"

pbp <- map(2020:2025, \(s) read_parquet(sprintf("%s/pbp/pbp_regular_%d.parquet", base, s))) |>
  list_rbind()
```

### Python

```python
import pandas as pd  # requires: pip install pandas pyarrow

url = "https://github.com/ramirobentes/nba_data/releases/download/pbp/pbp_regular_2025.parquet"
pbp = pd.read_parquet(url)
```

Several seasons at once:

```python
base = "https://github.com/ramirobentes/nba_data/releases/download"

pbp = pd.concat(
    [pd.read_parquet(f"{base}/pbp/pbp_regular_{s}.parquet") for s in range(2020, 2026)],
    ignore_index=True,
)
```

## Important notes on coverage

The underlying NBA feeds changed over the years, so some play-by-play columns only exist for part of the range. Every file still has every column: where a season's source didn't provide a column, its values are `NA`.

**Columns available only for 1996-97 to 2015-16:**
`reb_type`, `person1type`

**Columns available from 2016-17 onward:**
`off_team_abb`, `locX`, `locY`, `order`, `opt1`, `opt2`

**Columns available only for 2025-26 onward** (from the newer CDN feed):
`action_type`, `sub_type`, `qualifiers`, `descriptor`, `shot_result`, `shot_action_number`, `shot_distance`, `is_field_goal`, `side`, `area`, `area_detail`, `official_id`, `foul_drawn_person_id`, `time_actual`

**Exceptions to the above:**

- **2020-21 playoffs** follow the 1996-97 to 2015-16 column set, not the modern one. That season's playoff data was built with the older process because the newer one was missing a huge portion of a game.


## Updates

During the season, the current season's files are updated once a day. Files for past seasons don't change.

## Data dictionary

_Coming soon: a description of every column in each table._

## Source

The data is derived from the NBA's public play-by-play feeds and processed into the tables above. This project is not affiliated with or endorsed by the NBA.

## Questions or issues

Found a problem with the data? Please [open an issue](https://github.com/ramirobentes/nba_data/issues).

