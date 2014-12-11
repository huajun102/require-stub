# Broswer `require()` for testing purposes <a href="http://unlicense.org/UNLICENSE"><img src="http://upload.wikimedia.org/wikipedia/commons/6/62/PD-icon.svg" width="20"/></a>

While development it is very annoying to browserify code `--standalone` in order just to launch tests in browser. Either running `watchify` task for getting `test.html` — it [tends to break](https://github.com/substack/watchify/issues/83) and it always has a lag of compilation. Also all these sourceMaps — a separate bunch of data increasing the size of files and which can always [break debugging](https://github.com/visionmedia/mocha/issues/550).

I want just to doubleclick the `test.html`, or launch `http-server` in the project’s folder, and develop via tests as I used to when I didn’t know about node modules.

So here is a script to support `require` calls synchronously in-browser.


```shell
$ npm install require-stub
```

```html
<!-- provide `reqiure` -->
<script src="node_modules/require-stub/index.js"></script>

<!-- use require -->
<script>
	var chai = require("chai");
	var enot = require("enot");

	//request from package.json
	var other = require("other-module");
</script>
```

If you find something isn’t working, report a [bug](https://github.com/dfcreative/require-stub/issues).



###### How does it work?

Via getters/setters on global `module.exports` and `exports` variables. Synchronous XMLHttpRequest is used to load scripts. To resolve module paths is used `package.json`, if present, and if not - path is guessed.
Required scripts are evaled, so to provide module scopes.


# Precautions

* Don’t require stuff runtime: it is bad for performance, it causes tons of logs and it badly resolves deps, as far current script is known only during the initial run.
* If something causes recursion — clear session storage.
* Don’t use in production: dynamic evals proved to be ~3x slower than browserified code. Though it is good for perf testing.
* Avoid global variable name the same as any inner module name if it’s value isn’t the module itself.
* If you decide to declare modules as script tags - variable names conflict is unavoidable, so name vars differently.


Best wishes,

Deema.



[![NPM](https://nodei.co/npm/require-stub.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/require-stub/)