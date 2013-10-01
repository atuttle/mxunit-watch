# mxunit-watch

[![NPM version](https://badge.fury.io/js/mxunit-watch.png)](http://badge.fury.io/js/mxunit-watch)

Watch a directory for file changes. When a (.cfc, .cfm, .xml) file is changed, trigger an mxunit test suite run. Results displayed neatly in the console:

![mxunit-watch in action](https://raw.github.com/atuttle/mxunit-watch/master/screenshot.png)

## Pre-requisites

### Requires NodeJS

The guts of this tool are powered by Node, and you'll be installing it from NPM.

```bash
npm install -g mxunit-watch
```

### Requires MXUnit Bleeding-Edge Release

I had to [submit a small change to MXUnit](https://github.com/mxunit/mxunit/pull/33) to get it to offer up the list of tests, but they accepted my pull request, so it's now available in their Bleeding Edge Release. When another official release is published I'll update this readme with the minimum version number, but for now you should know that version 2.1.3 (the latest stable release) is _not recent enough_. You'll have to [download the latest master branch of mxunit](https://github.com/mxunit/mxunit/archive/master.zip).

### Add a CFM file to your project

mxunit-watch uses this file to get a listing of all of your test suites and the tests they contain:

```cfm
<cfscript>
	testsDir = expandPath('/your/project/tests');
	componentPath = 'your.project.tests';

	suite = createObject('mxunit.framework.TestSuite');
	files = createObject('mxunit.runner.DirectoryTestSuite').getTests(directory=testsDir, componentPath=componentPath);

	for (i = 1; i <= arrayLen(files); i++){
		suite.addAll(files[i]);
	}

	tests = suite.suites();

	for (t in tests){
		tests[t] = tests[t].methods;
	}
</cfscript>
<cfsetting showdebugoutput="false" />
<cfcontent reset="true" type="application/json; charset=UTF-8" /><cfoutput>#serializeJson(tests)#</cfoutput><cfabort/>
```

This file is provided in the mxunit-watch repository (test-list.cfm), or you can just copy it from above. Save it to a web-accessible location in your project and note its URL, you'll need that later.

## Let's go!

Now that you've installed mxunit-watch and saved your tests-list file. Let's auto-run some tests!

The CLI requires 2 arguments in order to run your tests:

* `-d` or `--dir` should be set to the directory that you want to watch
* `-l` or `--list` should be set to the URL of the test-list file you created


>     $ mxunit-watch -d /my/project -l http://localhost/my/project/tests/test-list.cfm
>     Watching for file changes. Get coding!

As the output says, it's now watching for changes. Any time you save a change to a `.cfm`, `.cfc`, or `.xml` file, your entire test suite will run.

## Other options

* `-H [hostname]` to set the hostname used to run tests
* `-h` for help
* `-V` for version
* `--debug` will print out some debug information at startup
