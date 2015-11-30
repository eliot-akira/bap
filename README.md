[![Bap](https://raw.githubusercontent.com/adamrenklint/bap-logo/master/dist/bap-medium.png)](http://bapjs.org)

[![Join the chat at https://gitter.im/adamrenklint/bap](https://img.shields.io/badge/GITTER-join_chat-blue.svg?style=flat-square)](https://gitter.im/adamrenklint/bap) [![npm](https://img.shields.io/npm/v/bap.svg?style=flat-square)](https://www.npmjs.com/package/bap) [![npm](https://img.shields.io/npm/dm/bap.svg?style=flat-square)](https://www.npmjs.com/package/bap) [![GitHub stars](https://img.shields.io/github/stars/adamrenklint/bap.svg?style=flat-square)](https://github.com/adamrenklint/bap/stargazers) [![GitHub forks](https://img.shields.io/github/forks/adamrenklint/bap.svg?style=flat-square)](https://github.com/adamrenklint/bap/network)

[![Travis branch](https://img.shields.io/travis/adamrenklint/bap.svg?style=flat-square)](https://travis-ci.org/adamrenklint/bap) [![Code Climate](https://img.shields.io/codeclimate/github/adamrenklint/bap.svg?style=flat-square)](https://codeclimate.com/github/adamrenklint/bap) [![Code Climate](https://img.shields.io/codeclimate/coverage/github/adamrenklint/bap.svg?style=flat-square)](https://codeclimate.com/github/adamrenklint/bap) [![David dependencies](https://img.shields.io/david/adamrenklint/bap.svg?style=flat-square)](https://david-dm.org/adamrenklint/bap) [![David devDependencies](https://img.shields.io/david/dev/adamrenklint/bap.svg?style=flat-square)](https://david-dm.org/adamrenklint/bap#info=devDependencies)

[Bap](http://bapjs.org) is a toolkit for making beats and composing sequences with Javascript and Web Audio for playback in modern browsers. It is inspired by the classic "MPC workflow" and built to make all aspects of beatmaking completely modular and reusable.

Made by [Adam Renklint](http://adamrenklint.com), Berlin april 2015

## Install and import

### From npm

```sh
$ npm install --save bap
```

### From rawgit CDN

```
<script src="https://cdn.rawgit.com/adamrenklint/bap/v0.4.0/bap.min.js"></script>
```

## Usage

```js
var bap = require('bap'); // or window.bap

// a kit is like an instrument, or program in mpc terms
var kit = bap.kit();
var oscillator = bap.oscillator({
  frequency: 440
});
// a kit connects infinite slots with infinite layers
kit.slot('Q').layer(oscillator);
kit.slot('W').layer(oscillator.with({ frequency: 330 }));
kit.slot('E').layer(bap.sample('foo.wav'));

// a pattern is a loop made up of channels and notes
var pattern = bap.pattern();
pattern.channel(1).add(
  ['1.*.01', '1Q', 48, 70, 0, -50],
  ['1.2.01', '1W', 96, 100, 0, 50],
  ['1.4.01', '1E']
);

// connect the kit, and play
pattern.kit(1, kit).start();
```

## Basic concepts

- Bap runs at *96 ticks per beat*, with a position signature like MPC: ```bar.beat.tick```
- Kits are like instruments (programs in MPC terms) and contains infinite slots, each with infinite layers of samples and oscillators
- Patterns are playable collections of channels containing notes, and connect with kits
- Notes are defined by six main parameters: position, target, duration, volume, pan and pitch
- Only position and target params are required, all others can be null/falsy/undefined
- Positions containing [expressions](https://github.com/adamrenklint/dilla-expressions#operators) are automatically expanded
- When a layer is played, it merges the params of the note, channel, layer, slot and kit

## Known issues

- Creating effect nodes on-the-fly is not performing well in Firefox, resulting in clipping on the initial run of a pattern

## Resources

- [Changelog](https://github.com/adamrenklint/bap/blob/master/CHANGELOG.md)
- Examples
  - [Metronome](http://adamrenklint.com/bap/#metronome)
  - [Boombap beat](http://adamrenklint.com/bap/#boombap)
  - [Sample slices](http://adamrenklint.com/bap/#slices)
  - [Multi-layered sequences](http://adamrenklint.com/bap/#sequences)
- [Related links](https://github.com/adamrenklint/bap/blob/master/links.md)

## API

- All objects are based on [ampersand-state](https://github.com/AmpersandJS/ampersand-state)
- ```on(name, callback)``` register event callback
- ```off(name, [callback])``` unregister event callback
- ```once(name, callback)``` register single-run event callback
- ```with(params)``` return a clone of itself with params
- ```toJSON()``` return current params as JSON

### bap

- ```clock``` reference to [clock](#clock) singleton
- ```volume``` number between 0 and 999, master volume setting, defaults to 100
- ```kit(params)``` returns a new [kit](#kit)
- ```slot(params)``` returns a new [slot](#slot)
- ```layer(params)``` returns a new [layer](#layer)
- ```oscillator(params)``` returns a new [oscillator](#oscillator)
- ```sample(params)``` returns a new [sample](#sample)
- ```pattern(params)``` returns a new [pattern](#pattern)
- ```sequence(sequence..., params)``` returns a new [sequence](#sequence)
- ```channel(params)``` returns a new [channel](#channel)
- ```note(params)``` returns a new [note](#note)
- ```reverb(params)``` returns a new [reverb](#reverb)
- ```delay(params)``` returns a new [delay](#delay)
- ```compressor(params)``` returns a new [compressor](#compressor)
- ```overdrive(params)``` returns a new [overdrive](#overdrive)
- ```filter(params)``` returns a new [filter](#filter)
- ```chorus(params)``` returns a new [chorus](#chorus)
- ```phaser(params)``` returns a new [phaser](#phaser)
- ```pingpong(params)``` returns a new [ping pong delay](#ping pong delay)

#### params

- ```mute``` boolean, defaults to ```false```
- ```volume``` number between ```0``` and ```999```, defaults to ```100```
- ```length``` number, length in seconds, overriden by duration if shorter
- ```duration``` number, duration in ticks, overriden by length if shorter
- ```attack``` number, attack in seconds
- ```release``` number, release in seconds
- ```pitch``` number between ```-999``` and ```999``` representing the pitch shift in percent, defaults to ```0```
- ```pan``` number between ```-100``` and ```100```, defaults to ```0```

### clock

- ```playing``` boolean, current state of playback, can be changed to start or pause
- ```position``` string in format ```bar.beat.tick```, can be set to move playback position
- ```bar```, ```beat```, ```tick``` numbers, equal and bound to position, can be set to move playback position
- ```tempo``` number, current tempo of playback, read only
- ```step``` function, called on each step with note and time as arguments, able to cancel step by returning false
- ```sequence``` pattern or sequence currently playing
- ```start()``` start playback, if current pattern is set
- ```start(pattern)``` set current pattern and start playback
- ```pause()``` stop playback
- ```stop()``` stop playback and set position to ```1.1.01```

### kit

- ```slot()``` returns blank slot assigned to next id
- ```slot(id)``` returns existing or blank slot with id
- ```slot(id, slot)``` assign slot instance to id
- ```slot(slot)``` assign slot instance to next id
- ```connect(effect)``` route output signal to destination via effect or chain
- ```bypass``` boolean to bypass all effects, effect type string or array of strings to bypass specific effect types, defaults to false

### slot

- ```layer()``` returns a blank layer assigned to next id
- ```layer(id)``` returns existing or blank layer with id
- ```layer(id, layer)``` assign layer instance to id
- ```layer(layer)``` assign layer instance to next id
- ```layer(sampleSrc)``` returns a new [sample](#sample) layer, assigned to next id
- ```start(time, [params])``` start playback of slot at (AudioContext) time
- ```start([params])``` start playback of slot immediately
- ```stop(time, [params])``` stop playback of slot at (AudioContext) time
- ```stop([params])``` stop playback of slot immediately
- ```connect(effect)``` route output signal to destination via effect or chain
- ```bypass``` boolean to bypass all effects, effect type string or array of strings to bypass specific effect types, defaults to false

### layer

- ```start(time, [params])``` start playback of slot at (AudioContext) time
- ```start([params])``` start playback of slot immediately
- ```connect(effect)``` route output signal to destination via effect or chain
- ```bypass``` boolean to bypass all effects, effect type string or array of strings to bypass specific effect types, defaults to false

#### oscillator

- ```frequency``` number, frequency of oscillation in hertz, defaults to ```0```
- ```note``` string, [note identifier](https://github.com/gre/audio-notes) like ```C3``` or ```a4``` - if set, overrides frequency
- ```shape``` string, shape of waveform, defaults to ```sine```, other values are ```square```, ```sawtooth```, ```triangle``` and ```custom```

#### sample

- ```src``` string, url used to load sample buffer
- ```offset``` number, starting point offset in seconds, defaults to ```0```
- ```channel``` string, defines how to handle stereo buffers: ```left``` or ```right``` uses a single channel, ```merge``` and ```diff``` combines or differentates between channels, default is ```null``` and does nothing
- ```reverse``` boolean, reverse buffer or slice of buffer
- ```loop``` number, loop length in seconds, defaults to ```0``` i.e. not looping
- ```slice(pieces)``` returns a kit with the sample sliced into even-sized sections
- ```bitcrush``` number between 0 and 16, resamples waveform to defined bit depth, defaults to ```0```, i.e. no resampling
- ```bitcrushFrequency``` number between 20 and 22050, normalization frequency at which to apply the bitcrusher effect, defaults to 6500
- ```bitcrushMix``` number between 0 and 100, ratio of wet bitcrushed signal to mix with dry signal, defaults to 50
- ```trimToZeroCrossingPoint``` boolean, automatically trim sample start and end to zero crossing point to avoid clipping, defaults to true

### pattern

- ```playing``` boolean, current state of playback, can be changed to start or pause
- ```tempo``` number, playback tempo in bpm, defaults to 120
- ```bars``` number, length of pattern in bars, defaults to 1
- ```beatsPerBar``` number, amount of beats per bar, defaults to 4
- ```loop``` boolean, define if pattern should loop, defaults to true
- ```volume``` number between 0 and 999, master volume for pattern, defaults to 100
- ```transform``` function to be called after expanding position expressions into notes, called after ```channel.transform```
- ```channel()``` returns a blank channel assigned to next id
- ```channel(id)``` returns existing or blank channel with id
- ```channel(id, channel)``` assign channel instance to id
- ```channel(channel)``` assign channel instance to next id
- ```start()``` start playback of pattern
- ```pause()``` stop playback
- ```stop()``` stop playback and set position to ```1.1.01```
- ```kit(id, kit)``` connect kit to id
- ```kit(id)``` return kit connected to id
- ```then(sequence, ...)``` return new sequence with passed sequences and patterns after pattern
- ```after(sequence, ...)``` return new sequence with passed sequences and patterns before pattern
- ```and(sequence, ...)``` return new sequence with passed sequences and patterns layered with pattern

### sequence

- ```constructor(sequence, ..., [params])``` the sequence constructor optionally takes any number of sequences and patterns as argument before the usual params
- ```playing``` boolean, current state of playback, can be changed to start or pause
- ```loop``` boolean, define if sequence should loop, defaults to false
- ```sequences``` an array of sequences, patterns or arrays of sequences and patterns
- ```bars``` number, length in bars, read-only
- ```then(sequence, ...)``` return new sequence with passed sequences and patterns after sequence
- ```after(sequence, ...)``` return new sequence with passed sequences and patterns before sequence
- ```and(sequence, ...)``` return new sequence with passed sequences and patterns layered with sequence

### channel

- ```transform``` function to be called after expanding position expressions into notes, called after ```note.transform```, can return ```false``` to not execute ```pattern.transform```
- ```add(note, note, ...)``` schedule note(s) to be played within context of channel
- ```connect(effect)``` route output signal to destination via effect or chain
- ```bypass``` boolean to bypass all effects, effect type string or array of strings to bypass specific effect types, defaults to false

### note

- ```transform``` function to be called after expanding position expressions into notes, called before ```channel.transform```, can return ```false``` to not execute ```channel.transform```
- ```start([time])``` start playback of note at (AudioContext) time or immediately
- ```stop([time])``` stop playback of note at (AudioContext) time or immediately
- ```connect(effect)``` route output signal to destination via effect or chain
- ```bypass``` boolean to bypass all effects, effect type string or array of strings to bypass specific effect types, defaults to false

### reverb

- ```wet``` number between 0 and 999, amount of wet signal, defaults to 30
- ```dry``` number between 0 and 999, amount of dry signal, defaults to 100
- ```time``` number, impulse time in seconds, defaults to 1
- ```decay``` number, drop off time in seconds, defaults to 3
- ```filter``` string, type of filter (highpass, lowpass, bandpass, lowshelf, highshelf, peaking, notch, allpass), defaults to highpass
- ```cutoff``` number, frequency where filter is applied, defaults to 2000
- ```reverse``` boolean, defaults to false
- ```bypass``` boolean, defaults to false

### delay

- ```wet``` number between 0 and 999, amount of wet signal, defaults to 50
- ```dry``` number between 0 and 999, amount of dry signal, defaults to 100
- ```sync``` boolean, sync delay with current tempo, defaults to false
- ```time``` number between 0.001 and 4, delay time in seconds (or beats if sync is true), defaults to 0.3
- ```feedback``` number between 0 and 999, amount of regeneration from processed signal, defaults to 50
- ```filter``` string, type of filter (highpass, lowpass, bandpass, lowshelf, highshelf, peaking, notch, allpass), defaults to highpass
- ```cutoff``` number, frequency where filter is applied, defaults to 2000
- ```bypass``` boolean, defaults to false

### compressor

- ```threshold``` number between -100 and 0, decibel value above which the compression will start taking effect, defaults to -12
- ```knee``` number between 0 and 40, decibel value representing the range above the threshold where the curve smoothly transitions to the compressed portion, defaults to 30
- ```ratio``` number between 0 and 20, amount of change in dB needed in input for 1 dB change in the output, defaults to 12
- ```attack``` number, seconds required to reduce the gain by 10 dB, defaults to 0
- ```release``` number, seconds required to increase the gain by 10 dB, defaults to 0.25
- ```gain``` number between 0 and 999, amount of gain to processed signal, defaults to 100
- ```bypass``` boolean, defaults to false

### overdrive

- ```wet``` number between 0 and 999, amount of wet signal, defaults to 50
- ```dry``` number between 0 and 999, amount of dry signal, defaults to 50
- ```preBand``` number between 0 and 100, amount of preband filtering, defaults to 50
- ```color``` number between 20 and 22050, frequency cutoff for preband filtering, defaults to 800
- ```postCut``` number between 20 and 22050, frequency cutoff for post filter, defaults to 3000
- ```gain``` number between 0 and 999, amount of gain to processed signal, defaults to 100
- ```bypass``` boolean, defaults to false

### filter

- based on  [BiquadFilterNode](https://developer.mozilla.org/en-US/docs/Web/API/BiquadFilterNode)
- ```wet``` number between 0 and 999, amount of wet signal, defaults to 50
- ```dry``` number between 0 and 999, amount of dry signal, defaults to 50
- ```shape``` string, type of filter (highpass, lowpass, bandpass, lowshelf, highshelf, peaking, notch, allpass), defaults to highpass
- ```frequency``` number between 20 and 22050, frequency at which to apply effect, defaults to 440
- ```q``` number between 0.001 and 100, controls the frequency band width or peak at cutoff, defaults to 1
- ```value``` number between -999 and 999, amount of gain to affected frequency band, defaults to 0
- ```gain``` number between 0 and 999, amount of gain to processed signal, defaults to 100
- ```bypass``` boolean, defaults to false

### chorus

- ```wet``` number between 0 and 999, amount of wet signal, defaults to 50
- ```dry``` number between 0 and 999, amount of dry signal, defaults to 50
- ```rate``` number between 0.01 and 99, defaults to 1.5
- ```feedback``` number between 0 and 999, defaults to 0.2
- ```delay``` number between 0 and 1, defaults to 0.005
- ```gain``` number between 0 and 999, amount of gain to processed signal, defaults to 100
- ```bypass``` boolean, defaults to false

### phaser

- ```wet``` number between 0 and 999, amount of wet signal, defaults to 50
- ```dry``` number between 0 and 999, amount of dry signal, defaults to 50
- ```rate``` number between 0.01 and 99, defaults to 1.5
- ```depth``` number between 0 and 1, defaults to 0.3
- ```feedback``` number between 0 and 999, defaults to 0.2
- ```stereoPhase``` number between 0 and 180, defaults to 45
- ```modulationFrequency``` number between 500 and 1500, defaults to 750
- ```gain``` number between 0 and 999, amount of gain to processed signal, defaults to 100
- ```bypass``` boolean, defaults to false

### ping pong delay

- ```wet``` number between 0 and 999, amount of wet signal, defaults to 50
- ```dry``` number between 0 and 999, amount of dry signal, defaults to 50
- ```feedback``` number between 0 and 1, defaults to 0.2
- ```left``` number between 0.001 and 10, left channel delay in seconds, defaults to 0.15
- ```right``` number between 0.001 and 10, right channel delay in seconds, defaults to 0.2
- ```gain``` number between 0 and 999, amount of gain to processed signal, defaults to 100
- ```bypass``` boolean, defaults to false

## Feedback and issues

- Report bugs with [Github Issues](https://github.com/adamrenklint/bap/issues)
- Ask questions on [Gitter](https://gitter.im/adamrenklint/bap)
- Send shoutouts on [Twitter](http://twitter.com/adamrenklint)
- Or just [email me](mailto:adam@renklint.com)

## Develop

- ```npm install``` install all dependencies
- ```npm start``` run examples development server
- ```npm test``` run tests
- ```npm run test:watch``` run and watch tests
- ```npm run coverage``` generate coverage report with Istanbul
- ```npm publish``` run tests, publish to npm, build minified version, tag and deploy examples

## Contribute

- Check the [roadmap](https://github.com/adamrenklint/bap/blob/master/roadmap.md) and [open issues](https://github.com/adamrenklint/bap/issues)
- Fork the repo
- Add tests and/or examples
- Submit a pull request

## Props

- [Roger Linn](http://en.wikipedia.org/wiki/Roger_Linn) for making [MPC60](http://en.wikipedia.org/wiki/Akai_MPC60)
- [Marley Marl](http://en.wikipedia.org/wiki/Marley_Marl) for innovating the art of sampling
- [DJ Premier](http://en.wikipedia.org/wiki/DJ_Premier), [J Dilla](http://en.wikipedia.org/wiki/J_Dilla), [Pete Rock](http://en.wikipedia.org/wiki/Pete_Rock), [Damu the Fudgemunk](http://en.wikipedia.org/wiki/Damu_the_Fudgemunk), [Black Milk](http://en.wikipedia.org/wiki/Black_Milk), [Large Professor](http://en.wikipedia.org/wiki/Large_Professor) and [Apollo Brown](https://apollobrown.bandcamp.com) for making dope beats to be inspired from
- [Matt McKegg](https://twitter.com/MattMcKegg) for building [Bopper](https://github.com/mmckegg/bopper) and [Ditty](https://github.com/mmckegg/ditty), which are used in this project since version 0.1

## License

MIT © 2015 [Adam Renklint](http://adamrenklint.com)

---
*Generated with [redok](https://github.com/adamrenklint/redok) @ Monday November 30th, 2015 - 9:44:20 PM*