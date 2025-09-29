import os
import time
import tkinter as tk
from tkinter import messagebox
import subprocess
import platform
import shlex
from openpyxl import Workbook, load_workbook
from datetime import datetime

# --- SETTINGS ---
BASE_DIR = r"C:/AutoMahn/" # This is where the output ends up. Change the filepath if you'd like it elsewhere.
if not os.path.exists(BASE_DIR):
    os.makedirs(BASE_DIR)

# These will be set later
LOG_FILE = None
BASE_URL = None
PLATFORM = None

# --- Browser launcher ---
def open_profile_in_browser(url):
    if platform.system() == "Windows":
        command = f'start chrome "{url}"'
        subprocess.Popen(command, shell=True)
    elif platform.system() == "Darwin":
        command = f'open -a "Google Chrome" "{url}"'
        subprocess.Popen(shlex.split(command))
    elif platform.system() == "Linux":
        command = f'google-chrome "{url}"'
        subprocess.Popen(shlex.split(command))

# --- Manual checker GUI ---
class ManualChecker:
    def __init__(self, master, usernames):
        self.master = master
        self.usernames = usernames
        self.current_index = 0
        self.total = len(usernames)

        self.label = tk.Label(master, text="", font=("Arial", 14))
        self.label.pack(pady=10)

        self.dead_button = tk.Button(master, text="DEAD", command=lambda: self.record_status("Dead"), width=20, height=2, bg="red", fg="white")
        self.dead_button.pack(pady=5)

        self.alive_button = tk.Button(master, text="ALIVE", command=lambda: self.record_status("Alive"), width=20, height=2, bg="green", fg="white")
        self.alive_button.pack(pady=5)

        self.skip_button = tk.Button(master, text="SKIP", command=lambda: self.record_status("Skipped"), width=20, height=2, bg="gray", fg="white")
        self.skip_button.pack(pady=5)

        self.status_label = tk.Label(master, text="", font=("Arial", 10))
        self.status_label.pack(pady=5)

        self.show_next()

    def show_next(self):
        if self.current_index >= self.total:
            self.label.config(text="All usernames checked!")
            self.status_label.config(text="")
            self.dead_button.config(state=tk.DISABLED)
            self.alive_button.config(state=tk.DISABLED)
            self.skip_button.config(state=tk.DISABLED)
            return

        self.current_username = self.usernames[self.current_index]
        progress = f"{self.current_index + 1}/{self.total}"
        self.label.config(text=f"Check: {self.current_username} ({progress})")

        url = BASE_URL + self.current_username
        open_profile_in_browser(url)

        self.status_label.config(text="Opened in browser. Choose status.")

    def record_status(self, status):
        timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        ws.append([self.current_username, status, timestamp])
        wb.save(LOG_FILE)
        self.current_index += 1
        self.show_next()

# --- Username input GUI ---
class UsernameInputApp:
    def __init__(self, master):
        self.master = master
        master.title("Choose Platform")

        tk.Label(master, text="Select platform to check:", font=("Arial", 12)).pack(pady=10)

        self.snapchat_btn = tk.Button(master, text="Snapchat", command=lambda: self.set_platform("Snapchat"), width=20, height=2, bg="yellow")
        self.snapchat_btn.pack(pady=5)

        self.instagram_btn = tk.Button(master, text="Instagram", command=lambda: self.set_platform("Instagram"), width=20, height=2, bg="pink")
        self.instagram_btn.pack(pady=5)

    def set_platform(self, platform_choice):
        global PLATFORM, BASE_URL, LOG_FILE, wb, ws

        PLATFORM = platform_choice
        timestamp_str = datetime.now().strftime("%Y%m%d_%H%M%S")
        LOG_FILE = os.path.join(BASE_DIR, f"{PLATFORM.lower()}_manual_check_{timestamp_str}.xlsx")

        BASE_URL = "https://www.snapchat.com/add/" if PLATFORM == "Snapchat" else "https://www.instagram.com/"

        # Create workbook
        wb = Workbook()
        ws = wb.active
        ws.append(["Username", "Status", "Timestamp"])
        
        self.master.destroy()
        self.launch_username_input()

    def launch_username_input(self):
        root = tk.Tk()
        root.title(f"Enter {PLATFORM} Usernames to Check")

        tk.Label(root, text=f"Paste {PLATFORM} usernames (one per line):", font=("Arial", 12)).pack(pady=5)
        textbox = tk.Text(root, width=40, height=15)
        textbox.pack(pady=5)

        def start_checking():
            raw_text = textbox.get("1.0", tk.END).strip()
            usernames = [line.strip() for line in raw_text.splitlines() if line.strip()]
            if not usernames:
                messagebox.showerror("Error", "Please enter at least one username.")
                return
            root.destroy()
            self.launch_checker(usernames)

        start_button = tk.Button(root, text="Start Checking", command=start_checking, bg="blue", fg="white", height=2, width=20)
        start_button.pack(pady=10)

        root.mainloop()

    def launch_checker(self, usernames):
        root = tk.Tk()
        root.title(f"{PLATFORM} Manual Checker")
        ManualChecker(root, usernames)
        root.mainloop()

# --- Launch platform selection ---
root = tk.Tk()
app = UsernameInputApp(root)
root.mainloop()
