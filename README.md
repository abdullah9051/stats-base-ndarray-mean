[![Releases](https://img.shields.io/badge/releases-view%20on%20GitHub-blue?logo=github)](https://github.com/abdullah9051/stats-base-ndarray-mean/releases)

# Arithmetic Mean for 1D ndarrays — Node.js Stats Base Module 🧮

![Node.js](https://nodejs.org/static/images/logos/nodejs-new-pantone-black.svg) ![Math](https://upload.wikimedia.org/wikipedia/commons/2/2c/Math_font_1.svg)

Compute the arithmetic mean of a one-dimensional ndarray. This small, focused module handles both plain numeric arrays and ndarray-style views (typed arrays with stride and offset). It runs in Node.js and in browsers when bundled.

- Topics: arithmetic-mean, average, avg, central-tendency, extent, javascript, math, mathematics, mean, ndarray, node, node-js, nodejs, statistics, stats, stdlib
- Releases: https://github.com/abdullah9051/stats-base-ndarray-mean/releases

Table of contents

- Features
- Install
- Quick example
- API
  - mean( x, opts )
  - Options
  - Returns
  - Errors
- Examples
  - Plain array
  - Typed array with stride
  - Masked view (offset + stride)
- Performance notes
- Tests
- Contributing
- License
- Releases

Features

- Small, single-purpose function.
- Works with plain arrays and typed arrays.
- Supports ndarray-style stride and offset.
- Avoids extra allocations.
- Deterministic numeric behavior.
- Minimal surface area, easy to audit.

Install

Use npm:

npm install stats-base-ndarray-mean

Or yarn:

yarn add stats-base-ndarray-mean

Quick example

This example uses a plain JavaScript array. The function accepts any array-like numeric container.

const mean = require( 'stats-base-ndarray-mean' );

const arr = [ 2, 4, 6, 8, 10 ];
const m = mean( arr );
console.log( m ); // 6

API

mean( x, opts )

Compute the arithmetic mean of a one-dimensional ndarray-like input.

Parameters

- x: ArrayLike<number>
  - A one-dimensional numeric container. Use a typed array (Float64Array, Float32Array, Int32Array, etc.) or a plain array.
  - If you use an ndarray-style view, pass additional options to describe the view layout.

- opts: Object (optional)
  - stride: number (default: 1)
    - Step between elements. Use negative values to walk backward.
  - offset: number (default: 0)
    - Index of first element in the underlying buffer.
  - length: number (optional)
    - Number of elements to include. If omitted, length is inferred from x.length, stride, and offset.

Returns

- number
  - The arithmetic mean of the selected elements.
  - Returns NaN when length is zero or when the input contains NaN values that influence the result.

Errors

- Throws TypeError when x is not array-like or when opts contains invalid types.
- Throws RangeError when stride or length produce an invalid range.

Behavior and edge cases

- If length is zero, the function returns NaN. This choice mirrors many numeric libraries and avoids silent division by zero.
- The function performs a single pass over the data and uses a pair of accumulators (count and sum).
- The function does not normalize for numerical stability beyond using a single-pass double precision accumulator. For large datasets with extreme values, consider a compensated summation approach or Kahan summation if you require added precision.

Examples

Plain array

const mean = require( 'stats-base-ndarray-mean' );

const a = [ 1, 2, 3, 4, 5 ];
console.log( mean( a ) ); // 3

Typed array

const mean = require( 'stats-base-ndarray-mean' );
const buf = new Float64Array( [ 1, 2, 3, 4, 5 ] );
console.log( mean( buf ) ); // 3

Stride and offset

One common pattern with ndarrays is to use stride and offset to create views. This module accepts stride and offset as options.

const mean = require( 'stats-base-ndarray-mean' );
// Underlying buffer contains interleaved values: x, skip, x, skip, ...
const buf = new Float64Array( [ 10, 0, 20, 0, 30, 0, 40, 0 ] );
// Start at index 0, take every 2nd element, length 4:
console.log( mean( buf, { stride: 2, offset: 0, length: 4 } ) ); // 25

Negative stride

const mean = require( 'stats-base-ndarray-mean' );
const buf = new Float64Array( [ 1, 2, 3, 4, 5 ] );
// Walk backward starting from index 4:
console.log( mean( buf, { stride: -1, offset: 4, length: 5 } ) ); // 3

Masked view example (offset + stride)

const mean = require( 'stats-base-ndarray-mean' );
const data = new Float32Array([100, 1, 200, 2, 300, 3]);
// We want the values at indices 0,2,4
console.log( mean( data, { offset: 0, stride: 2, length: 3 } ) ); // 200

Implementation notes

- The code uses a simple loop and a double precision accumulator.
- The function is synchronous and side-effect free.
- The function does not mutate the input.
- The module exposes a single default export for ease of use.

Performance notes

- The implementation avoids allocations and reads values sequentially when stride is 1.
- For typed arrays, V8 optimizes sequential numeric access. Strided access has higher cost.
- On large arrays, the function runs at native JavaScript speed and performs well for common tasks.
- If you need extra numeric stability for sums of widely varying magnitudes, consider using a compensated summation variant. The plain mean, however, meets the needs of most analytics tasks.

Benchmarks

- Small arrays (n < 1e4): overhead dominates. Use microbenchmarks to compare.
- Medium arrays (n ~ 1e5): linear pass dominates. Expect near memory bandwidth limits.
- Large arrays (n > 1e7): watch garbage collector and available memory.

Tests

Run unit tests with:

npm test

Tests use standard Node.js test runner (tap or ava, depending on project setup). The test suite covers:

- Basic arrays
- Typed arrays
- Stride, offset, length
- Edge cases (NaN, empty arrays, negative stride)

Contributing

- Fork the repository.
- Create a feature branch.
- Add tests for new behavior.
- Open a pull request with a clear description.
- Keep changes focused and small.

If you add features, maintain backward compatibility. Use clear variable names and document new options in the README.

License

MIT

Releases

Visit the releases page for packaged downloads and changelogs:

https://github.com/abdullah9051/stats-base-ndarray-mean/releases

Download the release artifact from the Releases page and run the packaged script. For example, download the release tarball or zip, extract it, and execute the included run script:

tar -xzf stats-base-ndarray-mean-vX.Y.Z.tgz
cd stats-base-ndarray-mean-vX.Y.Z
node bin/run.js

The Releases page lists all published versions, release notes, and binary assets when available. If the link does not work, check the repository Releases section on GitHub.