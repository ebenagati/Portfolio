# Wrapped in Mystery: Why Drake Always Makes the Cut

## Problem Statement 
There's a few things that are guaranteed in life, death, taxes and Drake being within my spotify wrapped. <br/><br/>
Spotify Wrapped is a yearly wrap up of listening habits that Spotify provide to it's users. This wrap up shows a user's Top 5 Artists, Top 5 Genres, Top 5 Songs and the amount of minutes of Spotify listened to.<br/>
Over the years i've noticed a pattern of Drake appearing in my Top 5 Artists every year despite me not choosing to listen to Drake very often.<br/><br/>
This project attempts to investigate my listening habits and to discover whether Drake truly deserves to be within my top 5 artists each year.

## Dataset Overview
The dataset used for this project was colled from Spotify via requesting my Extended streaming history. The key columns can be found below.:

| Field Name                     | Description                                                                 |
|---------------------------------|-----------------------------------------------------------------------------|
| ID                              | Python Generated Index                                                       |
| ts                              | Timestamp of when the track stopped playing                                  |
| platform                        | Platform used when streaming the track (e.g. Android OS, iOS)               |
| ms_played                       | Number of Milliseconds the track was played for                              |
| conn_country                    | Country where the song was played                                            |
| ip_addr                         | IP address logged when the track is streamed                                |
| master_metadata_track_name      | Song name                                                                    |
| master_metadata_album_artist_name| Artist name of the song played                                               |
| master_metadata_album_album_name | Album name of the song played                                                |
| spotify_track_uri               | Spotify's unique identifier for track names                                  |
| episode_name                    | Name of the episode of the podcast                                           |
| episode_show_name               | Name of the podcast                                                          |
| spotify_episode_uri             | Spotify's unique identifier for podcast episodes                             |
| audiobook_title                 | Name of Audiobook                                                            |
| audiobook_uri                   | Spotify's unique identifier for audiobooks                                   |
| audiobook_chapter_uri           | Spotify's unique identifier for audiobook chapters                           |
| audiobook_chapter_title         | Name of Audiobook chapter                                                    |
| reason_start                    | Reason for song starting                                                     |
| reason_end                      | Reason for song ending                                                       |
| shuffle                         | True or False field whether shuffle mode was on                              |
| skipped                         | Indicates whether the user skipped the song                                  |
| offline                         | Indicates whether the track was played in offline mode or not               |
| offline_timestamp               | Timestamp of when offline mode was used                                      |
| incognito_mode                  | Indicates whether the track was played in incognito mode or not             |

<br/><br/>
A snippet of data can be seen below:<br/>
```json
    "ts": "2024-05-21T12:18:41Z",
    "platform": "windows",
    "ms_played": 204000,
    "conn_country": "GB",
    "ip_addr": "94.3.250.13",
    "master_metadata_track_name": "dewford town ~ pokémon ruby & sapphire lofi",
    "master_metadata_album_artist_name": "Kihambo",
    "master_metadata_album_album_name": "A Journey Through the Hoenn Region ~ Pokémon Ruby & Sapphire Lo-Fi",
    "spotify_track_uri": "spotify:track:5eCkNIhK5WGi935iHM5jVB",
    "episode_name": null,
    "episode_show_name": null,
    "spotify_episode_uri": null,
    "audiobook_title": null,
    "audiobook_uri": null,
    "audiobook_chapter_uri": null,
    "audiobook_chapter_title": null,
    "reason_start": "trackdone",
    "reason_end": "trackdone",
    "shuffle": true,
    "skipped": false,
    "offline": false,
    "offline_timestamp": 1716293700,
    "incognito_mode": false
 ```


<br/><br/>After receiving the data from Spotify in 43 different .json files, a concatenation is needed; I will use python to undertake this task.

<br/>Importing the neccessary libraries:
```jyup
import pandas as pd
import glob
import json
```
<br/>Identifying the correct file path:
```jyup
file_path = r"C:\Users\Eben\Documents\SQL Project\Spotify\*.json"
```
<br/>Reading of each .json file and storing them in a list
```jyup
all_files = glob.glob(file_path)
df_list = [pd.read_json(file) for file in all_files]
```
<br/>Concatenation of fields is performed here:
```jyup
df = pd.concat(df_list, ignore_index=True)
```
<br/>An export of the data is undertaken into a SQL friendly format. UTF-8 encoding is used here to accommodate for special characters such as Jhené Aiko within the Artist field:
```jyup
df.to_csv('SpotifyData.csv', index=True, encoding='utf-8')
```
