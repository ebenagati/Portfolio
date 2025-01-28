# Wrapped in Mystery: Why Drake Always Makes the Cut

## Problem Statement 
There's a few things that are guaranteed in life, death, taxes and Drake being within my spotify wrapped. <br/><br/>
Spotify Wrapped is a yearly wrap up of listening habits that Spotify provide to it's users. This wrap up shows a user's Top 5 Artists, Top 5 Genres, Top 5 Songs and the amount of minutes of Spotify listened to across the year.<br/>
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

# Preliminary Steps
## Data Preperation
After receiving the data from Spotify in 43 different .json files, a concatenation is needed; I will use python to undertake this task.

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
This file is now imported into SQL Server Management Studio.<br/>

## Data Cleaning
Before we can start our data cleaning, let's perform a sense check of the data to ensure it makes sense.

```sql
Select top 5 *
FROM Spotify
```
<br/>Output:

| ID  | ts                         | platform                                           | ms_played | conn_country | ip_addr       | master_metadata_track_name                          | master_metadata_album_artist_name | master_metadata_album_album_name       | spotify_track_uri                              | episode_name | episode_show_name | spotify_episode_uri | audiobook_title | audiobook_uri | audiobook_chapter_uri | audiobook_chapter_title | reason_start | reason_end | shuffle | skipped | offline | offline_timestamp | incognito_mode |
| --- | -------------------------- | -------------------------------------------------- | --------- | ------------ | ------------- | --------------------------------------------------- | ---------------------------------- | -------------------------------------- | ---------------------------------------------- | ------------ | ----------------- | -------------------- | --------------- | ------------- | --------------------- | ------------------------ | ------------ | ---------- | ------- | ------- | ------- | ----------------- | -------------- |
| 0   | 2016-08-31T01:34:43Z       | Android OS 6.0.1 API 23 (samsung, SM-G920F)        | 307146    | GB           | 86.22.242.199 | Pisces                                             | Wretch 32                         | Wretchrospective (Deluxe Edition)      | spotify:track:0rwUVnDv5ceFRE8vBH7jFY            | NULL         | NULL              | NULL                 | NULL            | NULL          | NULL                  | NULL                     | clickrow     | trackdone  | 0       | 0       | 0       | NULL              | 0              |
| 1   | 2016-08-31T01:35:14Z       | Android OS 6.0.1 API 23 (samsung, SM-G920F)        | 31304     | GB           | 86.22.242.199 | Swaggalicious Interlude (feat. J.F.L.O.W.S)         | Wretch 32                         | Wretchrospective (Deluxe Edition)      | spotify:track:7zgSzRm37zIQYcxlJEEj26            | NULL         | NULL              | NULL                 | NULL            | NULL          | NULL                  | NULL                     | trackdone    | trackdone  | 0       | 0       | 0       | NULL              | 0              |
| 2   | 2016-08-31T11:24:20Z       | Android OS 6.0.1 API 23 (samsung, SM-G920F)        | 48196     | GB           | 86.22.242.199 | Swaggalicious (feat. Scorcher)                      | Wretch 32                         | Wretchrospective (Deluxe Edition)      | spotify:track:6g31cbqaHgEFNc6Lk5kdWH            | NULL         | NULL              | NULL                 | NULL            | NULL          | NULL                  | NULL                     | trackdone    | unexpected-exit-while-paused | 0       | 0       | 0       | NULL              | 0              |
| 3   | 2016-08-31T11:33:23Z       | Android OS 6.0.1 API 23 (samsung, SM-G920F)        | 396045    | GB           | 86.22.242.199 | The End Scene (Bonus Track)                         | Wretch 32                         | Wretchrospective (Deluxe Edition)      | spotify:track:0FAC7wsYaAN5uKaMCsBP3Z            | NULL         | NULL              | NULL                 | NULL            | NULL          | NULL                  | NULL                     | clickrow     | endplay    | 0       | 0       | 0       | NULL              | 0              |
| 4   | 2016-08-31T11:34:08Z       | Android OS 6.0.1 API 23 (samsung, SM-G920F)        | 45104     | GB           | 86.22.242.199 | Connect                                             | Drake                              | Nothing Was The Same - Deluxe           | spotify:track:1Sj81sMg37Hd4omn7Ow2qR            | NULL         | NULL              | NULL                 | NULL            | NULL          | NULL                  | NULL                     | clickrow     | endplay    | 0       | 0       | 0       | NULL              | 0              |

