# rock-paper-scissor-game
import tkinter as tk
from tkinter import messagebox
import random

class RPSGame:
    CHOICES = ['Rock', 'Paper', 'Scissors']
    ICONS = {'Rock': '🪨', 'Paper': '📄', 'Scissors': '✂️'}
    COLORS = {'Rock': '#e74c3c', 'Paper': '#e67e22', 'Scissors': '#2ecc71'}
    TOTAL_ROUNDS = 5

    def __init__(self, root):
        self.root = root
        self.root.title("🎮 Rock Paper Scissors Game")
        self.root.geometry("600x500")
        self.root.config(bg="yellow")
        self.root.resizable(False, False)

        self.user_score = 0
        self.computer_score = 0
        self.round = 1
        self.game_active = True

        self.buttons = {}
        self.build_ui()

    def build_ui(self):
        """Initializes the UI components."""
        tk.Label(
            self.root,
            text="Rock Paper Scissors",
            font=('Times New Roman', 22, 'bold'),
            bg="yellow", fg="black"
        ).pack(pady=15)

        self.round_label = tk.Label(
            self.root,
            text=f"Round {self.round}/{self.TOTAL_ROUNDS}",
            font=('Times New Roman', 16, 'bold'),
            bg="yellow", fg="black"
        )
        self.round_label.pack()

        btn_frame = tk.Frame(self.root, bg="yellow")
        btn_frame.pack(pady=20)

        for idx, choice in enumerate(self.CHOICES):
            btn = tk.Button(
                btn_frame,
                text=f"{self.ICONS[choice]} {choice}",
                font=('Times New Roman', 14, 'bold'),
                width=12,
                command=lambda c=choice: self.handle_user_choice(c),
                bg=self.COLORS[choice],
                fg="white",
                activebackground="black"
            )
            btn.grid(row=0, column=idx, padx=15)
            self.buttons[choice] = btn

        self.result_text = tk.Label(
            self.root,
            text="",
            font=('Times New Roman', 18, 'bold'),
            bg="yellow", fg="black"
        )
        self.result_text.pack(pady=15)

        self.user_choice_label = tk.Label(
            self.root,
            text="You: ",
            font=('Times New Roman', 14, 'bold'),
            bg="yellow", fg="black"
        )
        self.user_choice_label.pack()

        self.computer_choice_label = tk.Label(
            self.root,
            text="Computer: ",
            font=('Times New Roman', 14, 'bold'),
            bg="yellow", fg="black"
        )
        self.computer_choice_label.pack()

        self.score_label = tk.Label(
            self.root,
            text="Score - You: 0 | Computer: 0",
            font=('Times New Roman', 14, 'bold'),
            bg="yellow", fg="black"
        )
        self.score_label.pack(pady=10)

        self.reset_btn = tk.Button(
            self.root,
            text="🔁 Reset Game",
            font=('Times New Roman', 12, 'bold'),
            bg="black", fg="white",
            command=self.reset_game
        )
        self.reset_btn.pack(pady=10)

    def handle_user_choice(self, user_choice):
        """Handles the logic when a user makes a choice."""
        if not self.game_active:
            return

        self.disable_buttons()
        computer_choice = random.choice(self.CHOICES)
        result = self.determine_winner(user_choice, computer_choice)

        self.display_choices(user_choice, computer_choice)
        self.result_text.config(text=result)
        self.update_scores(result)
        self.round += 1
        self.update_round_label()
        self.root.after(1500, self.check_game_over)

    def determine_winner(self, user, computer):
        """Determines the winner of the round."""
        if user == computer:
            return "It's a Tie!"
        elif (user == 'Rock' and computer == 'Scissors') or \
             (user == 'Scissors' and computer == 'Paper') or \
             (user == 'Paper' and computer == 'Rock'):
            return "You Win!"
        else:
            return "You Lose!"

    def display_choices(self, user_choice, computer_choice):
        """Updates the UI with selected choices."""
        self.user_choice_label.config(text=f"You: {self.ICONS[user_choice]} {user_choice}")
        self.computer_choice_label.config(text=f"Computer: {self.ICONS[computer_choice]} {computer_choice}")

    def update_scores(self, result):
        """Updates the score based on result."""
        if result == "You Win!":
            self.user_score += 1
        elif result == "You Lose!":
            self.computer_score += 1

        self.score_label.config(
            text=f"Score - You: {self.user_score} | Computer: {self.computer_score}"
        )

    def update_round_label(self):
        """Updates the round label."""
        if self.round <= self.TOTAL_ROUNDS:
            self.round_label.config(text=f"Round {self.round}/{self.TOTAL_ROUNDS}")
        else:
            self.round_label.config(text="Final Round!")

    def check_game_over(self):
        """Checks if the game is over and shows result."""
        if self.round > self.TOTAL_ROUNDS:
            self.game_active = False
            if self.user_score > self.computer_score:
                final_result = "🎉 You Won the Match!"
            elif self.user_score < self.computer_score:
                final_result = "😞 You Lost the Match!"
            else:
                final_result = "🤝 It's a Draw!"

            messagebox.showinfo(
                "Game Over",
                f"{final_result}\nFinal Score:\nYou: {self.user_score} | Computer: {self.computer_score}"
            )
        else:
            self.enable_buttons()

    def reset_game(self):
        """Resets the game to its initial state."""
        self.user_score = 0
        self.computer_score = 0
        self.round = 1
        self.game_active = True

        self.user_choice_label.config(text="You: ")
        self.computer_choice_label.config(text="Computer: ")
        self.result_text.config(text="")
        self.score_label.config(text="Score - You: 0 | Computer: 0")
        self.round_label.config(text=f"Round {self.round}/{self.TOTAL_ROUNDS}")
        self.enable_buttons()

    def disable_buttons(self):
        """Disables choice buttons."""
        for btn in self.buttons.values():
            btn.config(state='disabled')

    def enable_buttons(self):
        """Enables choice buttons."""
        for btn in self.buttons.values():
            btn.config(state='normal')


# Launch the game
if __name__ == "__main__":
    root = tk.Tk()
    game = RPSGame(root)
    root.mainloop()
