# ExpNo 5: Implement Minimax Search Algorithm for a Simple TIC-TAC-TOE Game

| **Name**                       | Surya Prakash B  |
| ------------------------------ | - |
| **Register Number** | 212224230281  |

## Aim

To implement the **Minimax Search Algorithm** for a simple Tic-Tac-Toe game using Python 3.

---

## Theory

### Minimax Algorithm

**Minimax** is a decision-making algorithm commonly used in two-player games such as:

* Tic-Tac-Toe
* Chess
* Checkers
* Connect Four

It assumes that both players play optimally.

One player attempts to **maximize** the score, while the other attempts to **minimize** the score.

In this Tic-Tac-Toe implementation:

```text
Player O → Maximizing Player
Player X → Minimizing Player
```

The evaluation values are:

| Game Result | Score |
| ----------- | ----: |
| `O` wins    |  `+1` |
| Draw        |   `0` |
| `X` wins    |  `-1` |

Therefore:

```text
O tries to maximize the score.
X tries to minimize the score.
```

---

# Basic Idea of Minimax

Suppose the AI has several possible moves.

For every move, it asks:

```text
If I make this move,
what is the best move my opponent can make?
```

Then it continues exploring future moves until it reaches an end state:

```text
Win
Loss
Draw
```

The final result is propagated back through the game tree.

---

# Maximizing and Minimizing Players

## MAX Player

The MAX player tries to obtain the **largest possible value**.

In this program:

```text
MAX = O
```

For example, if the possible scores are:

```text
-1, 0, +1
```

MAX chooses:

```text
+1
```

---

## MIN Player

The MIN player tries to obtain the **smallest possible value**.

In this program:

```text
MIN = X
```

If the possible scores are:

```text
-1, 0, +1
```

MIN chooses:

```text
-1
```

---

# Minimax Game Tree

A simplified game tree can be represented as:

```text
                    MAX
                  /  |  \
                 /   |   \
               MIN  MIN  MIN
              / \   / \   / \
             1  -1 0   1 -1  0
```

Each MIN node selects the minimum value.

For example:

```text
MIN(1, -1) = -1
MIN(0, 1)  = 0
MIN(-1, 0) = -1
```

So MAX receives:

```text
[-1, 0, -1]
```

MAX selects:

```text
MAX(-1, 0, -1) = 0
```

Therefore, the best available result is a draw.

---

# Example of Minimax in Tic-Tac-Toe

![Minimax Example](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/498656fc-79ce-4234-a623-06568bad8dda)

Suppose `X` has three possible moves.

One move immediately wins the game.

Its score is:

```text
+10
```

The other moves may allow the opponent to win:

```text
-10
```

Therefore, the maximizing player chooses the move with the highest score.

---

# Opponent's Decision

![Opponent Minimax Example](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/029b1a70-e92e-46c0-9a32-d6aea98ecd9d)

The opponent also plays optimally.

Therefore, if the opponent can choose between:

```text
+10
-10
```

the minimizing player chooses:

```text
-10
```

because it is the worst result for the maximizing player.

---

# Complete Minimax Tree

![Minimax Tree](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/12b82542-54fb-47e7-8f76-b75fddc40f92)

The Minimax algorithm recursively moves between:

```text
MAX → MIN → MAX → MIN → ...
```

until a terminal game state is reached.

The result is then propagated upward through the tree.

---

# Algorithm

1. Initialize an empty Tic-Tac-Toe board.
2. Let player `X` make the first move.
3. Check whether the current board is a terminal state.
4. If `X` wins:

   * Return `-1`.
5. If `O` wins:

   * Return `+1`.
6. If the board is full and nobody wins:

   * Return `0`.
7. For the maximizing player `O`:

   * Generate every possible move.
   * Call `min()` recursively.
   * Select the move having the maximum score.
8. For the minimizing player `X`:

   * Generate every possible move.
   * Call `max()` recursively.
   * Select the move having the minimum score.
9. Continue until the game reaches a win, loss, or draw.
10. Display the winner or draw result.

---

# Python 3 Program

