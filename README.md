# Punycode converter

A Punycode converter that fully complies with [RFC 3492](https://tools.ietf.org/html/rfc3492) and [RFC 5891](https://tools.ietf.org/html/rfc5891).

Punycode is a representation of Unicode with the limited ASCII character subset used for Internet hostnames. Using Punycode, host names containing Unicode characters are transcoded to a subset of ASCII consisting of letters, digits, and hyphens, which is called the letter–digit–hyphen (LDH) subset. For example, `München` (German name for Munich) is encoded as `Mnchen-3ya`.

## Usage

_Tip: Run the example below by typing this in your terminal (requires Deno):_

```shell
deno run \
  --allow-net --allow-run --allow-env --allow-read \
  https://deno.land/x/mdrb@2.0.0/mod.ts \
  --dax=false --mode=isolated \
  https://raw.githubusercontent.com/doga/punycode/main/README.md
```

<details data-mdrb>
<summary>Example: Convert Unicode DNS names to punycode and back.</summary>

<pre>
description = '''
Running this example is safe, it will not read or write anything to your filesystem.
'''
</pre>
</details>

```javascript
import { punycode } from 'https://esm.sh/gh/doga/punycode@1.0.0/mod.mjs';

const unicodeDnsNames = [
    'mañana.rocks', '☃-⌘.design', 'джpумлатест.bрфa', 'hello.codes',
    '0.0.0.0', '::1'
 ];

unicodeDnsNames.forEach(name => {
    console.info(
`${name}
  encoded: ${punycode.toASCII(name)}
  decoded: ${punycode.toUnicode(punycode.toASCII(name))}\n`);
});
```

Sample output for the code above:

```text
mañana.rocks
  encoded: xn--maana-pta.rocks
  decoded: mañana.rocks

☃-⌘.design
  encoded: xn----dqo34k.design
  decoded: ☃-⌘.design

джpумлатест.bрфa
  encoded: xn--p-8sbkgc5ag7bhce.xn--ba-lmcq
  decoded: джpумлатест.bрфa

hello.codes
  encoded: hello.codes
  decoded: hello.codes

0.0.0.0
  encoded: 0.0.0.0
  decoded: 0.0.0.0

::1
  encoded: ::1
  decoded: ::1
```

## The full API

### `punycode.decode(string)`

Converts a Punycode string of ASCII symbols to a string of Unicode symbols.

```text
// decode domain name parts
punycode.decode('maana-pta'); // 'mañana'
punycode.decode('--dqo34k'); // '☃-⌘'
```

### `punycode.encode(string)`

Converts a string of Unicode symbols to a Punycode string of ASCII symbols.

```text
// encode domain name parts
punycode.encode('mañana'); // 'maana-pta'
punycode.encode('☃-⌘'); // '--dqo34k'
```

### `punycode.toUnicode(input)`

Converts a Punycode string representing a domain name or an email address to Unicode. Only the Punycoded parts of the input will be converted, i.e. it doesn’t matter if you call it on a string that has already been converted to Unicode.

```text
// decode domain names
punycode.toUnicode('xn--maana-pta.com');
// → 'mañana.com'
punycode.toUnicode('xn----dqo34k.com');
// → '☃-⌘.com'

// decode email addresses
punycode.toUnicode('джумла@xn--p-8sbkgc5ag7bhce.xn--ba-lmcq');
// → 'джумла@джpумлатест.bрфa'
```

### `punycode.toASCII(input)`

Converts a lowercased Unicode string representing a domain name or an email address to Punycode. Only the non-ASCII parts of the input will be converted, i.e. it doesn’t matter if you call it with a domain that’s already in ASCII.

```text
// encode domain names
punycode.toASCII('mañana.com');
// → 'xn--maana-pta.com'
punycode.toASCII('☃-⌘.com');
// → 'xn----dqo34k.com'

// encode email addresses
punycode.toASCII('джумла@джpумлатест.bрфa');
// → 'джумла@xn--p-8sbkgc5ag7bhce.xn--ba-lmcq'
```

### `punycode.ucs2`

#### `punycode.ucs2.decode(string)`

Creates an array containing the numeric code point values of each Unicode symbol in the string. While [JavaScript uses UCS-2 internally](https://mathiasbynens.be/notes/javascript-encoding), this function will convert a pair of surrogate halves (each of which UCS-2 exposes as separate characters) into a single code point, matching UTF-16.

```text
punycode.ucs2.decode('abc');
// → [0x61, 0x62, 0x63]
// surrogate pair for U+1D306 TETRAGRAM FOR CENTRE:
punycode.ucs2.decode('\uD834\uDF06');
// → [0x1D306]
```

#### `punycode.ucs2.encode(codePoints)`

Creates a string based on an array of numeric code point values.

```text
punycode.ucs2.encode([0x61, 0x62, 0x63]);
// → 'abc'
punycode.ucs2.encode([0x1D306]);
// → '\uD834\uDF06'
```

### `punycode.version`

A string representing the current Punycode.js version number.

## License

This software is released under the [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0) license.

∎
