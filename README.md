# SPOTIFY-LISTENING-ANALYSIS
This contains a Personal Spotify Listening Analysis based on my history which was provided by Spotify. The datset provided was based on my listening history on spotify since i began using it from 2021 to 2024.

## Table of Content##
1.0 - Introduction

2.0 - Data Source

3.0 - Tools Used

4.0 - Data Transformation

5.0 - Data visualization

6.0 - Insights Gotten

## 1.0 - Introduction
This is a Personal Spotify listening analysis that was made based on my spotify listening hsitory which i got from requesting my data. This analysis covered the listening history from 2021 - 2024. It can be seen as more of like a customize wrapped version of spotify but this one was done on a yearly basis. 

## 2.0 - Data Source
The data source was gotten from Spotify itself when i requested for my listening history from the offical website [Spotify](https://open.spotify.com/) The data given was in CSV format. 

## 3.0 - Tools Used
3.1 - Microsoft Excel [Download Here](www.microsoft.com)

3.2 - Microsoft PowerBI [Download Here](https;//app.powerbi.com/)

## 4.0 - Data Transformation
Using Microsoft Excel, i was able to convert some of the column formatting to help suit my analysis. 

Using Microsoft PowerBI, i was able to use Data Analysis Expressions such as DAX to curate some of the necessary insights whcih was needed for my analysis. 

Some of the DAX can be seen below. 

```
/* This is for some of the date table measures or expressions. */

Date Table = CALENDAR(MIN('Spotify Data'[Track Played Date]),MAX('Spotify Data'[Track Played Date]))
Day Name = FORMAT('Date Table'[Date],"DDD")
Month Name = FORMAT('Date Table'[Date],"MMM")
Weekday or Weekend = IF(WEEKDAY('Date Table'[Date],2)<=5,"Weekday","Weekend")
Month Number = MONTH('Date Table'[Date])

/* This is for the Quadrant measures which was used in the scatter graph. */

CF Quadrant = 
VAR AVGTime = ([Average Listening Time])<= 'Listening Time '[Listening Time  Value]
VAR TrackFreq = ([Track Frequency])>= 'Track Frequency Parameter'[Track Frequency Parameter Value]

VAR RESULT =
SWITCH(
    TRUE(),
    AVGTime && TrackFreq, 1, -- Low Time & High Freq
    NOT AVGTime && TrackFreq, 2, -- High Time & High Freq
    NOT AVGTime && NOT TrackFreq, 3, -- High Time & Low Freq
    AVGTime && NOT TrackFreq, 4 -- Low Time & Low Freq 
)
RETURN RESULT

/* This measure is for the HH:MM:SS of Listening time which was customized. */

HH:MM:SS of listening time = 
VAR TotalMS = SUM('Spotify Data'[Ms_played])
VAR TotalSeconds = INT(TotalMS/ 1000)
VAR Hours = INT(TotalSeconds/3600)
VAR Minutes = INT(MOD(TotalSeconds,3600)/60)
VAR Seconds = MOD(TotalSeconds,60)

RETURN
FORMAT(Hours,"00")&" Hr " & 
FORMAT(Minutes,"00")&" Min " & 
FORMAT(Seconds,"00")&" Sec "

/* This measure was used for the latest year which was also similar for latest albums. */

Latest Year Album = 

 VAR _latestyear = MAX('Date Table'[Year])

 RETURN
 CALCULATE(DISTINCTCOUNT('Spotify Data'[Album_name]), 'Date Table'[Year] = _latestyear)

/* This measure was used for the Previous Year which was also similar for Previous Album and Previous Track. */

Previous Year Album = 

VAR _latestyear = MAX('Date Table'[Year])
VAR _previousyear = _latestyear - 1 
RETURN
CALCULATE(DISTINCTCOUNT('Spotify Data'[Album_name]),'Date Table'[Year] = _previousyear)

/* This measure was used for the PY and YOY KPI on Albums, Tracks and Artists. */

PY and YOY Album KPI = 

VAR _latest = ([Latest Year Album])
VAR _previous = ([Previous Year Album])
VAR _YOY = IF(NOT(ISBLANK(_previous)), DIVIDE(_latest - _previous, _previous,0),BLANK())
RETURN 
IF(NOT(ISBLANK(_previous)), "vs PY: " & FORMAT(_previous, "#,##0") & " (" & FORMAT(_YOY, "0.00%") & ")", "No Data")

/* This measure was used for the track frequency which was necessary. */

Track Frequency = COUNTROWS('Spotify Data')

/* There are more measures that was used in the dashboard report. */

```
## 5.0 Data Visualization

## 6.0 Insights Gotten
