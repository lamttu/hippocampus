No matter how green field the team claims to be, it will still be new to you. You always have to pick up new, complex code.

## 1. Gain domain knowledge

Non-code knowledge can help you understand the problem the program is trying to solve, and why the software was built this way.

**Where to pick up knowledge:** anyone in the team, BA, designers, QA

## 2. Pull requests, commit history
Look at the historical commits for the class you're looking at to see what's happened before.

## 3. Trace the flow
Where does the client's request get resolved? Where is this parameter passed in?

## 4. Debug your application
Example: How to understand a replication program:

Value changes in db -> Producer pulishes the change in db -> Change is stored in the stream -> Consumer picks up change and process it

Each of these steps is a vertical slice. Debug a vertical slice to understand more about the process

- How does the producer know about the change?
- What kind of changes does the producer care about?
- How does it retrieve the data?
- What model is being used to pass the data around?

1. Find your vertical slice
2. Chuck in a breakpoint
3. Ask yourself questions about
   - How it teaks data from the previous layer
   - What it changes in this layer
   - How it sends it to the next layer
4. Find out where it's tested

Flowchart your stuff! Don't get bogged down to the right uml or anything. You just need a mental model of what's happening

![](img/flowchartEx.png)

Be mindful about how these things interact with one another, their dependencies, data being transformed and passed around

## 5. Find help
`git blame` to find the person to ask for help

## 6. Get your hands dirty
Find a small, self-contained ticket to get your feet wet

## 7. Write unit tests!
Write a test to confirm your hypothesis. 

## 8. Pay it forward to the next batch
- Clean up the code
- Note down how you tackle the problems (did you have to download an obscure version of something?)