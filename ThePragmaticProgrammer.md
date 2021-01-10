# The pragmatic programmer

Continuous small improvements every day

## 1. You have agency

If your work environment suck and your job is boring, TRY TO FIX IT YOURSELF. But if that doesn't work out, go to a different company.

Make time to study new stuff that looks interesting.

Be proactive and take the opportunities

## 2. Provide options, don't make lame excuses

Team trust is important. You should be able to trust and rely on your team and vice versa.

When you _do_ agree to take on some responsibilities, hold yourself accountable for them.

Don't say something can't be done, explain what _can_ be done to salvage the situtation.

## 3. Software entropy

Fix software rot as soon as it's discovered. If there's no time, do something to show that you're on top of the situation (comment out the code, sub dummy data, etc.)

Neglect _accelerates_ software rot.

### Remember the big picture

Software entropy happens gradually, so you might not notice it. Constantly review what's happening around you, not just what you're personally doing.

## 4. Be a catalyst for change

When you're in a situation where you know what needs to be done but it's hard to get approval to start a project (time, budget, etc.), work out what you _can_ reasonably ask for. 

For example, a few days to work on a concept, develop it well, then show people. Wait for them to ask for the functionality you originally wanted.

## 5. Good enough software

Involve the user in the trade off

Make quality a requirements (users would rather use software with rough edges than wait for a year)

Don't spoil a perfectly good program by overembellishment and overrefinement

## 6. Invest regularly on your knowledge portfolio

Manage this portfolio like you want to FIRE

- **Invest regularly**

Use a consistent time and place

