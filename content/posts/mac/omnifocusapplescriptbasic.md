---
title: "How to build your own apple script for omnifocus"
date: "2018-09-01"
description: "This blog tells you some basic usage for apple script  for omnifocus"
categories:
    - "Mac"
    - "GTD"
    - "Omnifocus"
---

# How to use apple script with Omnifocus

Omnifocus is a very great to-do list app in Mac and iPhone. It is expensive and very good to use. We could also use apple script to make it become more powerful.

## List all done tasks

In many cases, we need to do summary on how many tasks we have done that day or week. Therefore, we could just log out all completed tasks and send to notebook/Day one.

The general idea is to get all modified project and then get all tasks inside, if we saw any tasks with completion date during that day, we just log that tasks out. 

The code could see here:

```applescript
tell application "OmniFocus"
	tell front document
		-- Get the list of projects that were modified within thedate span
		set theProjects to every flattened project where its modification date is greater than startDate and modification date is less than endDate
		-- Loop through the matching project list
		repeat with a from 1 to length of theProjects
			-- Get the current project to work with
			set currentProj to item a of theProjects
			-- Get the tasks that were completed within the datespan
			set theTasks to (every flattened task of currentProj where its completed = true and completion date is greater than startDate and completion date is less than endDate)
			-- If there are tasks that meet the criteria
			if theTasks is not equal to {} then
				-- Add a dividing line and then enter the projectname
				set reportText to reportText & return & return & "# " & name of currentProj & return
				-- Loop through the tasks
				repeat with b from 1 to length of theTasks
					-- Get the current task to work with
					set currentTask to item b of theTasks
					-- Save the completed date to a variable
					set completedDate to completion date of currentTask
					-- Get the time of the completed date
					set completedTime to time string of completedDate
					-- Add the task to the report text with thecompleted time
					set reportText to reportText & return & " - " & name of currentTask & " ----- " & completedTime
				end repeat
			end if
		end repeat
    end tell -- end tell front document
end tell -- end tell application "OmniFocus"
```

## Add new template tasks

We could use `dialog` to input some common things, for example, if we want to read book, using following code could get all important elements.


```script
set bookTitle to text returned of (display dialog "What's the title of the book?" default answer "")
set numPages to text returned of (display dialog "How many pages are in the book?" default answer 0) as integer
set startDate to text returned of (display dialog "When should this start?" default answer "yyyymmdd")
set endDate to text returned of (display dialog "When should this end?" default answer "yyyymmdd")
```

Then we could perform some calculation and add tasks to omnifocus.

```script
tell application "OmniFocus"
	tell document of front window
        .....
        set newTask to make new inbox task with properties {name:taskTitle, defer date:deferDate, context:theContext}
    end tell
end tell
```



## Tips

1. if you want to learn more, there are some repo 
   - [OFscripts](https://github.com/joebuhlig/OFScripts)
   - [omnifocus-scripts](https://github.com/dbyler/omnifocus-scripts)

2. Open Apple script app, then choose open dictionary and open Omnifocus, you could see more commands.