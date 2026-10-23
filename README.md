# arbitrage2

A sports betting arbitrage scanner I built in high school (2021-2022) and have been meaning to clean up and push to GitHub ever since.

The idea: sportsbooks don't always agree on odds. If you pull odds for the same match from enough different books, every so often you'll find a combination where betting every possible outcome (team 1, team 2, and the tie) across different books guarantees a profit no matter who wins, because the books' odds disagree with each other more than they disagree with reality. That's an arbitrage opportunity.

## What it does

- Pulls soccer odds for US, UK, and EU bookmakers from [The Odds API](https://the-odds-api.com/)
- For every match, compares every pair of bookmakers against each other
- Converts American odds to implied win probabilities and checks whether the combined implied probability across all three outcomes (team 1 / tie / team 2) drops below 100%. If it does, that gap is the arbitrage
- Works out how to split your stake across the two books so you come out ahead regardless of the result
- Logs hits to a Google Sheet automatically (via a Google service account + gspread) and keeps an eye on API quota usage so I didn't blow through my request limit
- Started as a script with a Sheets-based output, then I wrapped it in a small Django app so I could run it from a web form instead of the command line. Posted it on Reddit and it picked up about 20 users

## Stack

Python, Django, [The Odds API](https://the-odds-api.com/), gspread / Google Sheets API

## Running it

You'll need a few things set as environment variables instead of hardcoded (the original version had these baked in, which is exactly the kind of thing I went back and fixed before cleaning this repo up):

```
ODDS_API_KEY=<your odds-api.com key>
DJANGO_SECRET_KEY=<any random string>
GOOGLE_SHEETS_CREDENTIALS_PATH=<path to a Google service account JSON file>
```

Then:

```
pip install -r requirements.txt
cd updated_betting2
python manage.py runserver
```

## Known rough edges

This was a high-school project, not production software, so there's stuff I'd do differently now:
- The bookmaker comparison and template rendering still assume some things about the local environment it was originally run in
- No tests
- Arbitrage windows close fast in real sports betting, so this was always more of a "see if the idea works" project than something I'd trust with real money unscaled

If I revisit it, the main thing worth doing is decoupling it from Google Sheets entirely and giving it a real database-backed history instead.
