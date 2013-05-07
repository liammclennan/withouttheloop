---
title: Functional Game of Life pt 2 (Haskell)
author: Liam McLennan
date: 2013-05-08 20:32
template: article.jade
---

In [part 1 of this series](../2013-05-06-functional-game-of-life/) I began to implement the game of life cellular automaton in C# using the functional programming style (at least as I know it). Part 1 defined the data model and implemented a function to render the state of the system to HTML. 

For this part 2 of the series I port my C# solution to Haskell. 

Firstly, the data structure. As with the C# version I model the game of life as a list of living cells. The definition of a `cell` is:

    data Cell = Cell {
      x       :: Int,
      y       :: Int
    } deriving (Show)

where x and y are functions that return the cartesian coordinates of the cell. It is the same as the C# version, except that this `Cell` type is immutable.

Having defined my `Cell` type I then defined a set of functions to do useful things with a `Cell` or a list of cells. `renderLocation` is a function that converts a point (xv,yv) and a boolean (indicating if the cell at that location is living) into its HTML string.

    renderLocation :: Int -> Int -> Bool -> String
    renderLocation xv yv on = printf 
                              "<div class=\"cell %s\" style=\"left: %dpx; top: %dpx;\">&nbsp;</div>\n" 
                              (if on then "on" else "") (20 * (xv+1)) (20 * (yv+1))

The first line is a type definition for the function. Its C# equivalent would be `Func<int,int,bool,string>`.

To make `renderLocation` useful I need a function to determine if a given location contains a living cell:

    isOn :: [Cell] -> Int -> Int -> Bool
    isOn cs xv yv = any cellMatch cs
      where cellMatch c = x c == xv && y c == yv

This roughly translates to the C# `cs.Any(c => x(c) == xv && y(c) == yv)`.

The other helper functions required calculate the largest X and Y values to determine the size of the system:

    largestX :: [Cell] -> Int
    largestX cs = maximum $ map x cs

    largestY :: [Cell] -> Int
    largestY cs = maximum $ map y cs

`largestX` translated to C# is:

    public int largestX(Cell[] cs) {
      return cs.Select(c => x(c)).Max();
    }

Next I created three living cells, at coordinates (1,2), (3,5) and (14,13):

    cells = [Cell 1 2, Cell 3 5, Cell 14 13]

Now I can create a `render` function that produces the HTML representation of a system:

    render :: [Cell] -> String
    render cs = concat [renderLocation x y (isOn cs x y) | x <- [0..xBound], y <- [0..yBound]]
      where xBound = largestX cs + 1
            yBound = largestY cs + 1

The expression `[renderLocation x y (isOn cs x y) | x <- [0..xBound], y <- [0..yBound]]` is a list comprehension. The part after the pipe `x <- [0..xBound], y <- [0..yBound]` produces the cartesian product of all possible x values (`[0..xBound]`) and all possible y values (`[0..yBound]`). For each point the `renderLocation` function is called. The results are collected into a list of strings which is then flattened to a single string by the `concat` function.

This is equivalent to the C# version:

    var results = from x in Enumerable.Range(0, largestX+1)
                  from y in Enumerable.Range(0, largestY+1)
                  select buildCell(x, y, IsOn(cells, x, y));

Now to get the program to actually do something I define a list of cells and then use the programs `main` function to render my list of cells and write the result to stdout.

    cells = [Cell 1 2, Cell 3 5, Cell 14 13]

    main = (putStrLn . render) cells

The output of this program is:

