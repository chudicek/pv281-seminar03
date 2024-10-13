# Todo app

original: <https://gitlab.fi.muni.cz/pv281/seminar-exercises/week-03-cli-todo-list>

Create a console application using `clap` that allows for task management.

Because of *reasons*, we will be using two sets of commands for this application.

- The first one is to configure how the application should start.
- The second one is to interact with the *running* application.

The application allows you to enter the switch `-n` or `--name`, which specifies the name of the task owner.

Upon startup, the application receives commands (always prompting with `>` before input).

---

## Starting Commands

There is just one starting command: `start` (with one optional parameter).

It is invoked as follows:

```shell
cargo run -- start
```

The optional parameter available under the `--name`, `-n` switch allows you to specify the name of who this session belongs to.

```shell
cargo run -- start --name "John Doe"
```

---

## Interactive Commands

Those are the commands the user can enter after the application has started.

>A prompt (`>>>`) is displayed before each command.

Use the following boilerplate to parse command from a loaded string:

```rust
/// Parses the command from the input line into the `YourInteractiveArgsStruct` struct.
/// 
/// Exits the application if the command is not recognized. The help message is printed in that case.
fn parse_to_interactive_args(line: &str) -> YourInteractiveArgsStruct {
    // assume that the command is exactly delimited by whitespace
    // you could use a more sophisticated parser here to split it more cli-like, eg `shlex`

    // need to bend the rules a bit to make this work - prepend the app name for the parser to work alright
    let line = "todo-app".to_string() + " " + line.trim();

    // parse the interactive command, exit print help and exit if it fails
    YourInteractiveArgsStruct::parse_from(line.split_whitespace())
}
```

```
exit		    terminates the application
add <name>	    creates a task, does not allow duplicates
remove <name>	removes a task (must exist)
list		    lists tasks
```

The format for listing tasks is:

```
<owner's name>'s TODOs
------------------------
<task1>
<task2>
...
```

> Optional: Ensure that the divider between the title and the tasks is exactly the length of the title.

---

## Extension - file handling

The application can be started with the switch `-f` or `--file`. If this file is specified, the output of the `list` command will be written to it after the `exit` command. If the file does not exist, it will be created.

## Extension - task status

Tasks can have the status `todo`, `in progress`, and `done`.

Each task is set as incomplete upon creation.

The `list` command will display tasks in the format:

```
<owner's name>'s TODOs
------------------------
[<status>] <task1>
[<status>] <task2>
...
```

The command `start <name>` changes the task status from `todo` to `in progress`.
The command `finish <name>` sets the task status to `done` (regardless of the previous status of the task).

## Extension - priority

Add the command `priority <name> <priority>`, which sets the priority for a task. Only tasks with the status `todo` can have priority (ensure type checking to enforce this).

Tasks are sorted in the output as follows:
1) All `todo` tasks by priority
2) All `in progress` tasks
3) All `done` tasks

## Extension - time tracking

The `start` command begins the timer for when work on a task starts. The `finish` command calculates the duration (from the start) and saves it to the respective task.

When listing tasks, completed tasks will also display the duration. The total time spent on tasks will be shown below the listings.

## Extension - anything else you can think of

Add any other feature you think would be useful for a task manager.