```python
import time


class Game:

    def __init__(self):
        self.initialize_game()


    # Initialize empty board
    def initialize_game(self):

        self.current_state = [
            ['.', '.', '.'],
            ['.', '.', '.'],
            ['.', '.', '.']
        ]

        # Player X always plays first
        self.player_turn = 'X'


    # Display board
    def draw_board(self):

        for i in range(3):

            for j in range(3):
                print(
                    '{}|'.format(self.current_state[i][j]),
                    end=" "
                )

            print()

        print()


    # Check whether move is valid
    def is_valid(self, px, py):

        # Coordinate outside board
        if px < 0 or px > 2 or py < 0 or py > 2:
            return False

        # Cell already occupied
        if self.current_state[px][py] != '.':
            return False

        return True


    # Check whether game has ended
    def is_end(self):

        # Vertical win
        for i in range(3):

            if (
                self.current_state[0][i] != '.'
                and self.current_state[0][i]
                == self.current_state[1][i]
                == self.current_state[2][i]
            ):

                return self.current_state[0][i]


        # Horizontal win
        for i in range(3):

            if self.current_state[i] == ['X', 'X', 'X']:
                return 'X'

            elif self.current_state[i] == ['O', 'O', 'O']:
                return 'O'


        # Main diagonal win
        if (
            self.current_state[0][0] != '.'
            and self.current_state[0][0]
            == self.current_state[1][1]
            == self.current_state[2][2]
        ):

            return self.current_state[0][0]


        # Secondary diagonal win
        if (
            self.current_state[0][2] != '.'
            and self.current_state[0][2]
            == self.current_state[1][1]
            == self.current_state[2][0]
        ):

            return self.current_state[0][2]


        # Check whether empty cells remain
        for i in range(3):

            for j in range(3):

                if self.current_state[i][j] == '.':
                    return None


        # Board full - draw
        return '.'


    # MAX player = O
    def max(self):

        # Lowest possible starting value
        maxv = -2

        px = None
        py = None

        result = self.is_end()


        # Terminal states
        if result == 'X':
            return (-1, 0, 0)

        elif result == 'O':
            return (1, 0, 0)

        elif result == '.':
            return (0, 0, 0)


        # Try every possible move for O
        for i in range(3):

            for j in range(3):

                if self.current_state[i][j] == '.':

                    # Simulate O's move
                    self.current_state[i][j] = 'O'

                    # Opponent X tries to minimize
                    m, min_i, min_j = self.min()


                    # Choose maximum score
                    if m > maxv:

                        maxv = m
                        px = i
                        py = j


                    # Undo move
                    self.current_state[i][j] = '.'


        return (maxv, px, py)


    # MIN player = X
    def min(self):

        # Highest possible starting value
        minv = 2

        qx = None
        qy = None

        result = self.is_end()


        # Terminal states
        if result == 'X':
            return (-1, 0, 0)

        elif result == 'O':
            return (1, 0, 0)

        elif result == '.':
            return (0, 0, 0)


        # Try every possible move for X
        for i in range(3):

            for j in range(3):

                if self.current_state[i][j] == '.':

                    # Simulate X's move
                    self.current_state[i][j] = 'X'

                    # Opponent O tries to maximize
                    m, max_i, max_j = self.max()


                    # Choose minimum score
                    if m < minv:

                        minv = m
                        qx = i
                        qy = j


                    # Undo move
                    self.current_state[i][j] = '.'


        return (minv, qx, qy)


    # Main game
    def play(self):

        while True:

            self.draw_board()

            self.result = self.is_end()


            # Game finished
            if self.result is not None:

                if self.result == 'X':
                    print('The winner is X!')

                elif self.result == 'O':
                    print('The winner is O!')

                elif self.result == '.':
                    print("It's a tie!")


                self.initialize_game()

                return


            # Human player's turn
            if self.player_turn == 'X':

                while True:

                    # Minimax recommended move
                    start = time.time()

                    m, qx, qy = self.min()

                    end = time.time()


                    print(
                        'Evaluation time: {}s'
                        .format(round(end - start, 7))
                    )

                    print(
                        'Recommended move: X = {}, Y = {}'
                        .format(qx, qy)
                    )


                    # User input
                    px = int(
                        input('Insert the X coordinate: ')
                    )

                    py = int(
                        input('Insert the Y coordinate: ')
                    )


                    # Validate move
                    if self.is_valid(px, py):

                        self.current_state[px][py] = 'X'

                        self.player_turn = 'O'

                        break

                    else:

                        print(
                            'The move is not valid! Try again.'
                        )


            # AI player's turn
            else:

                m, px, py = self.max()

                self.current_state[px][py] = 'O'

                self.player_turn = 'X'


# Main function
def main():

    g = Game()

    g.play()


if __name__ == "__main__":
    main()
```

