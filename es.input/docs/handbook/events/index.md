# Events

* Install the appropriate `reactiveui-events-*` package into your application.
* You can use the events package standlone, without any references to ReactiveUI. `reactiveui-events-*` will always be a seperate package that has no dependancy on  the`reactiveui` package.
* This package looks at all the `EventHandlers` for a platform and generates  `Observable.FromEventPattern` extension methods via this [Moustache template](https://github.com/reactiveui/ReactiveUI/blob/develop/src/EventBuilder/DefaultTemplate.mustache).
* Don't use `EventHandlers` ever, use the generated `Observable.FromEventPattern` versions.
* Combine multiple `Observable.FromEventPattern`together to get amazing composition.
* Remember to [dispose of your subscriptions](https://reactiveui.net/docs/concepts/reactive-programming/subscriptions#lifecycle) using the features provided by the Reactive Extensions.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">😍Long ago &#39;events&#39; was my major contrib to <a href="https://twitter.com/ReactiveXUI">@ReactiveXUI</a> <a href="https://t.co/adc181AveS">https://t.co/adc181AveS</a>. I&#39;ve almost forgotten how to use bare metal C# events 😂</p>&mdash; brad phelan (@bradgonesurfing) <a href="https://twitter.com/bradgonesurfing/status/895580815500886017">August 10, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none" data-lang="en"><p lang="en" dir="ltr">Events remove crappy noise ceremonial: not only intent literally surfaces but it spares useless verbose protocol. There is no going back!</p>&mdash; Aku Studio (@Aku_Studio) <a href="https://twitter.com/Aku_Studio/status/895918988412190722">August 11, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

```csharp
var codes = new[]
{
    Key.Up,
    Key.Up,
    Key.Down,
    Key.Down,
    Key.Left,
    Key.Right,
    Key.Left,
    Key.Right,
    Key.A,
    Key.B
};

// convert the array into an sequence
var koanmi = codes
    .ToObservable();

this.Events().KeyUp

    // we want the keycode
    .Select(x => x.Key)
    .Do(key => Debug.WriteLine($"{key} was pressed."))

    // get the last ten keys
    .Window(10)

    // compare to known konami code sequence
    .SelectMany(x => x.SequenceEqual(koanmi))
    .Do(isMatch => Debug.WriteLine(isMatch))

    // where we match
    .Where(x => x)
    .Do(x => Debug.WriteLine("Konami sequence"))
    .Subscribe(y => { });
```


<iframe width="1280" height="720" src="https://www.youtube.com/embed/tNn-7fen3DA" frameborder="0" allowfullscreen></iframe>

[Source-code for this meetup talk](https://github.com/reactiveui/meetups/blob/master/002%20-%20reactiveui-events%20-%20the%20super%20cool%20package.zip)

# See Also
* http://www.introtorx.com/content/v1.0.10621.0/04_CreatingObservableSequences.html#FromEvent
