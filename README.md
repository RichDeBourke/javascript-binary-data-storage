Store / Retrieve Binary Data in localStorage
========
## JavaScript plugin that provides:

  * Storage of binary data (an array of true/false) in the browser's localStorage
    * The data is stored as an array of integers with the integers being processed as binary data.
    * Storing integers rather than a series of true/false reduces the overall storage requirement. One integer (max value of -1073741824) can represent 32 binary values.
        * The difference between storing an array of 520 "t" or "f" values and storing 17 integers isn't that great (~3,120 vs. 408 bytes), but it's good to save memory space when possible.
  * Works in conjunction with the [Scrollable jQuery calendar / week selector plugin](http://github.com) for identifying weeks that have been previously selected
    * The calendar uses the localStorage plugin for tracking weeks that were previously access.

## Usage
Include the plugin and the JavaScript code to call the plugin:

    <script type="text/javascript" src="WeekHistory.js"></script>
    
    <script type="text/javascript">
        $(document).ready(function() {
            "use strict";
            var weeklyHistoryArray = window.getWeeklyHistory("calendarHistory", 522);
            
            $("#update-button").click(function () {
                window.updateWeeklyHistory("2012-01-01", "2016-02-07");
            }
            
            $("#erase-button").click(function () {
                window.clearWeeklyHistory(true);
            };
    </script>

#### getWeeklyHistory
Initiates the plugin and returns an array of true/false values

`getWeeklyHistory` is called with the storage name (the key) and optionally the minimum number of storage positions that are needed (default is 544). If an array was previously saved, the function will read in the existing data, covert the data to an array of true/false values, and return the array. If there is no stored value, the function will create an array of all false values.
  * When an array is created, it is not initially saved to localStorage. Saving only happens in the `updateWeeklyHistory` function.
  * The array size is always a multiple of 32, so the array may have more values than needed.
  * If the browser does not support localStorage (e.g. iOS does not support localStorage in private mode), `getWeeklyHistory` will return an array of all false values (there is no indication that the history could not be read localStorage - just all false values).

#### updateWeeklyHistory
Stores/updates the data in localStorage - returns `true` if there was no problem storing the array

`updateWeeklyHistory` is called with two date string values formatted as YYYY-MM-DD. The first value is the base date for the calendar (the starting week), and the second value is the week to be set to true. The function will set the bit for the appropriate data block and then save the new set of values to localStorage
  * localStorage will throw an exception if storage is full (unlikely, but possible if you're using localStorage for other things) or when running in private mode with iOS (iOS throws an error when a page attempts to save to localStorage in private mode).
  * The return value is `false` if an exception is thrown.
  * There is no validation of the date values.

#### clearWeeklyHistory
The localStorage key-value object is removed
`clearWeeklyHistory` can be called with one argument. If the passed value is a Boolean `true`, the plugin will request confirmation that the history should be removed.
  * Once removed, the history cannot be recovered.

## Demo
[Full function demo](http://google.com) - Calendar with week selection/indication capability and store / retrieve in operation.

[In operation](http://google.com) - Calendar with store / retrieve in operation.

## How the plugin works
#### Initiation / process values
The history data is managed internal to the plugin as an array of integer values. `JSON.stringify` and `JSON.parse` functions are used to handle the conversion to string and back again for storing the information in the browser's localStorage (information saved in localStorage is saved as a string). `JSON.parse` returns an array of integers.

While JavaScript processes numbers as double precision floating-point values, the JavaScript bitwise operators treat values as a sequence of 32 bits. The plugin uses this characteristic of JavaScript to treat one integer value as a set of 32 flags. Each of the 32 bits is used to represent one week.

The plugin initializes the integer array to an array of 17 zero value integers by default to cover ten years (17 * 32 = 544). The size can be initiated to a different size by calling the plugin with a different value:

    var weeklyHistoryArray = window.getWeeklyHistory("calendarHistory", 261);

Initiating the plugin with a value of 261 would create 9 storage blocks, capable of storing 288 values.

**Caution:** The plugin does not support changes to the storage requirement. The storage requirement should be initiated the first time to the maximum possibly required value.

#### Storing data
For storing values, the plugin is passed two values, the base date for the array and the week to be set as `true`. The plugin calculates the difference between the two dates, identifies which integer in the array should be updated, and then does a bitwise `and` to switch on the desired bit.

#### Erasing the history
When erasing the history, if the plugin is passed a `true` value, the plugin will request confirmation before clearing localStorage. If the passed value is any value other than a value that evaluates to `true`, or there is no passed value, the plugin will clear localStorage without asking for confirmation.

## Compatibility
The calendar plugin has been confirmed to work with the latest versions of:

* IE 9 & 11
* Edge (desktop & Surface)
* Chrome (mobile & desktop)
* Firefox
* Android Internet
* Safari (mobile & desktop)

## License
This plugin is provided under the [MIT license](http://opensource.org/licenses/mit-license.php).