---

# Understanding the `max()` Function

The `max()` function represents the AI player:

```text
O
```

Its objective is:

```text
Get the largest possible score.
```

The possible terminal scores are:

```text
X wins → -1
Draw   →  0
O wins → +1
```

Therefore, `O` prefers:

```text
+1
```

The function starts with:

```python
maxv = -2
```

Why `-2`?

Because every legitimate score is greater than `-2`:

```text
-1
 0
+1
```

Therefore, the first valid result will always replace `-2`.

---

# Understanding the `min()` Function

The `min()` function represents:

```text
X
```

Its objective is:

```text
Get the smallest possible score.
```

It starts with:

```python
minv = 2
```

because every possible game score is less than `2`:

```text
-1
0
1
```

For every empty position:

```python
if self.current_state[i][j] == '.':
```

the function temporarily places:

```python
self.current_state[i][j] = 'X'
```

Then it asks:

```python
m, max_i, max_j = self.max()
```

This means:

> If X makes this move, what is the best move O can make afterward?

X then selects the smallest value:

```python
if m < minv:
    minv = m
    qx = i
    qy = j
```

Finally, the board is restored:

```python
self.current_state[i][j] = '.'
```

This step is extremely important.

Without undoing the move, the next recursive branch would start from an incorrect board.

---

# Recursive Relationship

The most important part of Minimax is:

```text
MAX calls MIN
MIN calls MAX
```

In this program:

```python
# Inside max()
m, min_i, min_j = self.min()
```

and:

```python
# Inside min()
m, max_i, max_j = self.max()
```

Therefore, the algorithm recursively simulates:

```text
O → X → O → X → O → ...
```

until a terminal state occurs.

---

# Terminal State Evaluation

The evaluation function is effectively:

```text
X wins → -1
O wins → +1
Draw   →  0
```

This is implemented using:

```python
if result == 'X':
    return (-1, 0, 0)

elif result == 'O':
    return (1, 0, 0)

elif result == '.':
    return (0, 0, 0)
```

The first value is the score.

The other two values represent:

```text
Row coordinate
Column coordinate
```

---

# Board Representation

The board starts as:

```text
.| .| .|
.| .| .|
.| .| .|
```

Internally:

```python
[
    ['.', '.', '.'],
    ['.', '.', '.'],
    ['.', '.', '.']
]
```

where:

| Symbol | Meaning        |
| ------ | -------------- |
| `.`    | Empty position |
| `X`    | Human player   |
| `O`    | AI player      |

---

# Board Coordinates

The coordinates are:

```text
        Column

         0   1   2

Row 0    .   .   .
Row 1    .   .   .
Row 2    .   .   .
```

Therefore:

```text
0 0 → Top-left
0 1 → Top-middle
0 2 → Top-right

1 0 → Middle-left
1 1 → Center
1 2 → Middle-right

2 0 → Bottom-left
2 1 → Bottom-middle
2 2 → Bottom-right
```

For example:

```text
Insert the X coordinate: 1
Insert the Y coordinate: 1
```

places `X` in the center.

> In this program, the first coordinate effectively represents the **row** and the second represents the **column**.

---

# Sample Execution

```text
.| .| .|
.| .| .|
.| .| .|

Evaluation time: 0.8432512s
Recommended move: X = 0, Y = 0

Insert the X coordinate: 0
Insert the Y coordinate: 0
```

Board:

```text
X| .| .|
.| .| .|
.| .| .|
```

The AI then uses Minimax to determine its optimal move.

For example:

```text
X| .| .|
.| O| .|
.| .| .|
```

The game continues until:

* `X` wins,
* `O` wins, or
* the game ends in a draw.

