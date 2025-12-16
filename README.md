import datetime

# قائمة العادات
habits = ["Exercise", "Read", "Meditate", "Journal", "Sleep Early"]

# دالة عرض العادات
def show_habits():
    print("\nToday's Habits:")
    for i, habit in enumerate(habits, start=1):
        print(f"{i}. {habit}")

# دالة تسجيل التقدم
def save_progress():
    show_habits()
    completed = input("\nEnter the numbers of habits you completed today (example: 1 3 5): ")

    completed_list = completed.split()
    today = datetime.date.today()

    # فتح أو إنشاء ملف جديد
    with open("habit_progress.txt", "a") as file:
        file.write(f"Date: {today}\n")
        file.write("Completed habits:\n")
        for num in completed_list:
            if num.isdigit() and 1 <= int(num) <= len(habits):
                file.write(f"- {habits[int(num) - 1]}\n")
        file.write("\n")

    print("\nProgress saved successfully!")

import json
import os

FILE_NAME = "habits.json"

def load_habits() -> list[str]:
    """Load habits from a JSON file. Returns an empty list if file doesn't exist or is invalid."""
    if not os.path.exists(FILE_NAME):
        return []

    try:
        with open(FILE_NAME, "r", encoding="utf-8") as f:
            data = json.load(f)

        # Support either {"habits": [...]} or just [...]
        if isinstance(data, dict) and "habits" in data and isinstance(data["habits"], list):
            return [str(h) for h in data["habits"]]
        if isinstance(data, list):
            return [str(h) for h in data]

    except (json.JSONDecodeError, OSError):
        pass

    return []

def save_habits(habits: list[str]) -> None:
    """Save habits to a JSON file."""
    data = {"habits": habits}
    with open(FILE_NAME, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)

def show_habits(habits: list[str]) -> None:
    if not habits:
        print("No habits yet.")
        return
    print("\nYour Habits:")
    for i, h in enumerate(habits, start=1):
        print(f"{i}. {h}")

def add_habit(habits: list[str]) -> None:
    habit = input("Enter a new habit: ").strip()
    if not habit:
        print("Habit cannot be empty.")
        return
    if habit in habits:
        print("This habit already exists.")
        return
    habits.append(habit)
    save_habits(habits)
    print("Habit added and saved.")

def delete_habit(habits: list[str]) -> None:
    show_habits(habits)
    if not habits:
        return

    choice = input("Enter habit number to delete: ").strip()
    if not choice.isdigit():
        print("Please enter a valid number.")
        return

    idx = int(choice) - 1
    if idx < 0 or idx >= len(habits):
        print("Number out of range.")
        return

    removed = habits.pop(idx)
    save_habits(habits)
    print(f"Deleted '{removed}' and saved.")

def main():
    habits = load_habits()
    print("Welcome to Habit Tracker (with Save/Load)!")

    while True:
        print("\nMenu:")
        print("1) Show habits")
        print("2) Add habit")
        print("3) Delete habit")
        print("4) Exit")

        option = input("Choose an option: ").strip()

        if option == "1":
            show_habits(habits)
        elif option == "2":
            add_habit(habits)
        elif option == "3":
            delete_habit(habits)
        elif option == "4":
            print("Goodbye!")
            break
        else:
            print("Invalid option. Try again.")

if __name__ == "__main__":
    main()

# تشغيل النظام
save_progress()
