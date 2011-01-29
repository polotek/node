## process

The `process` object is a global object and can be accessed from anywhere.
It is an instance of `EventEmitter`.


### Event: exit

Emitted when the process is about to exit.  This is a good hook to perform
constant time checks of the module's state (like for unit tests).  The main
event loop will no longer be run after the 'exit' callback finishes, so
timers may not be scheduled.

Example of listening for `exit`:

    process.on('exit', function () {
      process.nextTick(function () {
       console.log('This will not run');
      });
      console.log('About to exit.');
    });

### Event: uncaughtException

Param: err {Error} The exception that trigger this event.

Emitted when an exception bubbles all the way back to the event loop. If a
listener is added for this exception, the default action &mdash; which is to print a stack trace and exit &mdash; will not occur.

Example of listening for an uncaughtException:

    process.on('uncaughtException', function (err) {
      console.log('Caught exception: ' + err);
    });

    setTimeout(function () {
      console.log('This will still run.');
    }, 500);

    // Intentionally cause an exception, but don't catch it.
    nonexistentFunc();
    console.log('This will not run.');

Note that `uncaughtException` is a very crude mechanism for exception
handling.  Using try / catch in your program will give you more control over
your program's flow.  Especially for server programs that are designed to
stay running forever, `uncaughtException` can be a useful safety mechanism.


### Signal Events

Emitted when the processes receives a signal. See [signal(7)](http://kernel.org/doc/man-pages/online/pages/man7/signal.7.html) for a list of standard POSIX signal names.

Example of listening for `SIGINT`:

    // Start reading from stdin so we don't exit.
    process.stdin.resume();

    process.on('SIGINT', function () {
      console.log('Got SIGINT.  Press Control-D to exit.');
    });

An easy way to send the `SIGINT` signal is with `Control-C` in most terminal
programs.


### process.stdout

A `Writable Stream` to `stdout`.

Example: the definition of `console.log`

    console.log = function (d) {
      process.stdout.write(d + '\n');
    };


### process.stdin

A `Readable Stream` for stdin. The stdin stream is paused by default, so one
must call `process.stdin.resume()` to read from it.

Example of opening standard input and listening for both events:

    process.stdin.resume();
    process.stdin.setEncoding('utf8');

    process.stdin.on('data', function (chunk) {
      process.stdout.write('data: ' + chunk);
    });

    process.stdin.on('end', function () {
      process.stdout.write('end');
    });


### process.argv

An array containing the command line arguments.  The first element will be
'node', the second element will be the name of the JavaScript file.  The
next elements will be any additional command line arguments.

    // print process.argv
    process.argv.forEach(function (val, index, array) {
      console.log(index + ': ' + val);
    });

This will generate:

    $ node process-2.js one two=three four
    0: node
    1: /Users/mjr/work/node/process-2.js
    2: one
    3: two=three
    4: four


### process.execPath

This is the absolute pathname of the executable that started the process.

Example:

    /usr/local/bin/node


### process.chdir(directory)

Param: directory {String} The directory path to change to.

Changes the current working directory of the process or throws an exception if that fails.

    console.log('Starting directory: ' + process.cwd());
    try {
      process.chdir('/tmp');
      console.log('New directory: ' + process.cwd());
    }
    catch (err) {
      console.log('chdir: ' + err);
    }



### process.cwd()

Returns the current working directory of the process.

    console.log('Current directory: ' + process.cwd());


### process.env

An object containing the user environment. See environ(7).


### process.exit([code])

Param: code {Integer} The optional exit code to exit with.

Ends the process with the code of `code`. If the `code` argument is omitted, the value of `0` (success) will be used.

To exit with a 'failure' code:

    process.exit(1);

The shell that executed node should see the exit code as 1.


### process.getgid()

Gets the group identity of the process, see [getgid(2)](http://kernel.org/doc/man-pages/online/pages/man2/getgid.2.html)
This is the numerical group id, not the group name.

    console.log('Current gid: ' + process.getgid());


### process.setgid(id)

Param: id {Integer|String} The numerical group ID or groupname string.

Sets the group identity of the process, see [setgid(2)](http://kernel.org/doc/man-pages/online/pages/man2/setgid.2.html). If a groupname is specified for `id`, this method blocks while resolving it to a numerical ID.

    console.log('Current gid: ' + process.getgid());
    try {
      process.setgid(501);
      console.log('New gid: ' + process.getgid());
    }
    catch (err) {
      console.log('Failed to set gid: ' + err);
    }


### process.getuid()

Gets the user identity of the process, see [getuid(2)](http://kernel.org/doc/man-pages/online/pages/man2/getuid.2.html)
This is the numerical user ID, not the username.

    console.log('Current uid: ' + process.getuid());


### process.setuid(id)

Param: id {Integer|String} The numerical user ID or username string.

Sets the user identity of the process, see [setuid(2)](http://kernel.org/doc/man-pages/online/pages/man2/setuid.2.html). This accepts either a numerical ID or a username string. If a username is specified, this method blocks while resolving it to a numerical ID.

    console.log('Current uid: ' + process.getuid());
    try {
      process.setuid(501);
      console.log('New uid: ' + process.getuid());
    }
    catch (err) {
      console.log('Failed to set uid: ' + err);
    }


### process.version

A compiled-in property that exposes `NODE_VERSION`.

    console.log('Version: ' + process.version);

### process.installPrefix

A compiled-in property that exposes `NODE_PREFIX`.

    console.log('Prefix: ' + process.installPrefix);


### process.kill(pid, [signal])

Param: pid {Integer} The process id.
Param: signal {String} The optional string of the signal to send.

Send a signal to a process, see [kill(2)](http://kernel.org/doc/man-pages/online/pages/man2/kill.2.html) for more information. If the `signal` argument is omitted, the value of `'SIGTERM'` will be used.

Note that just because the name of this function is `process.kill`, it is
really just a signal sender, like the `kill` system call.  The signal sent
may do something other than kill the target process.

Example of sending a signal to yourself:

    process.on('SIGHUP', function () {
      console.log('Got SIGHUP signal.');
    });

    setTimeout(function () {
      console.log('Exiting.');
      process.exit(0);
    }, 100);

    process.kill(process.pid, 'SIGHUP');


### process.pid

The PID of the process.

    console.log('This process is pid ' + process.pid);

### process.title

Getter/setter to set what is displayed in 'ps'.


### process.platform

What platform you're running on. `'linux2'`, `'darwin'`, etc.

    console.log('This platform is ' + process.platform);


### process.memoryUsage()

Returns an object describing the memory usage of the Node process.

    var util = require('util');

    console.log(util.inspect(process.memoryUsage()));

This will generate:

    {
			rss: 4935680,
      vsize: 41893888,
      heapTotal: 1826816,
      heapUsed: 650472
		}

`heapTotal` and `heapUsed` refer to V8's memory usage.


### process.nextTick(callback)

Param: callback {Function()} The function to call on nextTick.


On the next loop around the event loop call this callback.
This is *not* a simple alias to `setTimeout(fn, 0)`, it's much more
efficient.

    process.nextTick(function () {
      console.log('nextTick callback');
    });


### process.umask([mask])

Param: mask {Integer} The optional new mask to set.

Sets or reads the process's file mode creation mask, see [umask(2)](http://kernel.org/doc/man-pages/online/pages/man2/umask.2.html) for more information. Child processes inherit their mask from their parent process. Returns the old mask if `mask` argument is given, otherwise returns the current mask.

    var oldmask, newmask = 0644;

    oldmask = process.umask(newmask);
    console.log('Changed umask from: ' + oldmask.toString(8) +
                ' to ' + newmask.toString(8));