---

# Sample Input and Output

## Sample Game 1

![Tic-Tac-Toe Sample 1](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/6b668685-8bcc-43c5-b5c2-ddd43f3da84a)

---

## Sample Game 2

![Tic-Tac-Toe Sample 2](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/8ca1b08a-8312-4ef5-89df-e69b7b2c3fa2)

---

## Sample Game 3

![Tic-Tac-Toe Sample 3](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/dc06427a-d4ce-43a1-95bd-9acfaefac323)

---

## Sample Game 4

![Tic-Tac-Toe Sample 4](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/a8a27e2a-6fd4-46a2-afb5-6d27b8556702)

---

## Sample Game 5

![Tic-Tac-Toe Sample 5](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/a2acb6a1-ed8e-42e5-8968-fe805e4b0255)

---

# Important Functions

| Function            | Purpose                                 |
| ------------------- | --------------------------------------- |
| `initialize_game()` | Creates an empty board                  |
| `draw_board()`      | Displays the Tic-Tac-Toe board          |
| `is_valid()`        | Checks whether a move can be made       |
| `is_end()`          | Checks win, loss, draw, or ongoing game |
| `max()`             | Finds the best move for `O`             |
| `min()`             | Finds the best move for `X`             |
| `play()`            | Controls the game                       |
| `main()`            | Starts the program                      |

---

# Minimax Decision

For the maximizing player:

```text
Best Move = maximum(child scores)
```

For the minimizing player:

```text
Best Move = minimum(child scores)
```

Therefore:

```text
              MAX (O)
             /   |   \
           -1    0    1
```

MAX chooses:

```text
1
```

while:

```text
              MIN (X)
             /   |   \
           -1    0    1
```

MIN chooses:

```text
-1
```

---

# Why Minimax Plays Optimally

Minimax does not simply look at the next move.

It considers:

```text
My move
    ↓
Opponent's best move
    ↓
My best response
    ↓
Opponent's best response
    ↓
...
```

until the game ends.

Therefore, the AI assumes:

```text
My opponent will always make the best possible move.
```

This allows it to choose a move that gives the best guaranteed outcome.

---

# Complexity Analysis

Suppose:

* `b` = branching factor
* `d` = maximum depth of the game tree

The general Minimax time complexity is:

```text
O(b^d)
```

For Tic-Tac-Toe, the first move can have at most:

```text
9
```

choices.

Then:

```text
8
7
6
...
```

possible choices remain.

Therefore, the complete search space is relatively small and Minimax can search it efficiently.

### Space Complexity

Using recursive depth-first traversal, the recursion depth is approximately:

```text
O(d)
```

although additional game-state storage depends on the implementation.

---

# Advantages

* Produces optimal decisions when both players play optimally.
* Easy to understand for small games.
* Well suited for deterministic two-player games.
* Guarantees the best possible move when the complete game tree can be searched.

---

# Limitations

* Computationally expensive for large games.
* Number of states increases rapidly with game depth.
* Plain Minimax evaluates many unnecessary branches.
* More complex games require optimization techniques.

A commonly used improvement is:

```text
Alpha-Beta Pruning
```

which removes branches that cannot affect the final decision.

---

# Key Concepts

| Concept                 | Meaning                                                 |
| ----------------------- | ------------------------------------------------------- |
| **Minimax**             | Adversarial search algorithm for two-player games       |
| **MAX Player**          | Attempts to maximize the evaluation score               |
| **MIN Player**          | Attempts to minimize the evaluation score               |
| **Game Tree**           | Tree representing possible future game states           |
| **Terminal State**      | Win, loss, or draw state                                |
| **Evaluation Function** | Assigns numerical values to states                      |
| **Recursion**           | Function repeatedly calling itself through other states |
| **Backtracking**        | Undoing a simulated move                                |
| **Optimal Play**        | Selecting the best possible move                        |
| **Tic-Tac-Toe**         | Two-player zero-sum game                                |

---

# Result

Thus, the **Minimax Search Algorithm** was successfully implemented for a simple **Tic-Tac-Toe game** using Python 3, enabling the AI to evaluate possible game states and select an optimal move.
