## An example using the `rxjs-spy` console API

`rxjs-spy` exposes a module API intended to be called from code and a console API intended for interactive use via the browser's console.

The documentation for the APIs is in the [GitHub README](https://github.com/cartant/rxjs-spy).

This page includes an example showing what can be done with its console API, so open up a DevTools console and give it a go.

## The example's source

Have a look at the this page's source:

```html
<script>

    rxSpy.spy();

</script>
<script>

    (function () {

        var interval = new Rx.Observable
            .interval(2000)
            .tag("interval");

        var people = interval
            .map((value) => {
                const names = ["alice", "bob"];
                return names[value % names.length];
            })
            .tag("people")
            .subscribe();
    })();

</script>
```

Note that there are two `script` elements. The first configures the spy and the second represents the application code that will be spied upon.

The observables in the second `script` element are enclosed in an IIFE, so they are not accessible from the console. However, they are tagged, so they can be inspected and manipulated using the spy's console API.

## The example

1. To inspect the current snapshots of all tagged observables, run the following in the console:

        rxSpy.show();

    You'll see a listing of snapshots for all tagged observables. There are two: an interval observable; and an observable that emits people's names.

    To inspect a specific tagged observable, you can pass a string or a regular expression to `show`. For example:

        rxSpy.show("people");

1. Logging can be enabled using the API's `log`method. For example:

        rxSpy.log("people");

    This will enable logging for the specified tag.

    Run the following to log the interval observable's values, too:

        rxSpy.log("interval");

1. Calls made to the spy API can be undone using the `undo` method. To obtain a list of calls, run the following:

        rxSpy.undo();

    It will display a list of numbered calls. Undo the interval logging by passing the appropriate call number:

        rxSpy.undo(3);

1. The spy API can modify the a tagged observable using the `let` method. The method behaves in a similar manner to the RxJS `let` operator.

    For example, the following call will replace the value emitted from the people observable:

        rxSpy.let("people", source => source.mapTo("mallory"));

1. The spy API can pause tagged observables using the `pause` method. For example, the following call pauses the people observable so that it's emissions can be controlled from the console:

        rxSpy.pause("interval");

    `pause` returns a `Deck` instance that can be used to control the observable and return value should be assigned to a variable. It's easy to forget to do this, so there is a `deck` method that will display a numbered list of `pause` calls. Passing a call number to `deck` will return the instance associated with the call. For example:

        rxSpy.deck();
        var deck = rxSpy.deck(1);

    The deck can be used to inspect and control the observable's emissions. For example, calling its `log` method will show the notifications that have been paused:

        deck.log();

    Its `step` method will release a single paused notification:

        deck.step();

    Its `skip` method will skip a single paused notification:

        deck.skip();

    And its `resume` method will release all paused notifications and will resume the observable's emissions:

        deck.resume();

    Calling its `pause` method will return the observable to a paused state:

        deck.pause();

1. All API calls that manipulate observables can be undone, so `undo` can be used to undo the pausing of an observable. When undone, any paused notifications are resumed:

        rxSpy.undo();
        rxSpy.undo(4);

<script src="https://unpkg.com/core-js/client/core.js"></script>
<script src="https://unpkg.com/rxjs/bundles/Rx.js"></script>
<script src="https://unpkg.com/rxjs-spy"></script>
<script>

    rxSpy.spy();

</script>
<script>

    (function () {

        var interval = new Rx.Observable
            .interval(2000)
            .tag("interval");

        var people = interval
            .map((value) => {
                const names = ["alice", "bob"];
                return names[value % names.length];
            })
            .tag("people")
            .subscribe();
    })();

</script>