Title: The final pitch (usually)
Date: 2025-08-25
Modified: 2025-08-26
Category: Miscellaneous
Tags: baseball

Bottom of the 9th, bases loaded, full count, the home team trailing by one run. How often does this cliché actually occur in an MLB game?

To answer this question, let's obtain the data from the Retrosheet website. Here is the statement required by the website:
> The information used here was obtained free of charge from and is copyrighted by Retrosheet. Interested parties may contact Retrosheet at "www.retrosheet.org".

You can download [event data](https://www.retrosheet.org/game.htm) and its [parsed version](https://retrosheet.org/downloads/plays.html) from the website. I only included the regular season games.

The code runs in the following way:

1. From the parsed version, filter for at-bats with bot ninth, 3-2 and bases loaded. (it's difficult to recover the "bases loaded" condition from the event file)
2. For some reason, the parsed version does not contain the score. So I take the IDs of the games found in the previous step and search for them in the event files.
3. Recover the score at the relevant at-bats and filter for "1 run down".

I manually downloaded the data between 2010 and 2024.

This function filters for the at-bats that I want:
```python
def filter_df(df):
	bases_loaded = ~df["br1_pre"].isna()*~df["br2_pre"].isna()*~df["br3_pre"].isna()
	bot_ninth = (df["inning"]=="9")*(df["top_bot"]=="1")
	two_outs = df["outs_pre"]=="2"
	full_count = df["count"]=="32"
	
	filtered_df = df[bases_loaded*bot_ninth*two_outs*full_count]
    return filtered_df
```
This function returns the plays (and not other information) within the game that I am searching for:
```python
def get_play_data(event_list, gid):
    play_list = []
    recording = False
    for l in event_list:
        parsed = l.split(",")
        if not recording and parsed[0]=="id" and parsed[1].strip()==gid:
            recording = True
        elif recording:
            if parsed[0]=="id":
                break
            elif parsed[0]=="play":
                play_list.append(parsed)
    return play_list
```
This function recovers the score state based on the plays. ("H" without an alphanumeric around it refers to a runner reaching home; but this excludes the run scored by the batter hitting a home run "HR". "SBH" refers to stealing home.) I am not entirely certain this always returns the correct score, but it works for the cases that I checked.
```python
def recover_score(play_list):
    home_runs = 0
    away_runs = 0

    for play in play_list:
        event_split = re.split("[^a-zA-Z0-9]", play[6].strip())
        run_count = event_split.count("H") + event_split.count("HR") + event_split.count("SBH")
        if run_count > 0:
            if play[2] == "0":
                away_runs += run_count
            else:
                home_runs += run_count
    return home_runs, away_runs
```
The main program:
```python
from glob import glob
import re

import pandas as pd

searched_rows = []

for year in range(2010, 2025):
    df = pd.read_csv(f"{year}plays.csv", dtype=str)
    filtered_df = filter_df(df)

    for idx, row in filtered_df.iterrows():
        if row["event"] == "NP":
            continue

        team = row["gid"][:3]
        event_file_name = glob(f"{year}eve/{year}{team}.EV*")[0]
        with open(event_file_name, "r") as event_file:
            event_lines = event_file.readlines()

        play_list = get_play_data(event_lines, row["gid"])
        game_upto_searched_play = []
        for play in play_list:
            if (play[1] == row["inning"] and play[2] == row["top_bot"] 
                and play[3] == row["batter"] and play[4] == row["count"]
                and play[6].strip() == row["event"]):
                break
            else:
                game_upto_searched_play.append(play)

        home_runs, away_runs = recover_score(game_upto_searched_play)
        if away_runs - home_runs == 1:
            searched_rows.append(row)

for row in searched_rows:
    print(row["gid"], row["event"])
```
This prints the following events (the notation is explained [here](https://www.retrosheet.org/eventfile.htm)):
```text
MIN201010020 S8/G6M+.3-H;2-H;1-2
PIT201004170 W.3-H;2-3;1-2
ATL201108150 S8/G4M.3-H;2-H;1-2
NYN201104141 9/F9D+
SLN201109240 W.3-H;2-3;1-2
WAS201104300 K
MIA201406130 W.3-H;2-3;1-2
TEX201407290 7/F7D
SFN201505100 W.3-H;2-3;1-2
ANA201608170 5!3/G
SLN201608080 W.3-H;2-3;1-2
SDN201707140 9/F9D+
BOS201807260 K
TOR201807020 W.3-H;2-3;1-2
MIN201907160 5/P5F
PIT201908160 W.3-H;2-3;1-2
CIN202007270 8/L8D
ATL202108240 7/F78XD
DET202109260 K
PHI202106220 63/G56
NYN202205150 K
CHN202305310 7/F7LD
SDN202304160 K
NYN202405240 53/G56S
```
That's 24 occurrences in the space of 15 years, or $14\times2430+900=34920$ games (one pandemic-shortened season), so there is a 0.07% chance of witnessing it in each game (or once in 1,455 games).
The breakdown of the at-bats:

- 5 strikeouts
- 10 batted ball outs
- 7 walks (none since 2020)
- 2 hits (both ending the game, in 2010 and 2011)

This gives us an OBP of $9/24=0.375$ and batting average of $2/17=0.118$. So throw a strike?

Finally, we can see that the Angels game in 2016 contains `5!3/G`, where `!` refers to a great play. The [link](https://www.mlb.com/video/seager-s-game-saving-play-in-9th-c1065024383?q=ContentTags%20==%20[%22defense%22,%22gamepk-448661%22]%20Order%20By%20Timestamp%20DESC&pt=Defensive%20Highlights).
