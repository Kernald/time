Arduino Time Library
====================

Time is a library that provides timekeeping functionality for Arduino.

The code is derived from the Playground DateTime library but is updated
to provide an API that is more flexable and easier to use.

A primary goal was to enable date and time functionality that can be used with
a variety of external time sources with minimum differences required in sketch logic.

Usage
-----

### hour()

The hour now  (0-23)

### minute()

The minute now (0-59)

### second()

The second now (0-59)

### day()

The day now (1-31)

### weekday()

Day of the week, Sunday is day 0

### month()

The month now (1-12)

### year()

The full four digit year: (2012, 2013 etc)


There are also functions to return the hour in 12 hour format:

### hourFormat12()

The hour now in 12 hour format

### isAM()

Returns true if time now is AM

### isPM()

Returns true if time now is PM

### now()

Returns the current time as seconds since Jan 1 1970


The time and date functions can take an optional parameter for the time. This prevents
errors if the time rolls over between elements. For example, if a new minute begins
between getting the minute and second, the values will be inconsistent. Using the
following functions eliminates this problem
```
time_t t = now(); // store the current time in time variable t
hour(t);          // returns the hour for the given time t
minute(t);        // returns the minute for the given time t
second(t);        // returns the second for the given time t
day(t);           // the day for the given time t
weekday(t);       // day of the week for the given time t
month(t);         // the month for the given time t
year(t);          // the year for the given time t
```
  

Functions for managing the timer services are:

### setTime(t)

Set the system time to the give time t

### setTime(hr, min, sec, day, mnth, yr)

Alternative to above, yr is 2 or 4 digit yr (2010 or 10 sets year to 2010)

### adjustTime(adjustment)

Adjust system time by adding the adjustment value

### timeStatus()

Indicates if time has been set and recently synchronized. Returns one of the following enumerations:
- timeNotSet: The time has never been set, the clock started at Jan 1 1970
- timeNeedsSync: The time had been set but a sync attempt did not succeed
- timeSet: The time is set and is synced
Time and Date values are not valid if the status is timeNotSet. Otherwise values can be used but 
the returned time may have drifted if the status is timeNeedsSync. 	

### setSyncProvider(getTimeFunction)

Set the external time provider

### setSyncInterval(interval)

Set the number of seconds between re-sync


There are many convenience macros in the Time.h file for time constants and conversion of time units.

Differences between this code and the playground DateTime library
although the Time library is based on the DateTime codebase, the API has changed.
Changes in the Time library API:
- Time elements are functions returning int (they are variables in DateTime)
- Years start from 1970 
- Days of the week and months start from 1 (they start from 0 in DateTime)
- DateStrings do not require a seperate library
- Time elements can be accessed non-atomically (in DateTime they are always atomic)
- Function added to automatically sync time with extrnal source
- LocalTime and maketime parameters changed, localTime renamed to breakTime
 
Technical notes
---------------

Internal system time is based on the standard Unix time_t.
The value is the number of seconds since Jan 1 1970.
System time begins at zero when the sketch starts.
  
The internal time can be automatically synchronized at regular intervals to an external time source.
This is enabled by calling the setSyncProvider(provider) function - the provider argument is
the address of a function that returns the current time as a time_t.

The default interval for re-syncing the time is 5 minutes but can be changed by calling the 
setSyncInterval(interval) method to set the number of seconds between re-sync attempts.

The Time library defines a structure for holding time elements that is a compact version of the  C tm structure.
All the members of the Arduino tm structure are bytes and the year is offset from 1970.
Convenience macros provide conversion to and from the Arduino format.

Low level functions to convert between system time and individual time elements are provided:                    
  breakTime( time, &tm);  // break time_t into elements stored in tm struct
  makeTime( &tm);  // return time_t  from elements stored in tm struct 

Initial code by Michael Margolis.
