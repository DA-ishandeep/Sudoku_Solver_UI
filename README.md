# Sudoku Solver UI
Py script to solve sudoku. User can input numbers in grid. 0 means value is blank. 

```
import tkinter as tk
from tkinter import messagebox

# The solver functions from the previous solution
def is_valid(board, row, col, num):
    for i in range(9):
        if board[row][i] == num or board[i][col] == num:
            return False
    box_row, box_col = row // 3 * 3, col // 3 * 3
    for i in range(3):
        for j in range(3):
            if board[box_row + i][box_col + j] == num:
                return False
    return True

def solve_sudoku(board):
    for row in range(9):
        for col in range(9):
            if board[row][col] == 0:
                for num in range(1, 10):
                    if is_valid(board, row, col, num):
                        board[row][col] = num
                        if solve_sudoku(board):
                            return True
                        board[row][col] = 0
                return False
    return True

# Create a tkinter window for the UI
class SudokuSolverUI:
    def __init__(self, root):
        self.root = root
        self.root.title("Sudoku Solver")
        self.cells = [[None for _ in range(9)] for _ in range(9)]  # 9x9 grid of cells
        self.create_grid()
        self.create_buttons()

    def create_grid(self):
        for row in range(9):
            for col in range(9):
                # Entry widget with a validation function to limit input
                cell = tk.Entry(self.root, width=3, font=("Arial", 18), justify="center")
                cell.grid(row=row, column=col, padx=5, pady=5)
                cell.config(validate="key", validatecommand=(self.root.register(self.validate_digit), '%P'))
                self.cells[row][col] = cell

    def validate_digit(self, value):
        # Only allow one digit between 1 and 9, or empty
        if value == "" or (value.isdigit() and 1 <= int(value) <= 9):
            return True
        return False

    def get_board(self):
        board = []
        for row in range(9):
            board_row = []
            for col in range(9):
                value = self.cells[row][col].get()
                board_row.append(int(value) if value.isdigit() else 0)
            board.append(board_row)
        return board

    def set_board(self, board):
        for row in range(9):
            for col in range(9):
                if board[row][col] != 0:
                    self.cells[row][col].delete(0, tk.END)
                    self.cells[row][col].insert(0, str(board[row][col]))

    def solve(self):
        board = self.get_board()
        if solve_sudoku(board):
            self.set_board(board)
        else:
            messagebox.showinfo("Error", "No solution exists!")

    def reset(self):
        # Clear all the cells
        for row in range(9):
            for col in range(9):
                self.cells[row][col].delete(0, tk.END)

    def create_buttons(self):
        solve_button = tk.Button(self.root, text="Solve", command=self.solve)
        solve_button.grid(row=9, column=4, pady=10)

        reset_button = tk.Button(self.root, text="Reset", command=self.reset)
        reset_button.grid(row=9, column=5, pady=10)

# Run the tkinter application
if __name__ == "__main__":
    root = tk.Tk()
    app = SudokuSolverUI(root)
    root.mainloop()
```
