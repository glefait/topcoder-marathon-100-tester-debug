# topcoder-marathon-100-tester-debug

## Build

    javac SameColorPairsVis.java
    jar cmvf META-INF/MANIFEST.MF tester.jar *.class

## Edit or this repo is useless:

This is not a debug but a problem misunderstanding: removing the tiles that define an area does not induce to remove all the remaining tiles in that area.
Obvious. 

## Problem description

I was stuck for a while on debugging my code until I found that tester do not produce the behaviour I was expected.

As stated in the problem description:
> In one move, you can remove two tiles of the same color if their bounding rectangle doesn't contain any tiles of other colors (it can contain only empty cells and tiles of the same color as the ones being removed).

Therefore, we should be able to clear a zone such as (numbers describes the color IDs and dot refers for already removed tiles):

    1.
    11
    
Once the area (top-left, bottom-right) has been clean, we should have:

    ..
    ..

However, the local Tester was not removing the remaining tiles (of the same color) in the area, but just the two extremes tiles, as we can see in the code:

    String removePair(int r1, int c1, int r2, int c2) {
        [...]
        for (int i = Math.min(r1, r2); i <= Math.max(r1, r2); ++i)
        for (int j = Math.min(c1, c2); j <= Math.max(c1, c2); ++j)
            if (board[i][j] != c && board[i][j] != '.')
                return "The rectangle specified by the tiles you're trying to remove must not contain tiles of any other color.";
        // finally, perform removal
        removed.add(new Pair(r1, c1, r2, c2, board[r1][c1]));
        board[r1][c1] = board[r2][c2] = '.'; // <-- here we just remove two tiles while the are may contains more tiles
        
which leads to an area:
    
    ..
    1.
        
## Changes:

Replace:

        board[r1][c1] = board[r2][c2] = '.';

with:

        for (int i = Math.min(r1, r2); i <= Math.max(r1, r2); i++)
        for (int j = Math.min(c1, c2); j <= Math.max(c1, c2); j++)
            board[i][j] = '.';
