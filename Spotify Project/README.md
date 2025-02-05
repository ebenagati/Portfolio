# Wrapped in Mystery: Why Drake Always Makes the Cut

## Problem Statement 
There's a few things that are guaranteed in life, death, taxes and Drake being within my spotify wrapped. <br/><br/>
Spotify Wrapped is a yearly summary of listening habits that Spotify provide to it's users. This wrap up shows a user's Top 5 Artists, Top 5 Genres, Top 5 Songs and the amount of minutes of Spotify listened to across the year.<br/>
Over the years i've noticed a pattern of Drake appearing in my Top 5 Artists every year despite me not choosing to listen to Drake very often within certain years.<br/><br/>
This project attempts to investigate my listening habits and to discover whether Drake truly deserves to be within my top 5 artists each year.

## Dataset Overview
The dataset used for this project was collected from Spotify via requesting my Extended streaming history. The key columns can be found below.:

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

An additional dataset which has been retrieved from https://skiley.net will also be used in order to facilitate analysis upon my Liked Songs.



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
| Offline_Timestamp         | nvarchar(MAX)  |
| Incognito_Mode            | bit            |

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

## Spotify's Methodology
Let’s take a look at Spotifys methodology.
<br/>Spotify Wrapped's methodology used to calculate the top 5 artists is based on the following:
-   Top Artists are determined by the number of streams an artist receives within the date period.
-	Date period includes January to an unspecified date within November.
-	For a song to count as a stream it must be listened to for at least 30 seconds.
-	Songs listened to via incognito mode do not count.
-	Songs listened to via offline mode are counted.

<br/>In my opinion it can be seen that there are some limitations of Spotify’s methodology.
<br/><br/>To begin with Spotify Wrapped does not include data from the whole year which is probably to allow their data team enough time to process the data for their users. Typically, the month of December is missed out which can cause an inaccurate view of the data for example artists who primarily make Christmas related music are very unlikely to be within a Spotify Wrapped due to the fact the month in which their music has the highest streams are not included in Spotify Wrapped.
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
![image alt](https://github.com/ebenagati/Portfolio/blob/main/Spotify%20Project/2024%20Drake%20Minutes%20Listened.PNG?raw=true)

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

It is clear to see a large proportion of my listens from Drake are passive listens. To explain further Spotify has an autoplay feature such that when a song has finished, the next song is played. Due to the large amount of Drake in my liked songs, it makes sense that a large proportion of my plays from a Drake are from sceniaros where a Drake song is simply the next in line due to his relatively large presence in my liked songs
