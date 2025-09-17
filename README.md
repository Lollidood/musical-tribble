import tkinter as tk
from tkinter import messagebox
import random

root = tk.Tk()
root.title("Tic Tac Toe")
root.iconbitmap("icon.ico")

# ===== Global variables =====
current_player = "X"  # Human is always "X"
buttons = []          # List to store all buttons

# ===== Winning combinations =====
winning_combinations = [
    [0, 1, 2], [3, 4, 5], [6, 7, 8],  # Horizontal lines
    [0, 3, 6], [1, 4, 7], [2, 5, 8],  # Vertical lines
    [0, 4, 8], [2, 4, 6]              # Diagonal lines
]

# ===== Function to check winner =====
def check_winner():
    for combo in winning_combinations:
        a, b, c = combo
        if buttons[a]["text"] == buttons[b]["text"] == buttons[c]["text"] != "":
            buttons[a]['highlightbackground'] = 'lightgreen'
            buttons[b]['highlightbackground'] = 'lightgreen'
            buttons[c]['highlightbackground'] = 'lightgreen'
            return True
    return False

# ===== Computer AI move =====
def computer_move():
    # 1. Check if computer can win
    for combo in winning_combinations:
        values = [buttons[i]["text"] for i in combo]
        if values.count("O") == 2 and values.count("") == 1:
            choice = combo[values.index("")]
            buttons[choice]["text"] = "O"
            return

    # 2. Block human's winning move
    for combo in winning_combinations:
        values = [buttons[i]["text"] for i in combo]
        if values.count("X") == 2 and values.count("") == 1:
            choice = combo[values.index("")]
            buttons[choice]["text"] = "O"
            return

    # 3. Otherwise, random move
    available_moves = [i for i in range(9) if buttons[i]["text"] == ""]
    if available_moves:
        choice = random.choice(available_moves)
        buttons[choice]["text"] = "O"

# ===== Handle player's click =====
def on_click(index):
    global current_player
    if buttons[index]["text"] == "" and current_player == "X":
        buttons[index]["text"] = "X"

        if check_winner():
            messagebox.showinfo("Game Over", "You (X) win!")
            reset_game()
            return
        elif all(button["text"] != "" for button in buttons):
            messagebox.showinfo("Draw", "It's a draw!")
            reset_game()
            return

        current_player = "O"
        root.after(500, computer_turn)

def computer_turn():
    global current_player
    computer_move()
    if check_winner():
        messagebox.showinfo("Game Over", "Computer (O) wins!")
        reset_game()
    elif all(button["text"] != "" for button in buttons):
        messagebox.showinfo("Draw", "It's a draw!")
        reset_game()
    else:
        current_player = "X"

# ===== Reset game =====
def reset_game():
    global current_player
    current_player = "X"
    for button in buttons:
        button.config(text="", bg="SystemButtonFace")

# ===== Create board =====
for i in range(9):
    button = tk.Button(root, text="", font=("Arial", 20), width=5, height=2,
                       command=lambda i=i: on_click(i))
    button.grid(row=i // 3, column=i % 3)
    buttons.append(button)

# ===== Reset button =====
reset_button = tk.Button(root, text="New Game", font=("Arial", 14), command=reset_game)
reset_button.grid(row=3, column=0, columnspan=3, sticky="we")

root.mainloop()
