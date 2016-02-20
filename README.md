todo
====

A simple command line todo list manager which can be as powerful as you want it do be.

	$ todo -a "Fix the stuff"
	$ todo
	    1 | Fix the stuff
	$ todo -a "Fix the other thing"
	$ todo
	    1 | Fix the stuff
	    2 | Fix the other thing
	$ todo -d 1
	$ todo
	    2 | Fix the other thing


## Installation

Install the `todocli` package for Python 3 via pip.


## Basic usage

Add a task to do using the `-a` or `--add` option:

	$ todo -a "Do the thing"

See what you have to do by passing no option:

	$ todo
	    1 | Do the thing

The tasks shown first are the ones you entered first:

	$ todo -a "Fix the stuff"
	$ todo
	    1 | Do the thing
	    2 | Fix the stuff

Set a task as done using the `-d` or `--done` option, with the task's ID as value:

	$ todo -d 1
	$ todo
	    2 | Fix the stuff


## Deadlines

Set a deadline to a task using the `--deadline` option. The value is a date in the YYYY-MM-DD format:

	$ todo -a "Buy the gift for Stefany" --deadline 2016-02-25
	$ todo
	    3 | Buy the gift for Stefany ⌛ 16 days remaining
	    2 | Fix the stuff

Tasks with a deadline show up before tasks with no deadline, and tasks with closer deadlines are shown first. In the following example, a deadline is set using a delay instead of a date. Delays are specified using the letters `s`, `m`, `h`, `d`, `w` which respectively correspond to seconds, minutes, hours, days and weeks.

	$ todo -a "Send the documents for the house" --deadline 1w
	$ todo
	    4 | Send the documents for the house ⌛ 6 days remaining
	    3 | Buy the gift for Stefany ⌛ 16 days remaining
	    2 | Fix the stuff

### Long-term scheduling

Let's schedule the buying of a fireworks package for the 4th of July:

	$ todo -a "Buy the fireworks package" --deadline 2016-07-04
	$ todo 
	    4 | Send the documents for the house ⌛ 6 days remaining
	    3 | Buy the gift for Stefany ⌛ 16 days remaining
	    5 | Buy the fireworks package ⌛ 146 days remaining
	    2 | Fix the stuff

Since it's a long-term task, you probably don't want it to be shown in the middle of tasks that need to be done quickly.

You can use the `-s` or `--start` option in order to set a start-point to the task. The task will show up in the todo list only starting from the start-point. The value of `-s` is in the same format than for `--deadline`: a date or a delay. Let's say we want to be bothered by the fireworks thing starting from the middle of June.

	$ todo -t 5 -s 2016-06-15
	$ todo
	    4 | Send the documents for the house ⌛ 6 days remaining
	    3 | Buy the gift for Stefany ⌛ 16 days remaining
	    2 | Fix the stuff

The task won't show up until 2016-06-15.

In this example, we used the `-t` (`--task`) option to select a task to apply a modifier to. All the modifiers available when creating a task with the `-a` option are also available when selecting a task with the `-t` option.

## Priority

You can assign a priority to a task using the `-p` or `--priority` option. Priorities are integers; the higher the integer, the higher the priority. Tasks with a higher priority are shown first. By default, tasks have a priority of 1.

	$ todo -a "Fix the window" -p 3
	$ todo
	    6 | Fix the window ★3
	    4 | Send the documents for the house ⌛ 6 days remaining
	    3 | Buy the gift for Stefany ⌛ 16 days remaining
	    2 | Fix the stuff
	$ todo -t 2 -p 2
	$ todo
	    6 | Fix the window ★3
	    2 | Fix the stuff ★2
	    4 | Send the documents for the house ⌛ 6 days remaining
	    3 | Buy the gift for Stefany ⌛ 16 days remaining

## Contexts

You can assign a context to a task using the `-c` or `--context` option. Contexts are strings.

	$ todo -a "Read the article about chemistry" -c culture
	$ todo
	    6 | Fix the window ★3
	    2 | Fix the stuff ★2
	    4 | Send the documents for the house ⌛ 6 days remaining
	    3 | Buy the gift for Stefany ⌛ 16 days remaining
	    7 | Read the article about chemistry #culture

