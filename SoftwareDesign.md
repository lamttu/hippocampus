## 1. Keep notes
About everything.

## 2. Think of yourself as a software designer and not a code writer

Find opportunities to explore design concerns

`services.AddScoped<IHealthcheckDependency, RoutingDependency>();`

Possible design concerns to explore:
- API design
- Middleware pipeline design
  - Delegates
  - Async
  - Plug-in archi
- Dependency Injection lifetimes
- Generic methods as API
- Fluent interfaces

You don't need to do this in one go, just go a bit extra every time. Balance between this and delivering business value.

## 3. When learning, seek comprehensive real world examples

Example: Adapter Pattern

Don't stop at reading the concepts but seek out real examples to understand the subtleties

## 4. Low coupling, high cohesion

### High cohesion
The class knows about the job that it needs to do and nothing else

An example of poor cohesion:

```
class Template {
    Populate() {}
    Render() {}
    GetHistoryPageHtml(referenceId){
        // THIS DOESN'T BELONG HERE
    }
}
```

Maybe the devs need to have the history page in two different templates and they decide to put the method in the base class

## 5. Work the edges

- Find the edges (API) to understand a system
- Create good edges (good API)

1. Use your language/ library/ framework's idioms
2. Think of your fellow programmer who will read your code later
3. Avoid unnecessary interfaces

## 6. Design patterns as a communication tool

Think of them as a communication tool for other peeps to understand instead of a cookie cutter.

## 7. Find the tradeoff

Everything has tradeoffs. Find the tradeoffs (don't be blinded by shiny new things).

Ex: Is it worth spending more time to learn something? Or should we just take the easy way?

## 8. Embrace software's malleability
It's actually way easier to do it the waterfall way. But for the company, we have to adjust to our customers so we have to do it the Agile way.

- Teams try to do things the Agile way but still have an idea of what's going to come after.
- Skip doing something because something in the next sprint will fix it
- Putting in an interface for future use

This implies that we will pile on more stuff to the program.

Think of ourselves as gardeners instead of constructors. Plants die. Deletion should be part of your work as much as creation.

## 9. Automated testing as the key to success as scale
More unit tests! because they're deterministic and fast.
> For each desired change, make the change easy (warning: this may be hard), then make the easy change

## 10. KISS
It's hard to ***Keep*** it simple.

## Recommended reading
- Building Evolutionary Architacture -  Neal Ford, Rebecca Parsons & Patrick Kua
- Domain-Driven Design - Eric Evans