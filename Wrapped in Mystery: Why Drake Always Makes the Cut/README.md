# Wrapped in Mystery: Why Drake Always Makes the Cut

## Problem Statement 
There's a few things that are guaranteed in life, death, taxes and Drake being within my spotify wrapped. <br/><br/>
Spotify Wrapped is a yearly summary of listening habits that Spotify provide to it's users. This wrap up shows a user's Top 5 Artists, Top 5 Genres, Top 5 Songs and the amount of minutes of Spotify listened to across the year.<br/>
Over the years i've noticed a pattern of Drake appearing in my Top 5 Artists every year despite me believing that I do not listen to that much Drake.<br/><br/>
This project attempts to investigate my listening habits and to discover whether Drake truly deserves to be within my top 5 artists each year.

## Dataset Overview
The dataset used for this project was collected from Spotify via requesting my Extended streaming history. The key columns can be found below:

| Field Name                     | Description                                                                 |
|---------------------------------|-----------------------------------------------------------------------------|
| ID                              | Python Generated Index                                                       |
| ts                              | Timestamp of when the track stopped playing                                  |
| platform                        | Platform used when streaming the track (e.g. Android OS, iOS)               |
| ms_played                       | Number of Milliseconds the track was played for                              |
| conn_country                    | Country where the song was played                                            |
| ip_addr                         | IP address logged when the track was streamed                                |
| master_metadata_track_name      | Song name                                                                    |
| master_metadata_album_artist_name| Artist name of the song played                                               |
| master_metadata_album_album_name | Album name of the song played                                                |
| spotify_track_uri               | Spotify's unique identifier for each track                                  |
| episode_name                    | Name of the episode of the podcast                                           |
| episode_show_name               | Name of the podcast                                                          |
| spotify_episode_uri             | Spotify's unique identifier for podcast episodes                             |
| audiobook_title                 | Name of Audiobook                                                            |
| audiobook_uri                   | Spotify's unique identifier for audiobooks                                   |
| audiobook_chapter_uri           | Spotify's unique identifier for audiobook chapters                           |
| audiobook_chapter_title         | Name of Audiobook chapter                                                    |
| reason_start                    | Reason for song starting                                                     |
| reason_end                      | Reason for song ending                                                       |
| shuffle                         | True or False field indicating whether shuffle mode was on                              |
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

An additional dataset which has been retrieved from https://skiley.net will also be used in order to facilitate analysis upon my Liked Songs.

| Field Name         | Description                                                               |
|--------------------|---------------------------------------------------------------------------|
| artistName         | Name of the artist                                                       |
| albumArtistsNames  | Name of the artists credited on the |
| albumName          | Name of album                                                            |
| albumPopularity    | Indicates the popularity of the album at the time. with 100 being the most popular album recently |
| isLikedByUser      | Binary field which indicates whether a song is liked by the user         |
| trackDuration      | Length of the track                                                      |
| trackUri           | Unique Identifier for track                                             |
| trackUrl           | URL of the track                                                         |
| trackName          | Name of the song                                                         |