Set up goals

    - Learn at least one new language every year
    - Read a technical book each month
    - Read non-technical books too
    - Take classes
    - Participate in local meetups and events
    - Experiment with envs (try Linux if you've only worked w Win)
    - Stay current

- **Diversify**

The more tech you're comfortable with, the better you'll be able to adjust to change. Don't forget the non-technical skills

- **Manage risk**

Don't put all your technical eggs in one basket

- **Review and balance**

### Opportunities for learning

If you don't know something, find the answer, ask around, find out who can answer the question.

### Critical thinking

Analyse what you read and hear.
Ask why at least 5 times to dig deeper
Who does this benefit?
Find out the context (the prerequisites, the consequences, short and long term)
What will happen next? What wil happen after that?
Why is this a problem?

## 7. Communicate

- Understand the needs, interests, and capabilities of your audience
- Plan what you want to say, write an outline, ask yourself if it commmunicates what you want to say. Refine
- Ask youself: is this a good time to talk about ...?
- Choose the style of delivery that suits the audience
- Make it look good
- Get your audience's feedback early
- Get back to people
- Document everything  

## 8. Good design is easier to change than bad design

_Did the thing I just did make the system easier or harder to change?_

If you don't know the answer to that question:
- Try to make what you write replaceable.
- Note down the situation so that later when you need to change the code, you will be able to look back

## 9. DRY

It's not just about copy-paste code but about duplicate knowledge.

If you want ot chagne something and find yourself having to make changes in diffrent places and possibly in different formats (code, database schema,structure etc.), your code isn't DRY

Not all code duplication is knowledge duplication

Example:

DRY violation in data:

```
class​ Line {
​ 	  Point  start;
​ 	  Point  end;
​ 	  ​double​ length;
​ 	};
```

There's a duplication here: the length is defined by start and end points. Change 1 point and the length changes. It's better to make length a calculated field

```
class​ Line {
​ 	  Point  start;
​ 	  Point  end;
​ 	  ​double​ length { ​return​ start.distanceTo(end); }
​ 	};
```

Later on, you might want to set length as a field to cache the value and avoid expensive computation. They trick is to not expose this to the outside world.

```
​ 	​class​ Line {
​ 	  ​private​ ​double​ length;
​ 	  ​private​ Point  start;
​ 	  ​private​ Point  end;
​ 	
​ 	  ​public​ Line(Point start, Point end) {
​ 	    ​this​.start = start;
​ 	    ​this​.end   = end;
​ 	    calculateLength();
​ 	  }
​ 	
​ 	  ​// public​
​ 	  ​void​ setStart(Point p) { ​this​.start = p; calculateLength(); }
​ 	  ​void​ setEnd(Point p)   { ​this​.end   = p; calculateLength(); }
​ 	
​ 	  Point getStart()       { ​return​ start; }
​ 	  Point getEnd()         { ​return​ end;   }
​ 	
​ 	  ​double​ getLength()     { ​return​ length; }
​ 	
​ 	  ​private​ ​void​ calculateLength() {
​ 	    ​this​.length  = start.distanceTo(end);
​ 	  }
​ 	};
```

### Representational duplication

Your code interfaces with other libs (APIs), external sources, etc. The duplicaito nhere is that two things (your code and the external entity) have knowledge of the representation of the interface(schema, error code). Change one and it breaks the other.

Strategies to mitigate:

1. Look for tools that specifies the APi in a neutral format (mock APIs, functional tests, etc.)
2. Public API are documented using OpenAPI. Import the API spec into the local API tools ato integrate more reliably. If you can't find one, consider creating one and publishing it

## 10. Orthogonality - Decoupling

Eliminate effects between two unrelated things

Ask yourself _If I dramatically change the requirements behind a particular function, how many modules are affected?_

Avoid global data

Avoid similar functions: duplicate code is a symption of structural problems. (look at the Strategy pattern for a better implementation)

## 11. Reversibility

Keep your code flexible, as well as, architecture, deployment, and vendor integration

## 12. End to end system

Look for the important requirements, the ones that define the system, where you have doubts, the biggest risks. Code them first.

Do the MVP

## 13. Prototype to learn

Prototype anything that carries risks.

Protoypes gloss over details and focus in on specific aspects of the system

Make it clear that the prototype won't be productionize

## 14. Domain languages

## 15. Estimate to avoid surprises

First question to ask yourself: do they need high accuracy, or just a ballpark figure?

Choose the units that reflects what you want to convey (People will expect an estimate of 130 days to be accurate, whereas an estimate of 6 months can either be 5 or 7 months)

Think about the scope: _Assuming no traffic, I'll arrive in 20'_

Build a model of the system - Often it can also lads to discoveries of underlying patterns and processes. 

Break the model into components - Discover how the components interact

Record your estimates so you can see how close you were

Iterate the Schedule with the Code

**Estimate project schedules**

Give a range of scenarios

- Optimistic
- Most likely
- Pessimistic

## 16. Keep knowledge understandable to humans


```
<SOCIAL-SECURITY-NO>123-45-6789</SOCIAL-SECURITY-NO>
```

is better than

```
<FIELD10>123-45-6789</FIELD10>
```

or 

```
AC27123456789B11P
```

## 17. Shell games

Master your shell

- Simplify your workflow by aliases and functions

` alias apt-up='sudo apt-get update && sudo apt-get upgrade'`

## 18.  Power editing

Keyboard shortcuts cheatsheet in vscode: Ctrl K + Ctrl S

**Challenges**

- When editing text, move and make selections by character,word, line, and paragraph.
- When editing code, move by various syntactic units (matchingdelimiters, functions, modules, …).
- Reindent code following changes.Comment and uncomment blocks of code with a single command.
- Undo and redo changes.Split the editor window into multiple panels, and navigate between them.
- Navigate to a particular line number.
- Sort selected lines.
- Search for both strings and regular expressions, and repeat previous searches.
- Temporarily create multiple cursors based on a selection or on a pattern match, and edit the text at each in parallel.
- Display compilation errors in the current project.
- Run the current project’s tests.

No one is expecting you to know all of this. Pick and learn the ones that will make your life easier.

Ditch the mouse for a week and only edit with your keyboard. Note down what shortcuts you learn by pen and patper. Use repetition.

## 19. Use version control

Always use it

## 20. Debugging

Fix the problem. It doesn't matter if the bug is your fault, or someone else's. It's still your problem.

**A debugging mindset**

- Don't panic.
- Resist the urge to fix the symptoms. It's more likely that the actual fault is something else, and may involve a number of other related things.
- Always try to discover the root cause of the problem
- Rubber ducking
- Process of elimination
- Don't assume anything. Prove it. 
- When you come across a surprise bug, beyond merely fixing it, you need to determine why this failure wasn't caught earlier. Consider whether you need to amend the unit or the other tests so that they would have caught it.
- Are there any other places that might be susceptible to the same bug?


**Where to start**

- Before starting to look at the bug, make sure you are woking on code that built cleanly
- Reproduce bugs
- Fail test before fixing code 
- Jot down what you find

**Binary chop**

The code working a week ago suddenly fails and you want to find the commit that cause the error.

- Create a test that fails the current version
- Choose a half point between the current release and the last working version, run the tests. 
- Continue on until you can narrow it down.

## 21. Text manipulation

Learn text manipulation: `sed` and `awk` are powerful. Python and Ruby are also good candidates.

## 22. Engineering daybooks

Try journaling on what you're working on. Leave reminders, etc. Try using paper and not a file.