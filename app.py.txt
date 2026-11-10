import json
import os
from datetime import datetime

FILENAME = "planner_data.json"

def load_tasks():
    """Load tasks from a JSON file if it exists."""
    if os.path.exists(FILENAME):
        with open(FILENAME, "r") as file:
            try:
                return json.load(file)
            except json.JSONDecodeError:
                return []
    return []

def save_tasks(tasks):
    """Save tasks to a JSON file."""
    with open(FILENAME, "w") as file:
        json.dump(tasks, file, indent=4)

def add_task(tasks):
    """Add a new study task."""
    subject = input("Enter subject/topic name: ").strip()
    task_desc = input("Enter task description: ").strip()
    due_date = input("Enter due date (YYYY-MM-DD): ").strip()
    
    # Simple date validation
    try:
        datetime.strptime(due_date, "%Y-%m-%d")
    except ValueError:
        print("❌ Invalid date format! Please use YYYY-MM-DD.")
        return

    task = {
        "id": len(tasks) + 1,
        "subject": subject,
        "description": task_desc,
        "due_date": due_date,
        "completed": False
    }
    
    tasks.append(task)
    save_tasks(tasks)
    print("✅ Task added successfully!")

def view_tasks(tasks):
    """Display all study tasks."""
    if not tasks:
        print("\n📋 No study tasks found! Add one to get started.")
        return

    print("\n" + "="*60)
    print(f"{'ID':<4} | {'Subject':<15} | {'Task':<20} | {'Due Date':<10} | {'Status'}")
    print("="*60)
    for t in tasks:
        status = "✔️ Done" if t["completed"] else "⏳ Pending"
        print(f"{t['id']:<4} | {t['subject']:<15} | {t['description']:<20} | {t['due_date']:<10} | {status}")
    print("="*60 + "\n")

def mark_completed(tasks):
    """Mark a task as completed."""
    view_tasks(tasks)
    if not tasks:
        return
    
    try:
        task_id = int(input("Enter Task ID to mark as done: "))
        for t in tasks:
            if t["id"] == task_id:
                t["completed"] = True
                save_tasks(tasks)
                print(f"🎉 Task #{task_id} marked as completed!")
                return
        print("❌ Task ID not found.")
    except ValueError:
        print("❌ Please enter a valid numerical ID.")

def delete_task(tasks):
    """Delete a task from the planner."""
    view_tasks(tasks)
    if not tasks:
        return
        
    try:
        task_id = int(input("Enter Task ID to delete: "))
        for idx, t in enumerate(tasks):
            if t["id"] == task_id:
                deleted = tasks.pop(idx)
                # Re-index remaining task IDs
                for i, remaining in enumerate(tasks):
                    remaining["id"] = i + 1
                save_tasks(tasks)
                print(f"🗑️ Deleted task: '{deleted['description']}'")
                return
        print("❌ Task ID not found.")
    except ValueError:
        print("❌ Please enter a valid numerical ID.")

def main():
    tasks = load_tasks()
    while True:
        print("\n--- 📚 STUDY PLANNER MENU ---")
        print("1. View Tasks")
        print("2. Add Task")
        print("3. Mark Task as Completed")
        print("4. Delete Task")
        print("5. Exit")
        
        choice = input("Select an option (1-5): ").strip()
        
        if choice == "1":
            view_tasks(tasks)
        elif choice == "2":
            add_task(tasks)
        elif choice == "3":
            mark_completed(tasks)
        elif choice == "4":
            delete_task(tasks)
        elif choice == "5":
            print("👋 Goodbye! Happy studying!")
            break
        else:
            print("❌ Invalid option. Please choose between 1 and 5.")

if __name__ == "__main__":
    main()