For ease of readability, I decide to change the field names, at this stage I can also review the data types and ensure they make sense:
| Column Name               | Data Type      |
| ------------------------- | -------------- |
| ID                        | int            |
| Timestamp                 | nvarchar(MAX)  |
| Platform                  | nvarchar(MAX)  |
| Milliseconds              | int            |
| Country                   | nvarchar(MAX)  |
| IP                        | nvarchar(MAX)  |
| Track                     | nvarchar(MAX)  |
| Artist                    | nvarchar(MAX)  |
| Album                     | nvarchar(MAX)  |
| Spotify_URI               | nvarchar(MAX)  |
| Episode_Name              | nvarchar(MAX)  |
| Episode_Show_Name         | nvarchar(MAX)  |
| Spotify_Episode_Uri       | nvarchar(MAX)  |
| Audiobook_Title           | nvarchar(MAX)  |
| Audiobook_URI             | nvarchar(MAX)  |
| Audiobook_Chapter_URI     | nvarchar(MAX)  |
| Audiobook_Chapter_Title   | nvarchar(MAX)  |
| Start_Reason              | nvarchar(MAX)  |
| End_Reason                | nvarchar(MAX)  |
| Shuffle                   | bit            |
| Skipped                   | bit            |
| Offline                   | bit            |
| Offline_Timestamp         | float          |
| Incognito_Mode            | bit            |     |

Secondly, I decided to check for null values, my check was for NULLs was based on the Artist field as this field is central to my analysis.

```sql
SELECT Artist, COUNT(*) as Null_Count
FROM Spotify
Group By Artist
HAVING Artist IS NULL
```
<br/>Output:
**Artist**|**Null\_Count**
:-----:|:-----:
NULL|424

<br/> Through taking a look for the data, it is clear to see that the reason for the NULLs present within the artist field is due to the fact my dataset includes podcasts data hence causing the NULLs within the Artist. 
<br/> We can check this theory through reconciling the number of NULLs with the number of rows where the episode_name is not null

```sql
SELECT Count(*) As Null_Podcasts
FROM Spotify
WHERE Episode_Name IS NOT NULL 
```
<br/>Output:
**Null\_Podcasts**
:-----:
424

```sql
DELETE FROM Spotify
WHERE Artist is NULL
```

<br/>Output:
<br/>*(424 rows affected)
<br/>Completion time: 2025-01-28T16:07:28.0616267+00:00*

Additionally we can remove the columns not useful for our analysis.
```sql
ALTER TABLE Spotify 
DROP COLUMN Episode_Name, Episode_Show_Name, Spotify_Eposide_Uri, Audiobook_Title, Audiobook_ID, Audiobook_Chapter_ID,Audiobook_Chapter_Title;
```

<br/>Subsequently, I deicde to check to see if there is any duplicate rows. Before I begin querying the data I have another inspection of the data and realise that a duplicate check may not be appropriate here due to a few reasons:
- The spotify_track_uri holds the unique track ID's however due to the fact tracks can be listened multiple times, this alone would not be appropriate
- A combination of spotify_track_uri & a timestamp for example cannot be used due to the fact the timestamps are not unique. This is because of Spotify's offline mode which allows music to be listened when a device is not connected to the internet. Music listened through this method are still documented however, the record of the user listening to these offline songs receive the timestamp of when device has re-connected to the internet hence creating the possibility of multiple duplicate timestamps
