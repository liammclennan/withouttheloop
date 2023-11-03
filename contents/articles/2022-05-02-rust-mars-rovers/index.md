---
title: Rust Programming Mars Rovers Exercise
author: Liam McLennan
date: 2022-05-02 20:32
template: article.jade
---

![Rust logo](rust.png)

I've been learning to program with Rust, and frankly struggling. I like the book [Programming Rust](https://www.goodreads.com/en/book/show/25550614) which I've been working through, but still I get stuck. Things in the book make sense, but when I try it I end up tied in a knot. I decided I need to write more rust and work through it. When learning a new programming language I find it helpful to revist programs that I know well in other languages. 

Seven years ago I was [playing with the Mars Rovers problem in F#](https://www.withouttheloop.com/articles/2015-04-17-mars-rover/), so today I tried it in Rust.

## Bits I Think Interesting

### Types

The types are neat, and similar to their F# equivalents. Enums for directions and commands and a tuple to represent cartesian coordinates. The tuple has a nice property (as opposed to `struct { x,y }`) that it is a copy type (a type copied by value therefore not subject to borrowing). 

```rust
enum Direction {
    NORTH,SOUTH,EAST,WEST
}

type Point = (i8,i8);

struct Position {
    location: Point,
    orientation: Direction
}

enum Command {
    LEFT, RIGHT, MOVE, REPORT
}
```

### Parsing

The first step of the program is to parse the input file into the starting position of the rover and a set of commands. The input file is:

```
PLACE 1,2,EAST
MOVE
MOVE
LEFT
MOVE
MOVE
MOVE
MOVE
MOVE
LEFT
MOVE
MOVE
MOVE
MOVE
MOVE
REPORT
```

The function that parses the input is:

```rust
fn parse_input(content: String) -> (Position, Vec<Command>) {
    let lines: Vec<&str> = content.lines().collect();
    (get_start_position(&lines), get_commands(&lines))
}
```

I preferred to return a tuple for the position and the list of commands rather than introducing a type just to hold this function return value, as the function is called and its result immediately consumed. 

Splitting the contents of the input file into lines could have been: 

```rust
let lines: Vec<&str> = content.lines().collect();
```

or 

```rust
let lines = content.lines().collect::<Vec<&str>>();
```

I prefer the version with the type annotation on the variable. 

Since `lines` is passed to two functions, neither of them may consume the value, so they both expect `&Vec<str>`. 

The code to parse the starting position (`PLACE 1,2,EAST`) is:

```rust
fn get_start_position(lines: &Vec<&str>) -> Position {
    let first_line: &str = lines[0];
    let args: Vec<&str> = first_line[6..].split(",").collect();
    Position {
        location: (args[0].parse().unwrap(), args[1].parse().unwrap()),
        orientation: args[2].parse().unwrap()
    }
}
```

Skip the `PLACE ` part, split on `,`, and build a `Position` struct. The interesting thing here is that `args[2]` is a `&str`, and `Position::orientation` is a `Direction` enum (see above). How does that work?

`parse` uses the `from_str` method of the `FromStr` trait. You can `parse` a string to anything that has this trait implemented, like so:

```rust
impl FromStr for Direction {
    type Err = String;
    fn from_str(input: &str) -> Result<Direction, Self::Err> {
        match input {
            "NORTH" => Ok(Direction::NORTH),
            "EAST" => Ok(Direction::EAST),
            "SOUTH" => Ok(Direction::SOUTH),
            "WEST" => Ok(Direction::WEST),
            _ => Err(format!("Unknown direction {}", input))
        }
    }
}
```

Of course, in C# this would be easier with [`Enum.Parse`](https://docs.microsoft.com/en-us/dotnet/api/system.enum.parse?view=net-6.0).

Parsing the commands is implemented by mapping the remaining lines of the input file, trimming each line, skipping the empty lines, and using the same `FromStr` / `parse` trick to turn each line into a `Command`. 

```rust
fn get_commands(lines: &Vec<&str>) -> Vec<Command> {
    lines[1..].iter().map(|line| {
        line.trim()
    }).filter(|line| {
        !line.is_empty()
    }).map(|line| {
        line.parse().unwrap()
    }).collect()
}
```

Rust's iterators can be a bit clumsy compared to the F# equivalent, but here it works nicely. Chaining transformations on an iterator works well so long as their is a materialized type on either end of the chain. 

### Methods on Types

In F# I tend to keep types and functions separate (in the same module), but in Rust I found it nice to implement the 'mutations' on the `Position` type, like so:

```rust
struct Position {
    location: Point,
    orientation: Direction
}
impl Position {
    fn turn_left(&self) -> Position {
        match self.orientation {
            Direction::NORTH => 
                Position { orientation: Direction::WEST, ..*self },
            Direction::EAST => 
                Position { orientation: Direction::NORTH, ..*self },
            Direction::SOUTH => 
                Position { orientation: Direction::EAST, ..*self },
            Direction::WEST => 
                Position { orientation: Direction::SOUTH, ..*self },
        }
    }

    fn turn_right(&self) -> Position {
        match self.orientation {
            Direction::NORTH => 
                Position { orientation: Direction::EAST, ..*self },
            Direction::EAST => 
                Position { orientation: Direction::SOUTH, ..*self },
            Direction::SOUTH => 
                Position { orientation: Direction::WEST, ..*self },
            Direction::WEST => 
                Position { orientation: Direction::NORTH, ..*self },
        }
    }

    fn move_forward(&self) -> Position {
        let delta: (i8,i8) = match self.orientation {
            Direction::NORTH => (0,1),
            Direction::EAST => (1,0),
            Direction::SOUTH => (0,1),
            Direction::WEST => (-1,0),
        };

        // restrict val to `low..=high`
        fn bound(low: i8, high: i8, val: i8) -> i8 {
            max(min(val, high), low)
        }

        let highest_index = GRID_SIZE - 1;

        Position {
            location: (
                bound(0, highest_index, self.location.0 + delta.0), 
                bound(0, highest_index, self.location.1 + delta.1)),
            ..*self
        }
    }
}
```

'Mutations' is quoted because each of the operations, `turn_left`, `turn_right` and `move_forward`, returns a new, owned, `Position` instead of mutating `self`. Creating the new `Postion` uses the 'struct update' syntax (`..*self`) to be robust to changes in the `Position` type and to make the code more clearly express its purpose (make a new value the same as the old one except this specific change). 

### Drawing the Rest of the Owl

The last part is to fold over the list of commands, applying them to the starting position. 

Here I chose to pass the starting position (`start: Position`) and the list of commands (`Vec<Command>`), by value such that they are consumed by the `apply_commands` function. It is the last statement in the program so I know those values are not needed and can be safely released. 

Rust allows blocks anywhere an expression is valid, which makes it easy to embed statements in expressions. By making the value of the `Command::REPORT` branch a block it is possible to perform a side-effect (`report(&position)`) and then return the current position value, to move to the next command. 

```rust
fn apply_commands(start: Position, commands: Vec<Command>) -> Position {
    commands.iter().fold(start, |position, command| {
        match command {
            Command::LEFT => position.turn_left(),
            Command::RIGHT => position.turn_right(),
            Command::MOVE => position.move_forward(),
            Command::REPORT => {
                report(&position);
                position
            }
        }
    })
}
```

## Conclusion

I am enjoying the exercise of Rust, because I find it difficult, but that does not make it a good programming language. 

At this stage I can't honestly say that I *like* Rust, but I really want to. Surely, all those [stack overflow respondents](https://insights.stackoverflow.com/survey/2021#section-most-loved-dreaded-and-wanted-programming-scripting-and-markup-languages) can't be wrong? My hope is that once I pass the frustration stage I will move into the blissful satisfaction stage. 

If I was coming from a lower-level language (C, assembler) no doubt Rust would seem amazing, but coming from higher-level languages there is a sense in which it is a step backwards - a concession to the constraints of computer hardware and an acknowledgement that technology is not yet sufficiently advanced to free programmers from a bunch of [accidental complexity](https://www.cgl.ucsf.edu/Outreach/pc204/NoSilverBullet.html). 

Entire program for reference:

```rust
use std::fs;
use std::str::FromStr;
use std::cmp::{min,max};

pub fn run() {
    let content = read_file("src/rovers/input.txt");
    let input = parse_input(content);
    apply_commands(input.0, input.1);
}

static GRID_SIZE: i8 = 5;

#[derive(Debug,PartialEq,Eq,Clone,Copy)]
enum Direction {
    NORTH,SOUTH,EAST,WEST
}
impl FromStr for Direction {
    type Err = String;
    fn from_str(input: &str) -> Result<Direction, Self::Err> {
        match input {
            "NORTH" => Ok(Direction::NORTH),
            "EAST" => Ok(Direction::EAST),
            "SOUTH" => Ok(Direction::SOUTH),
            "WEST" => Ok(Direction::WEST),
            _ => Err(format!("Unknown direction {}", input))
        }
    }
}

type Point = (i8,i8);

#[derive(Debug,PartialEq,Eq)]
struct Position {
    location: Point,
    orientation: Direction
}
impl Position {
    fn turn_left(&self) -> Position {
        match self.orientation {
            Direction::NORTH => 
                Position { orientation: Direction::WEST, ..*self },
            Direction::EAST => 
                Position { orientation: Direction::NORTH, ..*self },
            Direction::SOUTH => 
                Position { orientation: Direction::EAST, ..*self },
            Direction::WEST => 
                Position { orientation: Direction::SOUTH, ..*self },
        }
    }

    fn turn_right(&self) -> Position {
        match self.orientation {
            Direction::NORTH => 
                Position { orientation: Direction::EAST, ..*self },
            Direction::EAST => 
                Position { orientation: Direction::SOUTH, ..*self },
            Direction::SOUTH => 
                Position { orientation: Direction::WEST, ..*self },
            Direction::WEST => 
                Position { orientation: Direction::NORTH, ..*self },
        }
    }

    fn move_forward(&self) -> Position {
        let delta: (i8,i8) = match self.orientation {
            Direction::NORTH => (0,1),
            Direction::EAST => (1,0),
            Direction::SOUTH => (0,1),
            Direction::WEST => (-1,0),
        };

        // restrict val to `low..=high`
        fn bound(low: i8, high: i8, val: i8) -> i8 {
            max(min(val, high), low)
        }

        let highest_index = GRID_SIZE - 1;

        Position {
            location: (bound(0, highest_index, self.location.0 + delta.0), bound(0, highest_index, self.location.1 + delta.1)),
            ..*self
        }
    }
}

#[derive(Debug,PartialEq,Eq)]
enum Command {
    LEFT, RIGHT, MOVE, REPORT
}
impl FromStr for Command {
    type Err = String;
    fn from_str(input: &str) -> Result<Command, Self::Err> {
        match input {
            "LEFT" => Ok(Command::LEFT),
            "RIGHT" => Ok(Command::RIGHT),
            "MOVE" => Ok(Command::MOVE),
            "REPORT" => Ok(Command::REPORT),
            _ => Err(format!("Unknown command {}", input))
        }
    }
}

fn apply_commands(start: Position, commands: Vec<Command>) -> Position {
    commands.iter().fold(start, |position, command| {
        match command {
            Command::LEFT => position.turn_left(),
            Command::RIGHT => position.turn_right(),
            Command::MOVE => position.move_forward(),
            Command::REPORT => {
                report(&position);
                position
            }
        }
    })
}

fn report(position: &Position) {
    println!("{:?}", position);
}

fn read_file(file_path: &str) -> String {
    fs::read_to_string(file_path).expect(&format!("Failed to read file {}", file_path))
}

fn parse_input(content: String) -> (Position, Vec<Command>) {
    let lines: Vec<&str> = content.lines().collect();
    (get_start_position(&lines), get_commands(&lines))
}

fn get_start_position(lines: &Vec<&str>) -> Position {
    let first_line: &str = lines[0];
    let args: Vec<&str> = first_line[6..].split(",").collect();
    Position {
        location: (args[0].parse().unwrap(), args[1].parse().unwrap()),
        orientation: args[2].parse().unwrap()
    }
}

fn get_commands(lines: &Vec<&str>) -> Vec<Command> {
    lines[1..].iter().map(|line| {
        line.trim()
    }).filter(|line| {
        !line.is_empty()
    }).map(|line| {
        line.parse().unwrap()
    }).collect()
}
```