A snippet can be seen below:
| artistName  | albumArtistsNames | albumName                                                   | albumPopularity | isLikedByUser | trackDuration | trackUri                               | trackUrl                                                                  | trackName                        |
|-------------|------------------|--------------------------------------------------------------|-----------------|--------------|--------------|-----------------------------------------|--------------------------------------------------------------------------|----------------------------------|
| Suzy        | Suzy             | While You Were Sleeping, Pt. 13 (Original Television Soundtrack) | 31              | TRUE         | 03:26:00     | spotify:track:1ZnSDGtDDta9qFNGEVoZbo  | [Track Link](https://open.spotify.com/track/1ZnSDGtDDta9qFNGEVoZbo)     | I Wanna Say To You              |
| JANNABI     | JANNABI          | LEGEND                                                       | 50              | TRUE         | 04:57:00     | spotify:track:1deQhoZakgMT5fw0t9zliD  | [Track Link](https://open.spotify.com/track/1deQhoZakgMT5fw0t9zliD)     | dreams, books, power and walls  |
| Central Cee | Central Cee      | CAN'T RUSH GREATNESS                                         | 75              | TRUE         | 02:02:00     | spotify:track:2Zej1ZTJJNDcRj8e8iWUUo  | [Track Link](https://open.spotify.com/track/2Zej1ZTJJNDcRj8e8iWUUo)     | Ten (feat. Skepta)              |


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
<br/>Concatenation is performed here:
```jyup
df = pd.concat(df_list, ignore_index=True)
```
<br/>An export of the data is undertaken into a SQL friendly format. UTF-8 encoding is used here to accommodate for special characters such as Jhené Aiko within the Artist field:
```jyup
df.to_csv('SpotifyData.csv', index=True, encoding='utf-8')
```
This file is now imported into SQL Server Management Studio.<br/>

The Liked Songs table does not require any Python related actions, hence this can be imported into SQL Management Studio without any preliminary actions.

## Data Cleaning
Before I can start the data cleaning, I perform a sense check of the data to ensure it makes sense.

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

```sql
Select top 5 *
from SpotifyLikedSongs
```
<br/>Output:
| artistName  | albumArtistsNames | albumName                                                   | albumPopularity | isLikedByUser | trackDuration       | trackUri                               | trackUrl                                                                  | trackName                        |
|-------------|------------------|--------------------------------------------------------------|-----------------|--------------|--------------------|-----------------------------------------|--------------------------------------------------------------------------|----------------------------------|
| Suzy        | Suzy             | While You Were Sleeping, Pt. 13 (Original Television Soundtrack) | 31              | TRUE         | 03:26:00.0000000   | spotify:track:1ZnSDGtDDta9qFNGEVoZbo  | [Track Link](https://open.spotify.com/track/1ZnSDGtDDta9qFNGEVoZbo)     | I Wanna Say To You              |
| JANNABI     | JANNABI          | LEGEND                                                       | 50              | TRUE         | 04:57:00.0000000   | spotify:track:1deQhoZakgMT5fw0t9zliD  | [Track Link](https://open.spotify.com/track/1deQhoZakgMT5fw0t9zliD)     | dreams, books, power and walls  |
| Central Cee | Central Cee      | CAN'T RUSH GREATNESS                                         | 75              | TRUE         | 02:02:00.0000000   | spotify:track:2Zej1ZTJJNDcRj8e8iWUUo  | [Track Link](https://open.spotify.com/track/2Zej1ZTJJNDcRj8e8iWUUo)     | Ten (feat. Skepta)              |
| Central Cee | Central Cee      | CAN'T RUSH GREATNESS                                         | 75              | TRUE         | 03:21:00.0000000   | spotify:track:25fvND4h7BT76CB77Mjsm3  | [Track Link](https://open.spotify.com/track/25fvND4h7BT76CB77Mjsm3)     | Limitless                       |
| Central Cee | Central Cee      | CAN'T RUSH GREATNESS                                         | 75              | TRUE         | 03:02:00.0000000   | spotify:track:5tgSjvWO685Z0mYKefArMI  | [Track Link](https://open.spotify.com/track/5tgSjvWO685Z0mYKefArMI)     | CRG (feat. Dave)                |




For ease of readability, I decide to change the field names, at this stage I can also review the data types and ensure they make sense:
**<br/><br/>Spotify Data Table:**
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
| Offline_Timestamp         | nvarchar(MAX)  |
| Incognito_Mode            | bit            |

**Spotify Liked Songs Table:**

| Column Name     | Data Type       |
|-----------------|-----------------|
| Artist          | nvarchar(MAX)   |
| Album_Artists   | nvarchar(MAX)   |
| Album           | nvarchar(MAX)   |
| Popularity      | int             |
| Liked           | bit             |
| Duration        | time(7)         |
| Spotify_URI     | nvarchar(MAX)   |
| Spotify_URL     | nvarchar(MAX)   |
| Track           | nvarchar(MAX)   |


Secondly, I decided to check for null values on the Artist & Milliseconds field as these fields are both central to my analysis.

<br/>Query:
```sql
SELECT Artist, COUNT(*) as Null_Count
FROM Spotify
GROUP BY Artist
HAVING Artist IS NULL
```
<br/>Output:
**Artist**|**Null\_Count**
:-----:|:-----:
NULL|424

<br/>Query:
```sql
SELECT Milliseconds, COUNT(*) as Null_Count
FROM Spotify
GROUP BY Milliseconds
HAVING Milliseconds IS NULL
```

<br/>Output:
**Milliseconds**|**Null\_Count**
:-----:|:-----:

<br/>Query:
```sql
SELECT Artist, COUNT(*) as Null_Count
FROM SpotifyLikedSongs
GROUP BY Artist
HAVING Artist IS NULL
```

<br/>Output:
**Artist**|**Null\_Count**
:-----:|:-----:


<br/> <br/>It is clear to see that there are null values within the Artists field in the Spotify table but no null vaues within the SpotifyLikedSongs table.
<br/><br/>After taking a deeper look at the Spotify table, the reason for the null values present within the the Spotify table is due to the fact my dataset includes Podcast data hence causing the nulls within the Artist field. 
<br/><br/> I can check this theory through reconciling the number of nulls with the number of rows where the episode_name is not null.

<br/>Query:
```sql
SELECT Count(*) As Null_Podcasts
FROM Spotify
WHERE Episode_Name IS NOT NULL 
```
<br/>Output:
**Null\_Podcasts**
:-----:
424

<br/>Subsequently, the nulls are removed from the dataset.

<br/>Query:
```sql
DELETE FROM Spotify
WHERE Artist is NULL
```

<br/>Output:
<br/>*(424 rows affected)
<br/>Completion time: 2025-01-28T16:07:28.0616267+00:00*

<br/><br/>Additionally we can remove the columns not useful for our analysis.
```sql
ALTER TABLE Spotify 
DROP COLUMN Episode_Name, Episode_Show_Name, Spotify_Eposide_Uri, Audiobook_Title, Audiobook_ID, Audiobook_Chapter_ID,Audiobook_Chapter_Title;
```

```sql
ALTER TABLE SpotifyLikedSongs
DROP COLUMN Popularity, Liked;
```

<br/> I then deicde to check to see if there is any duplicate rows. 
<br/><br/>Before I begin querying the data I have another inspection of the data and realise that a duplicate check may not be appropriate here due to a few reasons:<br/>
- The spotify_track_uri holds the unique track ID's however due to the fact tracks can be listened multiple times, this alone would not be appropriate.
- A combination of spotify_track_uri & a timestamp for example cannot be used due to the fact the timestamps are not unique. This is because of Spotify's offline mode which allows music to be listened to when a device is not connected to the internet. Music listened through this method are still documented however, the record of the user listening to these offline songs receive the timestamp of when device has re-connected to the internet hence creating the possibility of multiple duplicate timestamps.

## Spotify's Methodology
Before diving into my analysis, I think it's important to understand how Spotify calculates its Wrapped. While the exact methodology isn't publicly available, I've gathered the following insights from online sources.
<br/><br/>Spotify Wrapped's methodology used to calculate the top 5 artists is based on the following:
-   Top Artists are determined by the number of streams an artist receives within the date period.
-	The date period typically includes January to an unspecified date within November.
-	For a song to count as a stream it must be listened to for at least 30 seconds.
-	Songs listened to via incognito mode do not count.
-	Songs listened to via offline mode are counted.

<br/>In my opinion it can be seen that there are some limitations of Spotify’s methodology.
<br/><br/>To begin with Spotify Wrapped does not include data from the whole year. 
<br/><br/>Typically, the month of December is missed out which can cause an inaccurate view of the data; for example artists who primarily make Christmas related music are very unlikely to be within a Spotify Wrapped due to the fact the month in which their music has the highest streams are not included in Spotify Wrapped.
<br/><br/>Additionally, top artists are determined by how many times they have been played rather than the amount of time they have been listened to over the period. I believe that there are a number of limitations with this method of determing the top artists which can be seen below.
- Genres with naturally shorter track lengths such as pop would have an advantage over those with longer formats such as classical.
- Based on knowing my listening habits, its not uncommon for me to only listen to a song for a short about of time and then press next hence my short term listening habits will skew my top artists

I believe my top artist should be the artist that I have spent the most time listening to rather than the artist who I have played the most often as listening to an artist for a longer amount of time shows a deeper level of engagement versus.

## Analysis
In order to address the problem statement, I will use the below metholodology to analyse whether Drake truly deserves a spot in my top 5 artists across the last 5 years:
- Date period will be across a full year
- Top Artists are determined by how long they have been played within the year
- Songs listened to via incognito mode do not count
- Songs listened to via offline mode are counted
Subsequently, I will compare the results from Spotify's methodology top 5 artists versus the top 5 artists from my above methodology

### 2024
I will use the below query to extract the top 5 artists based on my methodology:
```sql
SELECT TOP 5 Artist, Year, SUM(Milliseconds) / 60000 as Minutes_Listened
FROM Spotify
WHERE Year = 2024 and (Incognito_Mode = 0)
GROUP BY Artist, Year
ORDER BY SUM(Milliseconds) DESC;
```
<br/>

Output:
| Artist      | Year | Minutes Listened |
|-------------|------|------------------|
| Drake       | 2024 | 1651             |
| Bashy       | 2024 | 1506             |
| K-Trap      | 2024 | 1455             |
| Headie One  | 2024 | 1281             |
| Ed Sheeran  | 2024 | 1110             |

Now let's compare this to Spotify Wrapped's results:

| Position | Spotify Methodology Artist | My Methodology Artist | Year |
|----------|-----------------------------|-----------------------|------|
| 1        | K-Trap                      | Drake                 | 2024 |
| 2        | Headie One                  | Bashy                 | 2024 |
| 3        | Drake                       | K-Trap                | 2024 |
| 4        | Bashy                       | Headie One            | 2024 |
| 5        | Future                      | Ed Sheeran            | 2024 |

My methodology shows Drake as my #1 artist based on how many minutes i've listened to him.
2024 is particularly an interesting year as a lot of my Drake plays are concentrated around a few songs.

```sql
SELECT top 5 Artist, Track, Count(Track) as Plays, Cast(Count(Track)*1.0/(SELECT COUNT(*)*1.0 
                          FROM Spotify 
                          WHERE Artist = 'Drake' 
                          AND Year = '2024' 
                          AND Milliseconds > 30000)*100 AS Decimal(5,2)) AS Proportion_Percentage
FROM Spotify
WHERE Artist ='Drake' and year = '2024' and Milliseconds > 30000 and Incoginito_Mode = 0
GROUP BY Artist, Track
Order By Count(Track) desc
```
Output:

| Artist | Track                                      | Plays | Proportion Percentage |
|--------|---------------------------------------------|-------|-----------------------|
| Drake  | Family Matters                             | 42    | 9.40                  |
| Drake  | Push Ups                                    | 24    | 5.37                  |
| Drake  | Diplomatic Immunity                         | 13    | 2.91                  |
| Drake  | Back To Back                                | 11    | 2.46                  |
| Drake  | First Person Shooter (feat. J. Cole)        | 10    | 2.24                  |


The two songs that relate to the conflict were Family Matters and Push Ups, the above table demonstrates how much the two songs relatively dominated my Drake listening habits. Additionally, if we look at the timestamps we can see that there is a spike in my Drake listening habits in the month of May which was the peak of the conflict.

```sql
SELECT 
    DATEPART(MONTH, Timestamp) AS Month, 
    SUM(Milliseconds) / 60000 AS Minutes_Listened
FROM Spotify
WHERE YEAR in (2020, 2021, 2022, 2023, 2024) and Artist = 'Drake'
GROUP BY DATEPART(MONTH, Timestamp)
ORDER BY Month;
```

Output visualised:


![image alt](https://github.com/ebenagati/Portfolio/blob/main/Wrapped%20in%20Mystery%3A%20Why%20Drake%20Always%20Makes%20the%20Cut/2024%20Drake%20Minutes%20Listened.PNG)

Let's repeat the methodology comparison analysis for the other years.

### 2023 
| Position | Spotify Methodology Artist | My Methodology Artist | Year |
|----------|-----------------------------|-----------------------|------|
| 1        | Ed Sheeran                  | Ed Sheeran            | 2023 |
| 2        | Headie One                  | Drake                 | 2023 |
| 3        | Drake                       | Headie One            | 2023 |
| 4        | Rod Wave                    | Jason Mraz            | 2023 |
| 5        | J Hus                       | Rod Wave              | 2023 |

### 2022
Due to the fact Spotify do not allow you to see your Wrapped from previous years, 2022 is the only year I do not have Spotify Wrapped data for hence, based on Spotify's methodology, I will run a query to emulate their results.

```sql
SELECT TOP 5 Artist, Year, COUNT(*)
FROM Spotify
WHERE Timestamp BETWEEN '2022-01-01T00:00:00Z' AND '2022-11-30T23:59:59Z' and (Milliseconds >= 30000) and (Incognito_Mode = 0)
GROUP BY Artist, Year
ORDER BY COUNT(*) DESC;
```


| Position | Spotify Methodology Artist | My Methodology Artist | Year |
|----------|-----------------------------|-----------------------|------|
| 1        | Blade Brown                 | PJ Morton             | 2022 |
| 2        | PJ Morton                    | Adele                 | 2022 |
| 3        | K-Trap                       | Blade Brown           | 2022 |
| 4        | Adele                        | K-Trap                | 2022 |
| 5        | Jason Mraz                   | Jason Mraz            | 2022 |


### 2021

| Position | Spotify Methodology Artist | My Methodology Artist | Year |
|----------|-----------------------------|-----------------------|------|
| 1        | Kanye West                  | Jason Mraz            | 2021 |
| 2        | Drake                       | Kanye West            | 2021 |
| 3        | Headie One                  | Adele                 | 2021 |
| 4        | Jason Mraz                  | Drake                 | 2021 |
| 5        | Pop Smoke                   | Owl City              | 2021 |


### 2020
| Position | Spotify Methodology Artist | My Methodology Artist | Year |
|----------|-----------------------------|-----------------------|------|
| 1        | Headie One                  | Jhené Aiko            | 2020 |
| 2        | Jhené Aiko                   | Headie One            | 2020 |
| 3        | Drake                       | Ms. Lauryn Hill       | 2020 |
| 4        | Snoh Aalegra                | Drake                 | 2020 |
| 5        | PJ Morton                    | PJ Morton             | 2020 |

Across the years, Drake has a strong presence irregardless of the methodology except within 2022. However this was quite a surface level analysis. Let's take this analysis a step further and understand the reasons as to why Drake was played.
<br/><br/>
Within our dataset we have the column Start_Reason which provides interesting insights as to why a song was played. Below is the list of reasons.

| Reason      | Explanation                                                                                         |
|-------------|-----------------------------------------------------------------------------------------------------|
| fwdbtn      | User manually skips to the next track by pressing the Next button on their device.                  |
| trackdone   | The previous track has finished playing naturally and the next track starts automatically.          |
| clickrow    | User manually selected a track by clicking on it from a playlist, album, or search results.         |
| backbtn     | User pressed the Previous button to go back to the current track's start or to the previous track.  |
| trackerror  | Occurs when an error occurs while trying to play the track, and the system attempts to play the next available track. |
| playbtn     | User presses the Play button to start playback of a song, typically from a paused state.            |
| remote      | A playback that was started using a connected remote device, such as Spotify Connect, a smart speaker, or another linked device. |
| appload     | Spotify starts playing a track automatically upon opening the app, such as resuming playback from a previous session. |

Now let's breakdown Drake plays across the last 5 years:

```sql
Select Start_Reason, Count(Start_Reason) as Count, CAST(Count(Start_Reason)*1.0/(SELECT COUNT(Start_Reason)*1.0 
                          FROM Spotify 
                          WHERE Artist = 'Drake'  AND Year in (2020,2021,2022,2023,2024)  AND Milliseconds > 30000)*100 AS Decimal(5,2)) AS Proportion_Percentage
From Spotify
Where Artist = 'Drake' and Year in (2020,2021,2022,2023,2024) AND Milliseconds > 30000
Group by Start_Reason
Order by Count(Start_Reason) desc
```

Output:
| Start_Reason | Count | Proportion_Percentage |
|--------------|-------|-----------------------|
| trackdone    | 1766  | 60.65                 |
| fwdbtn       | 580   | 19.92                 |
| clickrow     | 217   | 7.45                  |
| backbtn      | 146   | 5.01                  |
| playbtn      | 106   | 3.64                  |
| remote       | 40    | 1.37                  |
| trackerror   | 30    | 1.03                  |
| appload      | 27    | 0.93                  |

A significant portion of my Drake listens appears to be passive rather than intentional. This is largely due to Spotify's autoplay feature, which automatically plays the next song after one finishes. In these cases, I'm not actively choosing to listen to Drake—I'm simply letting the music continue playing. This is most likely due to the large amount of Drake in my liked songs (my most common method of listening), thus it makes sense that a large proportion of my plays from a Drake are from sceniaros where a Drake song is simply the next in line due to his relatively large presence in my liked songs.

```sql
Select TOP 10 Artist, Cast(count(Track)*1.0/(SELECT COUNT(*)*1.0 
                          FROM SpotifyLikedSongs)*100
                          AS DECIMAL (5,2)) as ProporitonPercentage
From SpotifyLikedSongs
Group By Artist
Order by Count(*) Desc
```

Output:
| Artist      | Proportion Percentage |
|-------------|------------------------|
| Drake       | 2.13%                  |
| Headie One  | 1.45%                  |
| Nines       | 1.28%                  |
| Kanye West  | 1.26%                  |
| K-Trap      | 1.06%                  |
| Carns Hill  | 1.06%                  |
| Skepta      | 1.06%                  |
| Meek Mill   | 1.06%                  |
| Ed Sheeran  | 1.05%                  |
| P Money     | 0.98%                  |

Let's now take a look at my top 5 artists across the years through the lens of intentional listening.

```sql
SELECT TOP 5 Artist,Start_Reason, SUM(Milliseconds) / 60000 as Minutes_Listened
FROM SPOTIFY
WHERE Year IN (2020,2021,2022,2023,2024) and (Start_Reason = 'clickrow') and (Incognito_Mode = 0)
GROUP BY Artist,Start_Reason
ORDER BY SUM(Milliseconds) desc
```

Output:
| Artist       | Start Reason | Minutes Listened |
|--------------|--------------|------------------|
| Drake        | clickrow     | 884              |
| Headie One   | clickrow     | 801              |
| Jason Mraz   | clickrow     | 729              |
| K-Trap       | clickrow     | 728              |
| Kanye West   | clickrow     | 681              |


Filtering for only intentional plays showcases that Drake is still my top artist.
## Limitations
A limitation of my analysis is the songs in which Drake is featured on are not included within my analysis. Currently within my dataset it is difficult to include songs in which an artist has been featured on due to the lack of standardisation across how featured artists are presented within each row of data. For example the song 'Sicko Mode' has Drake featured on, however this is not shown on the track name or artist, alternatively, the song 'Used to This (feat. Drake)' clearly indicates that Drake is a feature here. Another example is 'Fountains (with Tems)' further shows this lack of standardisation as 'Feat' is not used, instead 'With' is used. Therefore the lack of clear rules for how features are indicated has caused some inaccuracy in our analysis however I do not believe this inaccuracy will change my conclusions.
<br/><br/>
Another limitation is not knowing Spotify's exact methodology when determining top artists. In the year 2022 where I approximated my Wrapped results based on Spotify's unconfirmed methodology, my analysis may be slightly results for that year may be slightly incorrect due to not following Spotify's exact methodology. In addition to this, if I knew Spotify's exact methodology, this would have allowed me to create a more robust counter metholodology when devising my methodology.
## Extension
Through undertaking this project, I had a few thoughts of some other interesting questions I'd like answered regarding my Spotify Data.

**My Spotify Wrapped of all time**

**Top 5 Artists**
<br/>A query to find my top 5 artists of all time using the aforementioned Spotify methodology.

```sql
SELECT TOP 5 Artist, COUNT(*) as Plays
FROM Spotify
WHERE (Milliseconds >= 30000) and (Incognito_Mode = 0)
GROUP BY Artist
ORDER BY COUNT(*) DESC;
```

Output:

| Artist       | Plays |
|--------------|-------|
| Drake        | 5717  |
| Headie One   | 4773  |
| Wretch 32    | 4446  |
| K-Trap       | 4130  |
| Kanye West   | 3979  |


**Top Songs**
<br/>A query to find my top 5 songs of all time using the aforementioned Spotify methodology.

```sql
SELECT TOP 5 Artist, Track, COUNT(*) as Plays
FROM Spotify
WHERE (Milliseconds >= 30000) and (Incognito_Mode = 0)
GROUP BY Artist, Track
ORDER BY COUNT(*) DESC;
```

Output:
| Artist       | Track                                           | Plays |
|--------------|-------------------------------------------------|-------|
| PJ Morton    | BUILT FOR LOVE (feat. Jazmine Sullivan)         | 261   |
| Blade Brown   | Intro                                           | 232   |
| Jamie Foxx    | Family                                          | 212   |
| Wretch 32     | Open Conversation & Mark Duggan                | 205   |
| Wretch 32     | Something                                       | 205   |

**Listening Time**
<br/>A query to determine how much time i've spent listening to songs on Spotify.


```sql
SELECT 
    CAST(SUM(CAST(milliseconds AS BIGINT)) / 60000.0 AS DECIMAL(10,2)) AS Minutes_Listened,
    CAST(SUM(CAST(milliseconds AS BIGINT)) / 3600000.0 AS DECIMAL(10,2)) AS Hours_Listened,
    CAST(SUM(CAST(milliseconds AS BIGINT)) / 86400000.0 AS DECIMAL(10,2)) AS Days_Listened
FROM 
    Spotify
```
Output:
 | Minutes_Listened | Hours_Listened | Days_Listened |
|-----------------|---------------|--------------|
| 813,460.83      | 13,557.68      | 564.90       |

**How cyclical is my listening habits?**
<br/>
Do I listen to more music during the summer months or do I listen to more during the winter?
<br/>
A sql query to output total minutes listened to within each month of each year.
```sql
SELECT 
    Year, 
    CASE DATEPART(MONTH, Timestamp)
        WHEN 1 THEN 'January'
        WHEN 2 THEN 'February'
        WHEN 3 THEN 'March'
        WHEN 4 THEN 'April'
        WHEN 5 THEN 'May'
        WHEN 6 THEN 'June'
        WHEN 7 THEN 'July'
        WHEN 8 THEN 'August'
        WHEN 9 THEN 'September'
        WHEN 10 THEN 'October'
        WHEN 11 THEN 'November'
        WHEN 12 THEN 'December'
    END AS Month,
    SUM(Milliseconds) / 60000 AS Minutes_Listened
FROM Spotify
GROUP BY Year, DATEPART(MONTH, Timestamp)
ORDER BY Year, DATEPART(MONTH, Timestamp);
```
Subsequently, I switch to python in order to create a visualisation:
<br/><br/>
Importing libraries:
```jyup
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
```

Creation of a dictionary: 
```jyup
data = {'2016': [155.0, 5314.0, 2712.0, 3043.0, 4587.0, 0, 0, 6815.0, 5947.0, 7009.0, 6932.0, 6454.0],
    '2017': [6114, 7383, 9766, 6634, 6878, 7917, 9326, 6815, 5947, 7009, 6932, 6454],
    '2018': [5662, 5548, 6337, 6327, 5586, 7313, 7098, 6488, 6048, 7548, 6526, 8103],
    '2019': [7940, 5514, 7569, 6265, 7217, 7444, 10609, 11135, 9196, 11232, 15971, 8413],
    '2020': [10162, 10353, 12437, 8783, 8859, 9204, 12194, 13099, 10602, 13326, 8623, 9355],
    '2021': [12784, 12496, 9289, 12342, 11191, 8854, 9143, 11237, 12929, 13319, 11026, 10149],
    '2022': [14461, 9675, 12356, 9377, 7328, 11017, 7932, 5117, 4917, 9239, 10576, 6721],
    '2023': [9834, 7762, 5563, 5897, 6911, 7210, 7944, 7770, 4969, 6743, 7480, 4824],
    '2024': [6699, 4440, 5078, 5936, 6158, 5389, 5433, 5927, 7603, 5476, 4692, 4062]}

```

Creation of the dataframe:
```jyup
months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
df = pd.DataFrame(data, index=months)
```

Heatmap creation:
```jyup
plt.figure(figsize=(10, 8))
sns.heatmap(df, annot=True, cmap='YlOrRd', fmt=',.0f', linewidths=0.5)
plt.title('Heatmap of Monthly Listens')
plt.show()
```
<br/>Output:

![image alt](https://github.com/ebenagati/Portfolio/blob/main/Wrapped%20in%20Mystery%3A%20Why%20Drake%20Always%20Makes%20the%20Cut/Spotify%20Minutes%20Heatmap.png)


The data demonstrates a recurring seasonal pattern with an increase in minutes listened towards the middle of the year (May to August) which suggests during the summer months I listen to more music.<br/><br/>
Conversely, towards the end of year the data shows a decrease in minutes listened to. This can be explained due to the fact that my holidays usually conincide with the summer time hence I have more freetime in the summer to listen to music. Furthermore, typcially there are more releases in the summer which contribute to the increase in my listening.<br/><br/>
This particular heatmap presents an opportunity for further analysis around why specific months are so high (e.g. Novemeber 2019) which may provide some interesting insights.

## Conclusion
This project has delved into the patterns behind my Spotify Wrapped and aims to determine whether Drake truly deserves a spot in my top 5 artists every year, despite not consciously listening to him often. Through examining my listening data across multiple years, it became clear that Drake I have underestimated the amount of Drake I listen to. 

The findings showed that while Drake consistently appeared in my top artists across multiple years, the ranking varied depending on the methodology used. To be more specific, typically when considering the total minutes listened, Drake often ranked higher. Additionally, this analysis demonstrated the influence of passive listening via autoplay, which contributed significantly to Drake's presence in my top artists. The intentional listening analysis revealed that even when filtered for intentional plays, Drake remained a dominant artist. This reinforces the idea that Drake is a deeply ingrained within my listening habits.

However, this analysis has faced some limitations, particularly the inconsistency in how featured artists are tracked and the uncertainty around Spotify’s exact methodology for Wrapped. These factors may have caused minor inaccuracies in my analysis.

In conclusion, while Drake’s consistent top placement in my Wrapped results is undoubtedly influenced by autoplay and passive listening, he still holds a strong position within my listening habits.

