# d3-scale

…

Want a longer introduction? See these recommended tutorials:

* [Chapter 7. Scales](http://chimera.labs.oreilly.com/books/1230000000345/ch07.html) of *Interactive Data Visualization for the Web* by Scott Murray

* [d3: scales, and color.](http://www.jeromecukier.net/blog/2011/08/11/d3-scales-and-color/) by Jérôme Cukier

## Installing

If you use NPM, `npm install d3-scale`. Otherwise, download the [latest release](https://github.com/d3/d3-scale/releases/latest).

## API Reference

* [Linear](#linear-scales)
* [Identity](#identity-scales)
* [Power](#power-scales)
* [Log](#log-scales)
* [Quantize](#quantize-scales)
* [Quantile](#quantile-scales)
* [Threshold](#threshold-scales)
* [Ordinal](#ordinal-scales)
* [Category](#category-scales)

### Linear Scales

Linear scales are the most common scale and a good default choice to map a continuous input domain to a continuous output range. With a linear scale, each output [range](#linear_range) value *y* can be expressed as a linear function of the input [domain](#linear_domain) value *x*: *y* = *mx* + *b*. The domain is typically a dimension of data you want to visualize, such as the height of students in meters. The range is typically a dimension of the desired output visualization, such as the height of bars in pixels in a histogram.

<a name="linear" href="#linear">#</a> <b>linear</b>()

Constructs a new linear scale with the default [domain](#linear_domain) [0,1], the default [range](#linear_range) [0,1], the default [interpolator](#linear_interpolate) and [clamping](#linear_clamp) disabled.

<a name="_linear" href="#_linear">#</a> <i>linear</i>(<i>x</i>)

Given a value *x* in the [domain](#linear_domain), returns the corresponding value in the [range](#linear_range). For example, a color encoding:

```js
var s = linear()
    .domain([10, 100])
    .range(["brown", "steelblue"]);

s(20); // "#9a3439"
s(50); // "#7b5167"
```

A position encoding:

```js
var s = linear()
    .domain([10, 100])
    .range([0, 960]);

s(20); // 106.66666666666666
s(50); // 426.66666666666663
```

<a name="linear_invert" href="#linear_invert">#</a> <i>linear</i>.<b>invert</b>(<i>y</i>)

Given a value *y* in the [range](#linear_range), returns the corresponding value in the [domain](#linear_domain). This is the inverse of [*linear*](#_linear). The invert method is only supported if the output [range](#linear_range) is numeric, and will return undefined if the output range is non-numeric (such as colors, strings or objects).

For a valid value *y* in the output range, <i>linear</i>(<i>linear</i>.invert(<i>y</i>)) equals *y*; similarly, for a valid value *x* in the input domain, <i>linear</i>.invert(<i>linear</i>(<i>x</i>)) equals *x*. The invert method is useful for interaction, say to determine the value in the input domain that corresponds to the pixel location under the mouse.

<a name="linear_domain" href="#linear_domain">#</a> <i>linear</i>.<b>domain</b>([<i>domain</i>])

If *domain* is specified, sets the scale’s input domain to the specified array of numbers. The array must contain two or more numbers. If the elements in the given array are not numbers, they will be coerced to numbers. A linear scale can be used to encode types such as [dates](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Date) that can be converted to numbers; however, it is often more convenient to use [time scale](https://github.com/d3/d3-scale-time) for dates. If *numbers* is not specified, returns the scale’s current input domain.

Although linear scales typically have only two values in their domain, you can specify more than two values for a “polylinear” scale. In this case, there should be an equivalent number of values in the output [range](#linear_range). A polylinear scale represents multiple piecewise linear scales that divide a continuous domain and range. For example, to create a diverging color scale that interpolates between white and red for negative values, and white and green for positive values, say:

```js
var s = linear()
    .domain([-1, 0, 1])
    .range(["red", "white", "green"]);

s(-0.5); // "#ff8080"
s(+0.5); // "#80c080"
```

Internally, polylinear scales perform a [binary search](https://github.com/d3/d3-arrays#bisect) for the output interpolator corresponding to the given domain value.

<a name="linear_range" href="#linear_range">#</a> <i>linear</i>.<b>range</b>([<i>range</i>])

If *range* is specified, sets the scale’s output range to the specified array of values. The array must contain two or more values, matching the cardinality of the input domain; otherwise, the longer of the two is truncated to match the other. The elements in the given array need not be numbers; any value that is supported by the underlying [interpolator](#linear_interpolate) will work; however, numeric ranges are required for [invert](#linear_invert). If *values* is not specified, returns the scale’s current output range.

<a name="linear_rangeRound" href="#linear_rangeRound">#</a> <i>linear</i>.<b>rangeRound</b>(<i>range</i>)

Sets the scale’s output *range* to the specified array of values while also setting the scale’s [interpolator](#linear_interpolate) to [interpolateRound](https://github.com/d3/d3-interpolate#interpolateRound). This is a convenience routine for when the values output by the scale should be exact integers, such as to avoid antialiasing artifacts. Note that this interpolator can only be used with numeric [ranges](#linear_range).

<a name="linear_interpolate" href="#linear_interpolate">#</a> <i>linear</i>.<b>interpolate</b>([<i>interpolate</i>])

If *interpolate* is specified, sets the scale’s output interpolator factory. This interpolator factory is used to construct interpolators for each adjacent pair of values from the output [range](#linear_range); these interpolators then map a normalized domain parameter *t* in [0,1] to the corresponding value in the output range. If *factory* is not specified, returns the scale’s interpolator factory.

Note: the [default interpolator](https://github.com/d3/d3-interpolate#interpolate) **may reuse return values**. For example, if the domain values are arbitrary objects, then the default interpolator always returns the same object, modifying it in-place. If the scale is used to set an attribute or style, you typically don’t have to worry about this recyling of the scale’s return value; however, if you need to store the scale’s return value, specify your own interpolator or make a copy as appropriate.

<a name="linear_clamp" href="#linear_clamp">#</a> <i>linear</i>.<b>clamp</b>([<i>clamp</i>])

If *clamp* is specified, enables or disables clamping accordingly. By default, clamping is disabled, such that if a value outside the input domain is passed to the scale, the scale may return a value outside the output range through linear extrapolation. For example, with the default domain and range of [0,1], an input value of 2 will return an output value of 2. If clamping is enabled, the normalized domain parameter *t* is clamped to the range [0,1], such that the return value of the scale is always within the scale’s output range. If *clamp* is not specified, returns whether or not the scale currently clamps values to within the output range.

<a name="linear_nice" href="#linear_nice">#</a> <i>linear</i>.<b>nice</b>([<i>count</i>])

Extends the domain so that it starts and ends on nice round values. This method typically modifies the scale’s domain, and may only extend the bounds to the nearest round value. An optional tick *count* argument allows greater control over the step size used to extend the bounds, guaranteeing that the returned [ticks](#linear_ticks) will exactly cover the domain.

Nicing is useful if the domain is computed from data and may be irregular. For example, for a domain of [0.20147987687960267, 0.996679553296417], the nice domain is [0.2, 1]. If the domain has more than two values, nicing the domain only affects the first and last value.

<a name="linear_ticks" href="#linear_ticks">#</a> <i>linear</i>.<b>ticks</b>([<i>count</i>])

Returns approximately *count* representative values from the scale’s input domain. If *count* is not specified, it defaults to 10. The returned tick values are uniformly spaced, have human-readable values (such as multiples of powers of 10), and are guaranteed to be within the extent of the input domain. Ticks are often used to display reference lines, or tick marks, in conjunction with the visualized data. The specified *count* is only a hint; the scale may return more or fewer values depending on the input domain.

<a name="linear_tickFormat" href="#linear_tickFormat">#</a> <i>linear</i>.<b>tickFormat</b>(<i>count</i>[, <i>specifier</i>])

Returns a [number format](https://github.com/d3/d3-format) function suitable for displaying a tick value. The specified *count* should have the same value as the count that is used to generate the tick values. You don’t have to use the scale’s built-in tick format, but it automatically computes the appropriate precision based on the fixed interval between tick values.

The optional *specifier* argument allows a [custom format](https://github.com/d3/d3-format#locale_format) where the precision of the format is automatically substituted by the scale to be appropriate for the tick interval. For example, to format percentage change, you might say:

```js
var s = linear().domain([-1, 1]),
    ticks = s.ticks(5),
    format = s.tickFormat(5, "+%");

ticks.map(format); // ["-100%", "-50%", "+0%", "+50%", "+100%"]
```

If *specifier* uses the format type `s`, the scale will return a [SI-prefix format](https://github.com/d3/d3-format#locale_formatPrefix) based on the largest value in the domain. If the *specifier* already specifies a precision, this method is equivalent to [format](https://github.com/d3/d3-format#locale_format).

<a name="linear_copy" href="#linear_copy">#</a> <i>linear</i>.<b>copy</b>()

Returns an exact copy of this linear scale. Changes to this scale will not affect the returned scale, and vice versa.

### Identity Scales

Identity scales are a special case of linear scales where the domain and range are identical; the scale and its invert method are both the identity function. These scales are occasionally useful when working with pixel coordinates, say in conjunction with the axis and brush components.

<a name="identity" href="#identity">#</a> <b>identity</b>()

Constructs a new identity scale with the default domain [0,1] and the default range [0,1]. An identity scale is always equivalent to the identity function.

<a name="_identity" href="#_identity">#</a> <i>identity</i>(<i>x</i>)<br>
<a href="#_identity">#</a> <i>identity</i>.<b>invert</b>(<i>x</i>)

Returns the given value *x*.

<a name="identity_domain" href="#identity_domain">#</a> <i>identity</i>.<b>domain</b>([<i>domain</i>])<br>
<a href="#identity_domain">#</a> <i>identity</i>.<b>range</b>([<i>domain</i>])

If *domain* is specified, sets the scale’s input domain and output range to the specified array of numbers. The array must contain two or more numbers. If the elements in the given array are not numbers, they will be coerced to numbers. If numbers is not specified, returns the scale’s current input domain (or equivalently, output range).

<a name="identity_ticks" href="#identity_ticks">#</a> <i>identity</i>.<b>ticks</b>([<i>count</i>])

Returns approximately *count* representative values from the scale’s input domain (or equivalently, output range). If *count* is not specified, it defaults to 10. The returned tick values are uniformly spaced, have human-readable values (such as multiples of powers of 10), and are guaranteed to be within the extent of the input domain. Ticks are often used to display reference lines, or tick marks, in conjunction with the visualized data. The specified *count* is only a hint; the scale may return more or fewer values depending on the input domain.

<a name="identity_tickFormat" href="#identity_tickFormat">#</a> <i>identity</i>.<b>tickFormat</b>(<i>count</i>[, <i>specifier</i>])

Returns a [number format](https://github.com/d3/d3-format) function suitable for displaying a tick value. The specified *count* should have the same value as the count that is used to generate the tick values. You don’t have to use the scale’s built-in tick format, but it automatically computes the appropriate precision based on the fixed interval between tick values.

The optional *specifier* argument allows a [custom format](https://github.com/d3/d3-format#locale_format) to be specified. If the format specifier doesn’t have a defined precision, the precision will be set automatically by the scale, returning the appropriate format. This provides a convenient, declarative way of specifying a format whose precision will be automatically set by the scale.

<a name="identity_copy" href="#identity_copy">#</a> <i>identity</i>.<b>copy</b>()

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.

### Power Scales

Power scales are similar to linear scales, except an exponential transform is applied to the input domain value before the output range value is computed. The mapping to the output range value *y* can be expressed as a function of the input domain value *x*: *y* = *mx^k* + *b*, where *k* is the exponent value. Power scales also support negative values, in which case the input value is multiplied by -1, and the resulting output value is also multiplied by -1.

<a name="sqrt" href="#sqrt">#</a> <b>sqrt</b>()

Constructs a new power scale with the default domain [0,1], the default range [0,1], and the exponent .5. This method is shorthand for:

```js
pow().exponent(.5)
```

The returned scale is a function that takes a single argument *x* representing a value in the input domain; the return value is the corresponding value in the output range. Thus, the returned scale is equivalent to the [sqrt](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Math/sqrt) function for numbers; for example sqrt(0.25) returns 0.5.

<a name="pow" href="#pow">#</a> <b>pow</b>()

Constructs a new power scale with the default domain [0,1], the default range [0,1], and the default exponent 1. Thus, the default power scale is equivalent to the identity function for numbers; for example pow(0.5) returns 0.5.

<a name="_pow" href="#_pow">#</a> <i>pow</i>(<i>x</i>)

Given a value *x* in the input domain, returns the corresponding value in the output range. For example, a color encoding:

```js
var s = pow()
    .exponent(.5)
    .domain([10, 100])
    .range(["brown", "steelblue"]);

s(20); // "#933b44"
s(50); // "#6f5c79"
```

A position encoding:

```js
var s = pow()
    .exponent(.5)
    .domain([10, 100])
    .range([0, 960]);

s(20); // 183.90099810179152
s(50); // 548.7848671143346
```

<a name="pow_invert" href="#pow_invert">#</a> <i>pow</i>.<b>invert</b>(<i>y</i>)

Returns the value in the input domain *x* for the corresponding value in the output range *y*. This represents the inverse mapping from range to domain.

For a valid value *y* in the output range, <i>pow</i>(<i>pow</i>.invert(<i>y</i>)) equals *y*; similarly, for a valid value *x* in the input domain, <i>pow</i>.invert(<i>pow</i>(<i>x</i>)) equals *x*. The invert method is useful for interaction, say to determine the value in the input domain that corresponds to the pixel location under the mouse.

Note: the invert method is only supported if the output [range](#pow_range) is numeric, and will return undefined if the output range is non-numeric (such as colors, strings or objects).

<a name="pow_exponent" href="#pow_exponent">#</a> <i>pow</i>.<b>exponent</b>([<i>k</i>])

If *k* is specified, sets the current exponent to the given numeric value. If *k* is not specified, returns the current exponent. The default value is 1.

<a name="pow_domain" href="#pow_domain">#</a> <i>pow</i>.<b>domain</b>([<i>numbers</i>])

If *numbers* is specified, sets the scale’s input domain to the specified array of numbers. The array must contain two or more numbers. If the elements in the given array are not numbers, they will be coerced to numbers. If *numbers* is not specified, returns the scale’s current input domain.

As with [*linear*.domain](#linear_domain), this method can accept more than two values for the domain and range, thus resulting in a “polypower” scale.

<a name="pow_range" href="#pow_range">#</a> <i>pow</i>.<b>range</b>([<i>values</i>])

If *values* is specified, sets the scale’s output range to the specified array of values. The array must contain two or more values, matching the cardinality of the input domain; otherwise, the longer of the two is truncated to match the other. The elements in the given array need not be numbers; any value that is supported by the underlying [interpolator](#pow_interpolate) will work; however, numeric ranges are required for [invert](#pow_invert). If *values* is not specified, returns the scale’s current output range.

<a name="pow_rangeRound" href="#pow_rangeRound">#</a> <i>pow</i>.<b>rangeRound</b>(<i>range</i>)

Sets the scale’s output *range* to the specified array of values while also setting the scale’s [interpolator](#pow_interpolate) to [interpolateRound](https://github.com/d3/d3-interpolate#interpolateRound). This is a convenience routine for when the values output by the scale should be exact integers, such as to avoid antialiasing artifacts. Note that this interpolator can only be used with numeric [ranges](#pow_range).

<a name="pow_interpolate" href="#pow_interpolate">#</a> <i>pow</i>.<b>interpolate</b>([<i>interpolate</i>])

If *interpolate* is specified, sets the scale’s output interpolator factory. This interpolator factory is used to construct interpolators for each adjacent pair of values from the output [range](#pow_range); these interpolators then map a normalized domain parameter *t* in [0,1] to the corresponding value in the output range. If *factory* is not specified, returns the scale’s interpolator factory.

Note: the [default interpolator](https://github.com/d3/d3-interpolate#interpolate) **may reuse return values**. For example, if the domain values are arbitrary objects, then the default interpolator always returns the same object, modifying it in-place. If the scale is used to set an attribute or style, you typically don’t have to worry about this recyling of the scale’s return value; however, if you need to store the scale’s return value, specify your own interpolator or make a copy as appropriate.

<a name="pow_clamp" href="#pow_clamp">#</a> <i>pow</i>.<b>clamp</b>([<i>clamp</i>])

If *clamp* is specified, enables or disables clamping accordingly. By default, clamping is disabled, such that if a value outside the input domain is passed to the scale, the scale may return a value outside the output range through linear extrapolation. For example, with the default domain and range of [0,1] and an exponent of 0.5, an input value of 2 will return an output value of ~1.4142. If clamping is enabled, the normalized domain parameter *t* is clamped to the range [0,1], such that the return value of the scale is always within the scale’s output range. If *clamp* is not specified, returns whether or not the scale currently clamps values to within the output range.

<a name="pow_nice" href="#pow_nice">#</a> <i>pow</i>.<b>nice</b>([<i>count</i>])

Extends the domain so that it starts and ends on nice round values. This method typically modifies the scale’s domain, and may only extend the bounds to the nearest round value. An optional tick *count* argument allows greater control over the step size used to extend the bounds, guaranteeing that the returned [ticks](#pow_ticks) will exactly cover the domain.

Nicing is useful if the domain is computed from data and may be irregular. For example, for a domain of [0.20147987687960267, 0.996679553296417], the nice domain is [0.2, 1]. If the domain has more than two values, nicing the domain only affects the first and last value.

<a name="pow_ticks" href="#pow_ticks">#</a> <i>pow</i>.<b>ticks</b>([<i>count</i>])

Returns approximately *count* representative values from the scale’s input domain. If *count* is not specified, it defaults to 10. The returned tick values are uniformly spaced, have human-readable values (such as multiples of powers of 10), and are guaranteed to be within the extent of the input domain. Ticks are often used to display reference lines, or tick marks, in conjunction with the visualized data. The specified *count* is only a hint; the scale may return more or fewer values depending on the input domain.

<a name="pow_tickFormat" href="#pow_tickFormat">#</a> <i>pow</i>.<b>tickFormat</b>([<i>count</i>[, <i>specifier</i>]])

Returns a [number format](https://github.com/d3/d3-format) function suitable for displaying a tick value. The specified *count* should have the same value as the count that is used to generate the tick values. You don’t have to use the scale’s built-in tick format, but it automatically computes the appropriate precision based on the fixed interval between tick values.

The optional *specifier* argument allows a [custom format](https://github.com/d3/d3-format#locale_format) to be specified. If the format specifier doesn’t have a defined precision, the precision will be set automatically by the scale, returning the appropriate format. This provides a convenient, declarative way of specifying a format whose precision will be automatically set by the scale.

<a name="pow_copy" href="#pow_copy">#</a> <i>pow</i>.<b>copy</b>()

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.

### Log Scales

Log scales are similar to linear scales, except a logarithmic transform is applied to the input domain value before the output range value is computed. The mapping to the output range value *y* can be expressed as a function of the input domain value *x*: *y* = *m* log(<i>x</i>) + *b*.

As log(0) = -∞, a log scale must have either an exclusively-positive or exclusively-negative domain; the domain must not include or cross zero. A log scale with a positive domain has a well-defined behavior for positive values, and a log scale with a negative domain has a well-defined behavior for negative values. (For a negative domain, input and output values are implicitly multiplied by -1.) The behavior of the scale is undefined if you pass a negative value to a log scale with a positive domain or vice versa.

<a name="log" href="#log">#</a> <b>log</b>()

Constructs a new log scale with the default domain [1,10], the default range [0,1], and the base 10.

<a name="_log" href="#_log">#</a> <i>log</i>(<i>x</i>)

Given a value *x* in the input domain, returns the corresponding value in the output range. For example, a color encoding:

```js
var s = log()
    .domain([10, 100])
    .range(["brown", "steelblue"]);

s(20); // "#884454"
s(50); // "#63688a"
```

A position encoding:

```js
var s = log()
    .domain([10, 100])
    .range([0, 960]);

s(20); // 288.9887958374218
s(50); // 671.0112041625778
```

<a name="log_invert" href="#log_invert">#</a> <i>log</i>.<b>invert</b>(<i>y</i>)

Returns the value in the input domain *x* for the corresponding value in the output range *y*. This represents the inverse mapping from range to domain.

For a valid value *y* in the output range, <i>log</i>(<i>log</i>.invert(<i>y</i>)) equals *y*; similarly, for a valid value *x* in the input domain, <i>log</i>.invert(<i>log</i>(<i>x</i>)) equals *x*. The invert method is useful for interaction, say to determine the value in the input domain that corresponds to the pixel location under the mouse.

Note: the invert method is only supported if the output [range](#log_range) is numeric, and will return undefined if the output range is non-numeric (such as colors, strings or objects).

<a name="log_base" href="#log_base">#</a> <i>log</i>.<b>base</b>([<i>base</i>])

If *base* is specified, sets the base for this logarithmic scale. If *base* is not specified, returns the current base, which defaults to 10.

<a name="log_domain" href="#log_domain">#</a> <i>log</i>.<b>domain</b>([<i>domain</i>])

If *domain* is specified, sets the scale’s input domain to the specified array of numbers. The array must contain two or more numbers. If the elements in the given array are not numbers, they will be coerced to numbers. If *numbers* is not specified, returns the scale’s current input domain.

As with [*linear*.domain](#linear_domain), this method can accept more than two values for the domain and range, thus resulting in a “polylog” scale.

<a name="log_range" href="#log_range">#</a> <i>log</i>.<b>range</b>([<i>range</i>])

If *range* is specified, sets the scale’s output range to the specified array of values. The array must contain two or more values, matching the cardinality of the input domain; otherwise, the longer of the two is truncated to match the other. The elements in the given array need not be numbers; any value that is supported by the underlying [interpolator](#log_interpolate) will work; however, numeric ranges are required for [invert](#log_invert). If *values* is not specified, returns the scale’s current output range.

<a name="log_rangeRound" href="#log_rangeRound">#</a> <i>log</i>.<b>rangeRound</b>(<i>range</i>)

Sets the scale’s output *range* to the specified array of values while also setting the scale’s [interpolator](#log_interpolate) to [interpolateRound](https://github.com/d3/d3-interpolate#interpolateRound). This is a convenience routine for when the values output by the scale should be exact integers, such as to avoid antialiasing artifacts. Note that this interpolator can only be used with numeric [ranges](#log_range).

<a name="log_interpolate" href="#log_interpolate">#</a> <i>log</i>.<b>interpolate</b>([<i>interpolate</i>])

If *interpolate* is specified, sets the scale’s output interpolator factory. This interpolator factory is used to construct interpolators for each adjacent pair of values from the output [range](#log_range); these interpolators then map a normalized domain parameter *t* in [0,1] to the corresponding value in the output range. If *factory* is not specified, returns the scale’s interpolator factory.

Note: the [default interpolator](https://github.com/d3/d3-interpolate#interpolate) **may reuse return values**. For example, if the domain values are arbitrary objects, then the default interpolator always returns the same object, modifying it in-place. If the scale is used to set an attribute or style, you typically don’t have to worry about this recyling of the scale’s return value; however, if you need to store the scale’s return value, specify your own interpolator or make a copy as appropriate.

<a name="log_clamp" href="#log_clamp">#</a> <i>log</i>.<b>clamp</b>([<i>clamp</i>])

If *clamp* is specified, enables or disables clamping accordingly. By default, clamping is disabled, such that if a value outside the input domain is passed to the scale, the scale may return a value outside the output range through linear extrapolation. For example, with the default domain of [1,10], default range of [0,1] and default base of 10, an input value of 20 will return an output value of ~1.3010. If clamping is enabled, the normalized domain parameter *t* is clamped to the range [0,1], such that the return value of the scale is always within the scale’s output range. If *clamp* is not specified, returns whether or not the scale currently clamps values to within the output range.

<a name="log_nice" href="#log_nice">#</a> <i>log</i>.<b>nice</b>()

Extends the domain so that it starts and ends on nice round values. This method typically modifies the scale’s domain, and may only extend the bounds to the nearest round value. The nearest round value is based on integer powers of the scale’s [base](#log_base).

Nicing is useful if the domain is computed from data and may be irregular. For example, for a domain of [0.20147987687960267, 0.996679553296417], the nice domain is [0.1, 1]. If the domain has more than two values, nicing the domain only affects the first and last value.

<a name="log_ticks" href="#log_ticks">#</a> <i>log</i>.<b>ticks</b>()

Returns representative values from the scale’s input domain. The returned tick values are uniformly spaced within each power of ten, and are guaranteed to be within the extent of the input domain. Ticks are often used to display reference lines, or tick marks, in conjunction with the visualized data. Note that the number of ticks cannot be customized (due to the nature of log scales); however, you can filter the returned array of values if you want to reduce the number of ticks.

<a name="log_tickFormat" href="#log_tickFormat">#</a> <i>log</i>.<b>tickFormat</b>([<i>count</i>[, <i>format</i>]])

Returns a [number format](https://github.com/d3/d3-format) function suitable for displaying a tick value. If a *count* is specified, then some of the tick labels may not be displayed; this is useful if there is not enough room to fit all of the tick labels. However, note that the tick marks will still be displayed (so that the log scale distortion remains visible).

When specifying a count, you may also override the *format* function; you can also specify a format specifier as a string, and it will automatically be converted to a function with [format](https://github.com/d3/d3-format#locale_format). For example, to get a tick formatter that will display 20 ticks of a currency:

```js
s.tickFormat(20, "$,f")
```

If the format specifier doesn’t have a defined precision, the precision will be set automatically by the scale, returning the appropriate format. This provides a convenient, declarative way of specifying a format whose precision will be automatically set by the scale.

<a name="log_copy" href="#log_copy">#</a> <i>log</i>.<b>copy</b>()

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.

### Quantize Scales

Quantize scales are a variant of linear scales with a discrete rather than continuous range. The input domain is still continuous, and divided into uniform segments based on the number of values in (the cardinality of) the output range. The mapping is *linear* in that the output range value *y* can be expressed as a linear function of the input domain value *x*: *y* = *mx* + *b*. The input domain is typically a dimension of the data that you want to visualize, such as the height of students in meters in a sample population. The output range is typically a dimension of the desired output visualization, such as the height of bars in pixels in a histogram. See [bl.ocks.org/4060606](http://bl.ocks.org/mbostock/4060606) for an example.

<a name="quantize" href="#quantize">#</a> <b>quantize</b>()

Constructs a new quantize scale with the default domain [0,1] and the default range [0,1]. Thus, the default quantize scale is equivalent to the [round](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Math/round) function for numbers.

<a name="_quantize" href="#_quantize">#</a> <i>quantize</i>(<i>x</i>)

Given a value *x* in the input domain, returns the corresponding value in the output range. For example, a color encoding:

```js
var s = quantize()
    .domain([0, 1])
    .range(["brown", "steelblue"]);

s(0.49); // "brown"
s(0.51); // "steelblue"
```

Dividing the domain into three equally-sized parts with different range values, say to compute an appropriate stroke width:

```js
var s = quantize()
    .domain([10, 100])
    .range([1, 2, 4]);

s(20); // 1
s(50); // 2
s(80); // 4
```

<a name="quantize_invertExtent" href="#quantize_invertExtent">#</a> <i>quantize</i>.<b>invertExtent</b>(<i>y</i>)

Returns the extent of values in the input domain [<i>x0</i>, <i>x1</i>] for the corresponding value in the output range *y*, representing the inverse mapping from range to domain. This method is useful for interaction, say to determine the value in the input domain that corresponds to the pixel location under the mouse.

```js
var s = quantize()
    .domain([10, 100])
    .range([1, 2, 4]);

s.invertExtent(2); // [40, 70]
```

<a name="quantize_domain" href="#quantize_domain">#</a> <i>quantize</i>.<b>domain</b>([<i>domain</i>])

If *domain* is specified, sets the scale’s input domain to the specified two-element array of numbers. If the array contains more than two numbers, only the first and last number are used. If the elements in the given array are not numbers, they will be coerced to numbers. If *numbers* is not specified, returns the scale’s current input domain.

<a name="quantize_range" href="#quantize_range">#</a> <i>quantize</i>.<b>range</b>([<i>range</i>])

If *range* is specified, sets the scale’s output range to the specified array of values. The array may contain any number of discrete values. The elements in the given array need not be numbers; any value or type will work. If *values* is not specified, returns the scale’s current output range.

<a name="quantize_copy" href="#quantize_copy">#</a> <i>quantize</i>.<b>copy</b>()

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.

### Quantile Scales

Quantile scales map an input domain to a discrete range. Although the input domain is continuous and the scale will accept any reasonable input value, the input domain is specified as a discrete set of values. The number of values in (the cardinality of) the output range determines the number of quantiles that will be computed from the input domain. To compute the quantiles, the input domain is sorted, and treated as a [population of discrete values](https://en.wikipedia.org/wiki/Quantile#Quantiles_of_a_population). The input domain is typically a dimension of the data that you want to visualize, such as the daily change of the stock market. The output range is typically a dimension of the desired output visualization, such as a diverging color scale. See [bl.ocks.org/8ca036b3505121279daf](http://bl.ocks.org/mbostock/8ca036b3505121279daf) for an example.

<a name="quantile" href="#quantile">#</a> <b>quantile</b>()

Constructs a new quantile scale with an empty domain and an empty range. The quantile scale is invalid until both a domain and range are specified.

<a name="_quantile" href="#_quantile">#</a> <i>quantile</i>(<i>x</i>)

Given a value *x* in the input domain, returns the corresponding value in the output range.

<a name="quantile_invertExtent" href="#quantile_invertExtent">#</a> <i>quantile</i>.<b>invertExtent</b>(<i>y</i>)

Returns the extent of values in the input domain [<i>x0</i>, <i>x1</i>] for the corresponding value in the output range *y*, representing the inverse mapping from range to domain. This method is useful for interaction, say to determine the value in the input domain that corresponds to the pixel location under the mouse.

<a name="quantile_domain" href="#quantile_domain">#</a> <i>quantile</i>.<b>domain</b>([<i>numbers</i>])

If *numbers* is specified, sets the input domain of the quantile scale to the specified set of discrete numeric values. The array must not be empty, and must contain at least one numeric value; NaN, null and undefined values are ignored and not considered part of the sample population. If the elements in the given array are not numbers, they will be coerced to numbers. A copy of the input array is sorted and stored internally. If *numbers* is not specified, returns the scale’s current input domain.

<a name="quantile_range" href="#quantile_range">#</a> <i>quantile</i>.<b>range</b>([<i>values</i>])

If *values* is specified, sets the discrete values in the output range. The array must not be empty, and may contain any type of value. The number of values in (the cardinality, or length, of) the *values* array determines the number of quantiles that are computed. For example, to compute quartiles, *values* must be an array of four elements such as [0, 1, 2, 3]. If *values* is not specified, returns the current output range.

<a name="quantile_quantiles" href="#quantile_quantiles">#</a> <i>quantile</i>.<b>quantiles</b>()

Returns the quantile thresholds. If the output range contains *n* discrete values, the returned threshold array will contain *n* - 1 values. Values less than the first element in the thresholds array, quantiles()[0], are considered in the first quantile; greater values less than the second threshold are in the second quantile, and so on. Internally, the thresholds array is used with [bisect](https://github.com/d3/d3-arrays#bisect) to find the output quantile associated with the given input value.

<a name="quantile_copy" href="#quantile_copy">#</a> <i>quantile</i>.<b>copy</b>()

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.

### Threshold Scales

Threshold scales are similar to quantize scales, except they allow you to map arbitrary subsets of the domain to discrete values in the range. The input domain is still continuous, and divided into slices based on a set of threshold values. The input domain is typically a dimension of the data that you want to visualize, such as the height of students in meters in a sample population. The output range is typically a dimension of the desired output visualization, such as a set of colors. See [bl.ocks.org/3306362](http://bl.ocks.org/mbostock/3306362) for an example.

<a name="threshold" href="#threshold">#</a> <b>threshold</b>()

Constructs a new threshold scale with the default domain [.5] and the default range [0,1]. Thus, the default threshold scale is equivalent to the [round](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Math/round) function for numbers; for example threshold(0.49) returns 0, and threshold(0.51) returns 1.

```js
var s = threshold()
    .domain([0, 1])
    .range(["a", "b", "c"]);

s(-1);   // "a"
s(0);    // "b"
s(0.5);  // "b"
s(1);    // "c"
s(1000); // "c"
s.invertExtent("a"); // [undefined, 0]
s.invertExtent("b"); // [0, 1]
s.invertExtent("c"); // [1, undefined]
```

<a name="_threshold" href="#_threshold">#</a> <i>threshold</i>(<i>x</i>)

Given a value *x* in the input domain, returns the corresponding value in the output range.

<a name="threshold_invertExtent" href="#threshold_invertExtent">#</a> <i>threshold</i>.<b>invertExtent</b>(<i>y</i>)

Returns the extent of values in the input domain [<i>x0</i>, <i>x1</i>] for the corresponding value in the output range *y*, representing the inverse mapping from range to domain. This method is useful for interaction, say to determine the value in the input domain that corresponds to the pixel location under the mouse.

<a name="threshold_domain" href="#threshold_domain">#</a> <i>threshold</i>.<b>domain</b>([<i>domain</i>])

If *domain* is specified, sets the scale’s input domain to the specified array of values. The values must be in sorted ascending order, or the behavior of the scale is undefined. The values are typically numbers, but any naturally ordered values (such as strings) will work; a threshold scale can be used to encode any type that is ordered. If the number of values in the scale’s range is N+1, the number of values in the scale’s domain must be N. If there are fewer than N elements in the domain, the additional values in the range are ignored. If there are more than N elements in the domain, the scale may return undefined for some inputs. If *domain* is not specified, returns the scale’s current input domain.

<a name="threshold_range" href="#threshold_range">#</a> <i>threshold</i>.<b>range</b>([<i>values</i>])

If *values* is specified, sets the scale’s output range to the specified array of values. If the number of values in the scale’s domain is N, the number of values in the scale’s range must be N+1. If there are fewer than N+1 elements in the range, the scale may return undefined for some inputs. If there are more than N+1 elements in the range, the additional values are ignored. The elements in the given array need not be numbers; any value or type will work. If *values* is not specified, returns the scale’s current output range.

<a name="threshold_copy" href="#threshold_copy">#</a> <i>threshold</i>.<b>copy</b>()

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.

### Ordinal Scales

Unlike quantitative scales, ordinal scales have a discrete domain and range. For example, an ordinal scale might map a set of named categories to a set of colors, or determine the horizontal positions of columns in a column chart.

<a name="ordinal" href="#ordinal">#</a> <b>ordinal</b>()

Constructs a new ordinal scale with an empty domain and an empty range. The ordinal scale is invalid (always returning undefined) until an output range is specified.

<a name="_ordinal" href="#_ordinal">#</a> <i>ordinal</i>(<i>x</i>)

Given a value *x* in the input domain, returns the corresponding value in the output range.

If the range was specified explicitly (as by [range](#ordinal_range), but not [rangeBands](#ordinal_rangeBands), [rangeRoundBands](#ordinal_rangeRoundBands) or [rangePoints](#ordinal_rangePoints)), _and_ the given value *x* is not in the scale’s [domain](#ordinal_domain), then *x* is implicitly added to the domain; subsequent invocations of the scale given the same value *x* will return the same value *y* from the range.

<a name="ordinal_domain" href="#ordinal_domain">#</a> <i>ordinal</i>.<b>domain</b>([<i>values</i>])

If *values* is specified, sets the input domain of the ordinal scale to the specified array of values. The first element in *values* will be mapped to the first element in the output range, the second domain value to the second range value, and so on. Domain values are stored internally in an associative array as a mapping from value to index; the resulting index is then used to retrieve a value from the output range. Thus, an ordinal scale's values must be coercible to a string, and the stringified version of the domain value uniquely identifies the corresponding range value. If *values* is not specified, this method returns the current domain.

Setting the domain on an ordinal scale is optional. If no domain is set, a [range](#ordinal_range) must be set explicitly. Then, each unique value that is passed to the scale function will be assigned a new value from the output range; in other words, the domain will be inferred implicitly from usage. Although domains may thus be constructed implicitly, it is still a good idea to assign the ordinal scale's domain explicitly to ensure deterministic behavior, as inferring the domain from usage will be dependent on ordering.

<a name="ordinal_range" href="#ordinal_range">#</a> <i>ordinal</i>.<b>range</b>([<i>values</i>])

If *values* is specified, sets the output range of the ordinal scale to the specified array of values. The first element in the domain will be mapped to the first element in *values*, the second domain value to the second range value, and so on. If there are fewer elements in the range than in the domain, the scale will recycle values from the start of the range. If *values* is not specified, this method returns the current output range.

This method is intended for when the set of discrete output values is computed explicitly, such as a set of categorical colors. In other cases, such as determining the layout of an ordinal scatterplot or bar chart, you may find the [rangePoints](#ordinal_rangePoints) or [rangeBands](#ordinal_rangeBands) operators more convenient.

<a name="ordinal_rangePoints" href="#ordinal_rangePoints">#</a> <i>ordinal</i>.<b>rangePoints</b>(<i>interval</i>[, <i>padding</i>])

Sets the output range from the specified continuous *interval*. The array *interval* contains two elements representing the minimum and maximum numeric value. This interval is subdivided into *n* evenly-spaced **points**, where *n* is the number of (unique) values in the input domain. The first and last point may be offset from the edge of the interval according to the specified *padding*, which defaults to zero. The *padding* is expressed as a multiple of the spacing between points. A reasonable value is 1.0, such that the first and last point will be offset from the minimum and maximum value by half the distance between points.

![rangepoints](https://f.cloud.github.com/assets/230541/538689/46d87118-c193-11e2-83ab-2008df7c36aa.png)

```js
var s = ordinal()
    .domain([1, 2, 3, 4])
    .rangePoints([0, 100]);

s.range(); // [0, 33.333333333333336, 66.66666666666667, 100]
```

<a name="ordinal_rangeRoundPoints" href="#ordinal_rangeRoundPoints">#</a> <i>ordinal</i>.<b>rangeRoundPoints</b>(<i>interval</i>[, <i>padding</i>])

Like [rangePoints](#ordinal_rangePoints), except guarantees that the range values are integers so as to avoid antialiasing artifacts.

```js
var s = ordinal()
    .domain([1, 2, 3, 4])
    .rangeRoundPoints([0, 100]);

s.range(); // [1, 34, 67, 100]
```

Note that rounding necessarily introduces additional outer padding which is, on average, proportional to the length of the domain. For example, for a domain of size 50, an additional 25px of outer padding on either side may be required. Modifying the range extent to be closer to a multiple of the domain length may reduce the additional padding.

```js
var s = ordinal()
    .domain(d3.range(50))
    .rangeRoundPoints([0, 95]);

s.range(); // [23, 24, 25, …, 70, 71, 72]
s.rangeRoundPoints([0, 100]);
s.range(); // [1, 3, 5, …, 95, 97, 98]
```

(Alternatively, you could round the output of the scale manually or apply shape-rendering: crispEdges. However, this will result in irregularly spaced points.)

<a name="ordinal_rangeBands" href="#ordinal_rangeBands">#</a> <i>ordinal</i>.<b>rangeBands</b>(<i>interval</i>[, <i>padding</i>[, <i>outerPadding</i>]])

Sets the output range from the specified continuous *interval*. The array *interval* contains two elements representing the minimum and maximum numeric value. This interval is subdivided into *n* evenly-spaced **bands**, where *n* is the number of (unique) values in the input domain. The bands may be offset from the edge of the interval and other bands according to the specified *padding*, which defaults to zero. The padding is typically in the range [0,1] and corresponds to the amount of space in the range interval to allocate to padding. A value of 0.5 means that the band width will be equal to the padding width. The *outerPadding* argument is for the entire group of bands; a value of 0 means there will be padding only between rangeBands.

![rangebands](https://f.cloud.github.com/assets/230541/538688/46c298c0-c193-11e2-9a7e-15d9abcfab9b.png)

```js
var s = ordinal()
    .domain([1, 2, 3])
    .rangeBands([0, 100]);

s.rangeBand(); // 33.333333333333336
s.range(); // [0, 33.333333333333336, 66.66666666666667]
s.rangeExtent(); // [0, 100]
```

<a name="ordinal_rangeRoundBands" href="#ordinal_rangeRoundBands">#</a> <i>ordinal</i>.<b>rangeRoundBands</b>(<i>interval</i>[, <i>padding</i>[, <i>outerPadding</i>]])

Like [rangeBands](#ordinal_rangeBands), except guarantees that range values and band width are integers so as to avoid antialiasing artifacts.

```js
var s = ordinal()
    .domain([1, 2, 3])
    .rangeRoundBands([0, 100]);

s.range(); // [1, 34, 67]
s.rangeBand(); // 33
s.rangeExtent(); // [0, 100]
```

Note that rounding necessarily introduces additional outer padding which is, on average, proportional to the length of the domain. For example, for a domain of size 50, an additional 25px of outer padding on either side may be required. Modifying the range extent to be closer to a multiple of the domain length may reduce the additional padding.

```js
var s = ordinal()
    .domain(d3.range(50))
    .rangeRoundBands([0, 95]);

s.range(); // [23, 24, 25, …, 70, 71, 72]
s.rangeRoundBands([0, 100]);
s.range(); // [0, 2, 4, …, 94, 96, 98]
```

(Alternatively, you could round the output of the scale manually or apply shape-rendering: crispEdges. However, this will result in irregularly spaced and sized bands.)

<a name="ordinal_rangeBand" href="#ordinal_rangeBand">#</a> <i>ordinal</i>.<b>rangeBand</b>()

Returns the band width. When the scale’s range is configured with rangeBands or rangeRoundBands, the scale returns the lower value for the given input. The upper value can then be computed by offsetting by the band width. If the scale’s range is set using range or rangePoints, the band width is zero.

<a name="ordinal_rangeExtent" href="#ordinal_rangeExtent">#</a> <i>ordinal</i>.<b>rangeExtent</b>()

Returns a two-element array representing the extent of the scale's range, i.e., the smallest and largest values.

<a name="ordinal_copy" href="#ordinal_copy">#</a> <i>ordinal</i>.<b>copy</b>()

Returns an exact copy of this ordinal scale. Changes to this scale will not affect the returned scale, and vice versa.

### Category Scales

<a name="category10" href="#category10">#</a> <b>category10</b>()

Constructs a new [ordinal scale](#ordinal) with a range of ten categorical colors.

![1f77b4](https://cloud.githubusercontent.com/assets/230541/8294503/052c3462-18f2-11e5-8532-8e6f95a0a2fa.png) #1f77b4<br>
![ff7f0e](https://cloud.githubusercontent.com/assets/230541/8294556/05d92a0a-18f2-11e5-8954-cd7e28d8e265.png) #ff7f0e<br>
![2ca02c](https://cloud.githubusercontent.com/assets/230541/8294504/052cedda-18f2-11e5-903f-4bc4d1e489ce.png) #2ca02c<br>
![d62728](https://cloud.githubusercontent.com/assets/230541/8294542/05a966d0-18f2-11e5-8902-ae194b562d57.png) #d62728<br>
![9467bd](https://cloud.githubusercontent.com/assets/230541/8294525/05775078-18f2-11e5-90c2-cd604dc0eb69.png) #9467bd<br>
![8c564b](https://cloud.githubusercontent.com/assets/230541/8294509/05416ab2-18f2-11e5-9093-81b592d6a40b.png) #8c564b<br>
![e377c2](https://cloud.githubusercontent.com/assets/230541/8294548/05ba04ae-18f2-11e5-8f7c-e4ca8eabe655.png) #e377c2<br>
![7f7f7f](https://cloud.githubusercontent.com/assets/230541/8294500/051d70e4-18f2-11e5-83df-c45e7bc20803.png) #7f7f7f<br>
![bcbd22](https://cloud.githubusercontent.com/assets/230541/8294532/058e2aa0-18f2-11e5-8c42-b93f8d6f210d.png) #bcbd22<br>
![17becf](https://cloud.githubusercontent.com/assets/230541/8294517/055eeac4-18f2-11e5-9bed-3ecdd0d7a87b.png) #17becf<br>

<a name="category20" href="#category20">#</a> <b>category20</b>()

Constructs a new [ordinal scale](#ordinal) with a range of twenty categorical colors.

![1f77b4](https://cloud.githubusercontent.com/assets/230541/8294503/052c3462-18f2-11e5-8532-8e6f95a0a2fa.png) #1f77b4<br>
![aec7e8](https://cloud.githubusercontent.com/assets/230541/8294530/0583af58-18f2-11e5-8ad1-e06b75439900.png) #aec7e8<br>
![ff7f0e](https://cloud.githubusercontent.com/assets/230541/8294556/05d92a0a-18f2-11e5-8954-cd7e28d8e265.png) #ff7f0e<br>
![ffbb78](https://cloud.githubusercontent.com/assets/230541/8294555/05d890ea-18f2-11e5-8aef-f618983c741f.png) #ffbb78<br>
![2ca02c](https://cloud.githubusercontent.com/assets/230541/8294504/052cedda-18f2-11e5-903f-4bc4d1e489ce.png) #2ca02c<br>
![98df8a](https://cloud.githubusercontent.com/assets/230541/8294520/05670682-18f2-11e5-8d54-5685d3fa096d.png) #98df8a<br>
![d62728](https://cloud.githubusercontent.com/assets/230541/8294542/05a966d0-18f2-11e5-8902-ae194b562d57.png) #d62728<br>
![ff9896](https://cloud.githubusercontent.com/assets/230541/8294557/05db90ba-18f2-11e5-9a48-cfdd6fe0cd81.png) #ff9896<br>
![9467bd](https://cloud.githubusercontent.com/assets/230541/8294525/05775078-18f2-11e5-90c2-cd604dc0eb69.png) #9467bd<br>
![c5b0d5](https://cloud.githubusercontent.com/assets/230541/8294535/05947f86-18f2-11e5-8325-336478038a2b.png) #c5b0d5<br>
![8c564b](https://cloud.githubusercontent.com/assets/230541/8294509/05416ab2-18f2-11e5-9093-81b592d6a40b.png) #8c564b<br>
![c49c94](https://cloud.githubusercontent.com/assets/230541/8294537/05a1bb1a-18f2-11e5-8f9d-0638b5444d5b.png) #c49c94<br>
![e377c2](https://cloud.githubusercontent.com/assets/230541/8294548/05ba04ae-18f2-11e5-8f7c-e4ca8eabe655.png) #e377c2<br>
![f7b6d2](https://cloud.githubusercontent.com/assets/230541/8294549/05c6985e-18f2-11e5-8fe1-555b340e5c1b.png) #f7b6d2<br>
![7f7f7f](https://cloud.githubusercontent.com/assets/230541/8294500/051d70e4-18f2-11e5-83df-c45e7bc20803.png) #7f7f7f<br>
![c7c7c7](https://cloud.githubusercontent.com/assets/230541/8294499/051c446c-18f2-11e5-8230-497286d225dc.png) #c7c7c7<br>
![bcbd22](https://cloud.githubusercontent.com/assets/230541/8294532/058e2aa0-18f2-11e5-8c42-b93f8d6f210d.png) #bcbd22<br>
![dbdb8d](https://cloud.githubusercontent.com/assets/230541/8294546/05b707cc-18f2-11e5-8189-fd060071b0cc.png) #dbdb8d<br>
![17becf](https://cloud.githubusercontent.com/assets/230541/8294517/055eeac4-18f2-11e5-9bed-3ecdd0d7a87b.png) #17becf<br>
![9edae5](https://cloud.githubusercontent.com/assets/230541/8294513/0552f584-18f2-11e5-93fa-823f2dec5a84.png) #9edae5<br>

<a name="category20b" href="#category20b">#</a> <b>category20b</b>()

Constructs a new [ordinal scale](#ordinal) with a range of twenty categorical colors.

![393b79](https://cloud.githubusercontent.com/assets/230541/8294519/0565d19a-18f2-11e5-8626-08531146a382.png) #393b79<br>
![5254a3](https://cloud.githubusercontent.com/assets/230541/8294523/057054c6-18f2-11e5-9921-3e4763ce32ae.png) #5254a3<br>
![6b6ecf](https://cloud.githubusercontent.com/assets/230541/8294505/052e8320-18f2-11e5-91eb-b22ab5d74aa6.png) #6b6ecf<br>
![9c9ede](https://cloud.githubusercontent.com/assets/230541/8294511/0543b3d0-18f2-11e5-9c41-7b63ec7d4e5b.png) #9c9ede<br>
![637939](https://cloud.githubusercontent.com/assets/230541/8294526/057b6014-18f2-11e5-92a9-ebabc51c8587.png) #637939<br>
![8ca252](https://cloud.githubusercontent.com/assets/230541/8294510/054208dc-18f2-11e5-9f24-676b4b350f72.png) #8ca252<br>
![b5cf6b](https://cloud.githubusercontent.com/assets/230541/8294531/058b5e2e-18f2-11e5-923f-2cf857af6060.png) #b5cf6b<br>
![cedb9c](https://cloud.githubusercontent.com/assets/230541/8294538/05a2ae76-18f2-11e5-940a-5109e8310f27.png) #cedb9c<br>
![8c6d31](https://cloud.githubusercontent.com/assets/230541/8294508/053fbc44-18f2-11e5-9984-2a233483ce06.png) #8c6d31<br>
![bd9e39](https://cloud.githubusercontent.com/assets/230541/8294534/0591c250-18f2-11e5-8d18-b1cf77245d9f.png) #bd9e39<br>
![e7ba52](https://cloud.githubusercontent.com/assets/230541/8294547/05b787a6-18f2-11e5-8cbf-97fadded7e8b.png) #e7ba52<br>
![e7cb94](https://cloud.githubusercontent.com/assets/230541/8294545/05b69bac-18f2-11e5-9253-6f0922ebb88d.png) #e7cb94<br>
![843c39](https://cloud.githubusercontent.com/assets/230541/8294522/056d4466-18f2-11e5-8dea-e72e0668bcb9.png) #843c39<br>
![ad494a](https://cloud.githubusercontent.com/assets/230541/8294529/05832b50-18f2-11e5-8811-a01316c48447.png) #ad494a<br>
![d6616b](https://cloud.githubusercontent.com/assets/230541/8294539/05a48a5c-18f2-11e5-9891-84c242808d25.png) #d6616b<br>
![e7969c](https://cloud.githubusercontent.com/assets/230541/8294553/05c9f832-18f2-11e5-9571-58955d5307de.png) #e7969c<br>
![7b4173](https://cloud.githubusercontent.com/assets/230541/8294507/05344634-18f2-11e5-9132-4bde5cd9046c.png) #7b4173<br>
![a55194](https://cloud.githubusercontent.com/assets/230541/8294528/0580d972-18f2-11e5-81d6-67a71edf3687.png) #a55194<br>
![ce6dbd](https://cloud.githubusercontent.com/assets/230541/8294541/05a61f2a-18f2-11e5-8ba5-7976b2bb58f8.png) #ce6dbd<br>
![de9ed6](https://cloud.githubusercontent.com/assets/230541/8294543/05b51354-18f2-11e5-9ab5-3acfdfb55902.png) #de9ed6<br>

<a name="category20c" href="#category20c">#</a> <b>category20c</b>()

Constructs a new [ordinal scale](#ordinal) with a range of twenty categorical colors. This color scale includes color specifications and designs developed by Cynthia Brewer ([colorbrewer.org](http://colorbrewer.org/)).

![3182bd](https://cloud.githubusercontent.com/assets/230541/8294524/05733826-18f2-11e5-93da-468df888f34e.png) #3182bd<br>
![6baed6](https://cloud.githubusercontent.com/assets/230541/8294506/05320036-18f2-11e5-9897-64b7a60fc1f9.png) #6baed6<br>
![9ecae1](https://cloud.githubusercontent.com/assets/230541/8294514/05540a14-18f2-11e5-92e1-6fe4e77a32f5.png) #9ecae1<br>
![c6dbef](https://cloud.githubusercontent.com/assets/230541/8294536/0595f00a-18f2-11e5-96da-bd6a07a6c07b.png) #c6dbef<br>
![e6550d](https://cloud.githubusercontent.com/assets/230541/8294551/05c89082-18f2-11e5-825c-39fcd1568018.png) #e6550d<br>
![fd8d3c](https://cloud.githubusercontent.com/assets/230541/8294550/05c71a7c-18f2-11e5-950e-ba1124961d0a.png) #fd8d3c<br>
![fdae6b](https://cloud.githubusercontent.com/assets/230541/8294552/05ca1164-18f2-11e5-80ac-8e4c8da9a52f.png) #fdae6b<br>
![fdd0a2](https://cloud.githubusercontent.com/assets/230541/8294554/05cc4920-18f2-11e5-9a5e-3237f59b81e7.png) #fdd0a2<br>
![31a354](https://cloud.githubusercontent.com/assets/230541/8294515/0556cc40-18f2-11e5-9960-1615d9c8907c.png) #31a354<br>
![74c476](https://cloud.githubusercontent.com/assets/230541/8294516/055a1922-18f2-11e5-9005-21977889c9cf.png) #74c476<br>
![a1d99b](https://cloud.githubusercontent.com/assets/230541/8294527/057ed708-18f2-11e5-983d-9a423278f2ea.png) #a1d99b<br>
![c7e9c0](https://cloud.githubusercontent.com/assets/230541/8294540/05a4c864-18f2-11e5-9eb2-23eb61c5641e.png) #c7e9c0<br>
![756bb1](https://cloud.githubusercontent.com/assets/230541/8294521/056acad8-18f2-11e5-905b-aa6c8817f402.png) #756bb1<br>
![9e9ac8](https://cloud.githubusercontent.com/assets/230541/8294512/0547342e-18f2-11e5-9334-12c7f9400d3c.png) #9e9ac8<br>
![bcbddc](https://cloud.githubusercontent.com/assets/230541/8294533/059195e6-18f2-11e5-844d-a5ae45346e4b.png) #bcbddc<br>
![dadaeb](https://cloud.githubusercontent.com/assets/230541/8294544/05b529b6-18f2-11e5-8841-55f543b23c76.png) #dadaeb<br>
![636363](https://cloud.githubusercontent.com/assets/230541/8294501/051e1076-18f2-11e5-9c4d-b7233e33c09a.png) #636363<br>
![969696](https://cloud.githubusercontent.com/assets/230541/8294498/051bceba-18f2-11e5-9732-ad2b95b72ea3.png) #969696<br>
![bdbdbd](https://cloud.githubusercontent.com/assets/230541/8294502/051f981a-18f2-11e5-8c4c-5f9fa4e36833.png) #bdbdbd<br>
![d9d9d9](https://cloud.githubusercontent.com/assets/230541/8294518/05601516-18f2-11e5-848c-291939213e5d.png) #d9d9d9<br>

## Changes from D3 3.x:

…