When used alone, the `-c` option filters tasks according to their context:

	$ todo -c culture
	    7 | Read the article about chemistry #culture

### Subcontexts

You can define contexts within contexts using the dot notation:

	$ todo -t 7 -c culture.chemistry
	$ todo -c culture
	    7 | Read the article about chemistry #culture.chemistry
	$ todo -a "Listen to the podcast about movies" -c culture.cinema
	$ todo -c culture
	    7 | Read the article about chemistry #culture.chemistry
	    8 | Listen to the podcast about movies #culture.cinema
	$ todo -c culture.chemistry
	    7 | Read the article about chemistry #culture.chemistry

### Visibility

Tasks have a visibility which impacts on what tasks are listed when you use the `--context` option or its shorthand `-c`. There are three kinds of visibility: hidden, discreet or wide. The default is discreet.

 - hidden means that the task will show up only if its context is exactly the one given in the command line. So `hello.world.yeah` will only match `hello.world.yeah`.

 - discreet means that the task will show up only if its context is a subcontext of the one given in the command line. `hello.world.yeah` is a subcontext of `hello.world` and also a subcontext of `hello`. Discreet tasks which have a top-level context will also show up when no context is specified in the command line, such as `culture` in the previous examples.

 - wide is the same as discreet, but the task will always show up when no context is specified in the command line, even if the task doesn't have a top-level context.

For example, continuing on the previous example, if you show the general todolist:

	$ todo
	    6 | Fix the window ★3
	    2 | Fix the stuff ★2
	    4 | Send the documents for the house ⌛ 6 days remaining
	    3 | Buy the gift for Stefany ⌛ 11 days remaining

You realize that the cultural tasks have disappeared. That's because these tasks don't have a top-level context. They have sub-level contexts, namely `culture.chemistry` and `culture.movies`. Being discreet by default, these tasks will only show up when you query the todolist with a super-context of their context, `culture` in this case:

	$ todo -c culture
	    7 | Read the article about chemistry #culture.chemistry
	    8 | Listen to the podcast about movies #culture.cinema

If you really want them to be shown in the general todolist, you can set their visibility to "wide", using the `-v` or `--visibility` option:

	$ todo -t 7 -v wide
	$ todo -t 8 -v wide
	$ todo
	    6 | Fix the window ★3
	    2 | Fix the stuff ★2
	    4 | Send the documents for the house ⌛ 6 days remaining
	    3 | Buy the gift for Stefany ⌛ 11 days remaining
	    7 | Read the article about chemistry #culture.chemistry
	    8 | Listen to the podcast about movies #culture.cinema

On the opposite, if you want a task to appear only if you query the todolist with its exact context, you can set its visibility to "hidden".

	$ todo -t 7 -v hidden
	$ todo
	    6 | Fix the window ★3
	    2 | Fix the stuff ★2
	    4 | Send the documents for the house ⌛ 6 days remaining
	    3 | Buy the gift for Stefany ⌛ 11 days remaining
	    8 | Listen to the podcast about movies #culture.cinema
	$ todo -c culture
	    8 | Listen to the podcast about movies #culture.cinema
	$ todo -c culture.chemistry
	    7 | Read the article about chemistry #culture.chemistry

You can specify the visibility of a context. This visibility will apply to all the tasks belonging to the context (and to the future tasks belonging to the context):

	$ todo -c culture.cinema -v wide

This only applies to tasks which match the exact context, and this doesn't recurse to subcontexts.

### Context priority

You can specify a priority to a whole context:

	$ todo -c health -p 10

Tasks with this context will always show up before tasks with a lower context priority.

## Comments/Updates

Each task can be assigned a single comment as an indication of progress:

	$ todo -t 1 -u "I have done ____ in order to complete the task"

A blank argument to the -u switch will remove the comment. Comments are overwritten each time.

## Sort summary

When showing the todolist, tasks are sorted in the following order:

 - Priority, descending

 - Remaining time, ascending

 - Context priority, descending

 - Date added, ascending
