# Date.parse()

The `Date.parse()` method parses a string representation of a date, and returns the number of milliseconds since January 1, 1970, 00:00:00 UTC or `NaN` if the string is unrecognized or, in some cases, contains illegal date values (e.g. 2015-02-31).

It is not recommended to use `Date.parse` as until ES5, parsing of strings was entirely implementation dependent. There are still many differences in how different hosts parse date strings, therefore date strings should be manually parsed (a library can help if many different formats are to be accommodated).

## Syntax

Direct call:

    Date.parse(dateString)

Implicit call:

    new Date(dateString)

### Parameters

`dateString`  
A string representing [a simplification of the ISO 8601 calendar date extended format](#date_time_string_format). (Other formats may be used, but results are implementation-dependent.)

### Return value

A number representing the milliseconds elapsed since January 1, 1970, 00:00:00 UTC and the date obtained by parsing the given string representation of a date. If the argument doesn't represent a valid date, [`NaN`](../nan) is returned.

## Description

The `parse()` method takes a date string (such as "`2011-10-10T14:48:00`") and returns the number of milliseconds since January 1, 1970, 00:00:00 UTC.

This function is useful for setting date values based on string values, for example in conjunction with the [`setTime()`](settime) method and the [`Date`](../date) object.

### Date Time String Format

The standard string representation of a date time string is a simplification of the ISO 8601 calendar date extended format. (See the section [Date Time String Format](https://tc39.github.io/ecma262/#sec-date-time-string-format) in the ECMAScript specification for more details.)

For example, "`2011-10-10`" (_date-only_ form), "`2011-10-10T14:48:00`" (_date-time_ form), or "`2011-10-10T14:48:00.000+09:00`" (_date-time_ form with milliseconds and time zone) can be passed and will be parsed. When the time zone offset is absent, date-only forms are interpreted as a UTC time and date-time forms are interpreted as local time.

While time zone specifiers are used during date string parsing to interpret the argument, the value returned is always the number of milliseconds between January 1, 1970 00:00:00 UTC and the point in time represented by the argument or `NaN`.

Because `parse()` is a static method of [`Date`](../date), it is called as `Date.parse()` rather than as a method of a [`Date`](../date) instance.

### Fall-back to implementation-specific date formats

**Note:** This section contains implementation-specific behavior that can be inconsistent across implementations.

The ECMAScript specification states: If the String does not conform to the standard format the function may fall back to any implementation-specific heuristics or implementation-specific parsing algorithm. Unrecognizable strings or dates containing illegal element values in ISO formatted strings shall cause `Date.parse()` to return [`NaN`](../nan).

However, invalid values in date strings not recognized as simplified ISO format as defined by ECMA-262 may or may not result in [`NaN`](../nan), depending on the browser and values provided, e.g.:

    // Non-ISO string with invalid date values
    new Date('23/25/2014');

will be treated as a local date of 25 November, 2015 in Firefox 30 and an invalid date in Safari 7.

However, if the string is recognized as an ISO format string and it contains invalid values, it will return [`NaN`](../nan) in all browsers compliant with ES5 and later:

    // ISO string with invalid values
    new Date('2014-25-23').toISOString();
    // throws "RangeError: invalid date" in all ES5-compliant browsers

SpiderMonkey's implementation-specific heuristic can be found in [`jsdate.cpp`](https://dxr.mozilla.org/mozilla-central/source/js/src/jsdate.cpp?rev=64553c483cd1#889). The string "`10 06 2014`" is an example of a non-conforming ISO format and thus falls back to a custom routine. See also this [rough outline](https://bugzilla.mozilla.org/show_bug.cgi?id=1023155#c6) on how the parsing works.

    new Date('10 06 2014');

will be treated as a local date of 6 October, 2014, and not 10 June, 2014.

Other examples:

    new Date('foo-bar 2014').toString();
    // returns: "Invalid Date"

    Date.parse('foo-bar 2014');
    // returns: NaN

### Differences in assumed time zone

**Note:** This section contains implementation-specific behavior that can be inconsistent across implementations.

Given a non-standard date string of "`March 7, 2014`", `parse()` assumes a local time zone, but given a simplification of the ISO 8601 calendar date extended format such as "`2014-03-07`", it will assume a time zone of UTC (ES5 and ECMAScript 2015). Therefore [`Date`](../date) objects produced using those strings may represent different moments in time depending on the version of ECMAScript supported unless the system is set with a local time zone of UTC. This means that two date strings that appear equivalent may result in two different values depending on the format of the string that is being converted.

## Examples

### Using `Date.parse()`

The following calls all return `1546300800000`. The first according to ES5 will imply UTC time, and the others are specifying UTC timezone via the ISO date specification (`Z` and `+00:00`)

    Date.parse("2019-01-01")
    Date.parse("2019-01-01T00:00:00.000Z")
    Date.parse("2019-01-01T00:00:00.000+00:00")

The following call, which does not specify a time zone will be set to 2019-01-01 at 00:00:00 in the local timezone of the system.

    Date.parse("2019-01-01T00:00:00")

### Non-standard date strings

**Note:** This section contains implementation-specific behavior that can be inconsistent across implementations.

If `IPOdate` is an existing [`Date`](../date) object, it can be set to August 9, 1995 (local time) as follows:

    IPOdate.setTime(Date.parse('Aug 9, 1995'));

Some other examples of parsing non-standard date strings:

    Date.parse('Aug 9, 1995');

Returns `807937200000` in time zone GMT-0300, and other values in other time zones, since the string does not specify a time zone and is not ISO format, therefore the time zone defaults to local.

    Date.parse('Wed, 09 Aug 1995 00:00:00 GMT');

Returns `807926400000` no matter the local time zone as GMT (UTC) is provided.

    Date.parse('Wed, 09 Aug 1995 00:00:00');

Returns `807937200000` in time zone GMT-0300, and other values in other time zones, since there is no time zone specifier in the argument and it is not ISO format, so is treated as local.

    Date.parse('Thu, 01 Jan 1970 00:00:00 GMT');

Returns `0` no matter the local time zone as a time zone GMT (UTC) is provided.

    Date.parse('Thu, 01 Jan 1970 00:00:00');

Returns `14400000` in time zone GMT-0400, and other values in other time zones, since no time zone is provided and the string is not in ISO format, therefore the local time zone is used.

    Date.parse('Thu, 01 Jan 1970 00:00:00 GMT-0400');

Returns `14400000` no matter the local time zone as a time zone GMT (UTC) is provided.

## Specifications

<table><thead><tr class="header"><th>Specification</th></tr></thead><tbody><tr class="odd"><td><a href="https://tc39.es/ecma262/#sec-date.parse">ECMAScript Language Specification (ECMAScript) 
<br/>

<span class="small">#sec-date.parse</span></a></td></tr></tbody></table>

## Browser compatibility

Desktop

Mobile

Chrome

Edge

Firefox

Internet Explorer

Opera

Safari

WebView Android

Chrome Android

Firefox for Android

Opera Android

Safari on IOS

Samsung Internet

`parse`

1

12

1

3

3

1

1

18

4

10.1

1

1.0

`iso_8601`

6

12

4

9

6

5.1

≤37

18

4

10.1

5.1

1.0

### Compatibility notes

-   Firefox 49 changed the parsing of 2-digit years to be aligned with the Google Chrome browser instead of Internet Explorer. Now, 2-digit years that are less than `50` are parsed as 21<sup>st</sup> century years. For example, `04/16/17`, previously parsed as April 16, 1917, will be April 16, 2017 now. To avoid any interoperability issues or ambiguous years, it is recommended to use the ISO 8601 format like "`2017-04-16`" ([bug 1265136](https://bugzilla.mozilla.org/show_bug.cgi?id=1265136)).
-   Google Chrome will accept a numerical string as a valid `dateString` parameter. This means that, for instance, while `!!Date.parse("42")` evaluates to `false` in Firefox, it evaluates to `true` in Google Chrome because "`42`" is interpreted as January 1<sup>st</sup>, 2042.

## See also

-   [`Date.UTC()`](utc)

<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/parse" class="_attribution-link">https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/parse</a>
