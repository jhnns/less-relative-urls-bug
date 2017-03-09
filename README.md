# less-relative-urls-bug

This repository demonstrates a bug in [Less](https://github.com/less/less.js) when the content is passed via stdin and the `--relative-urls` option is used.

---

Run `npm run error` to reproduce the problem:

```bash
> less-relative-urls-bug@1.0.0 error /Users/jhnns/dev/temp/less-relative-urls-bug
> cd ./a/a && ../../node_modules/.bin/lessc -ru - < a.less

.b {
  background-image: url("b/b/test.jpg");
}
```

The expected output would be:

```bash
.b {
  background-image: url("../../b/b/test.jpg");
}
```

---

When the folder is nested only one level deep, however, the output is correct. See `npm run ok`:

```bash
> less-relative-urls-bug@1.0.0 ok /Users/jhnns/dev/temp/less-relative-urls-bug
> cd ./a && ../node_modules/.bin/lessc -ru - < a.less

.b {
  background-image: url("../b/b/test.jpg");
}
```

---

The problem is [somewhere along these lines](https://github.com/less/less.js/blob/43ab0b872248ec34186bd8c245904d9a0f5d7825/lib/less/import-manager.js#L89-L97) and can be reproduced with less `2.x` and `less@3.0.0-alpha.1`. `alpha.2` is throwing an error:

```
TypeError: Cannot read property 'then' of undefined
    at parseLessFile (/Users/jhnns/dev/temp/less-relative-urls-bug/node_modules/less/bin/lessc:287:13)
    at ReadStream.<anonymous> (/Users/jhnns/dev/temp/less-relative-urls-bug/node_modules/less/bin/lessc:318:13)
    at emitNone (events.js:91:20)
    at ReadStream.emit (events.js:188:7)
    at endReadableNT (_stream_readable.js:974:12)
    at _combinedTickCallback (internal/process/next_tick.js:80:11)
    at process._tickCallback (internal/process/next_tick.js:104:9)
```