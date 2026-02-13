# Smart-task-manager-
Java project
class Task:
    """Represents a single task with priority and metadata"""
    
    def __init__(self, task_id, description, priority):
        self.task_id = task_id
        self.description = description
        self.priority = priority  # 1=High, 2=Medium, 3=Low
        self.created_at = datetime.now()
        self.status = "Pending"
    
    def __str__(self):
        priority_map = {1: "High", 2: "Medium", 3: "Low"}
        return f"[{self.task_id}] {self.description} | Priority: {priority_map[self.priority]} | Status: {self.status}"


class SmartTaskManager:
    """
    Task Manager using multiple data structures:
    - Array: stores all tasks
    - Queue: processes normal priority tasks (FIFO)
    - Stack: tracks processed tasks for undo functionality (LIFO)
    """
    
    def __init__(self):
        self.all_tasks = []  # Array to store all tasks
        self.task_queue = deque()  # Queue for normal task processing
        self.processed_stack = []  # Stack for undo functionality
        self.next_id = 1
    
    def add_task(self, description, priority):
        """
        Add a new task with priority
        Priority: 1=High, 2=Medium, 3=Low
        """
        if priority not in [1, 2, 3]:
            print("❌ Invalid priority! Use 1 (High), 2 (Medium), or 3 (Low)")
            return None
        
        task = Task(self.next_id, description, priority)
        self.all_tasks.append(task)  # Store in array
        self.task_queue.append(task)  # Add to queue
        self.next_id += 1
        
        print(f"✅ Task added successfully: {task}")
        return task
    
    def enqueue_task(self, description, priority):
        """Alias for add_task - explicitly shows queue operation"""
        return self.add_task(description, priority)
    
    def dequeue_task(self):
        """
        Process the next task from queue (FIFO - First In First Out)
        Moves task from queue to processed stack
        """
        if not self.task_queue:
            print("⚠️  No tasks in queue to process!")
            return None
        
        # Get task based on priority (process high priority first)
        task = self._get_highest_priority_task()
        
        if task:
            task.status = "Completed"
            self.processed_stack.append(task)  # Push to stack for undo
            print(f"✅ Task processed: {task}")
            return task
        
        return None
    
    def _get_highest_priority_task(self):
        """Helper method to get highest priority pending task from queue"""
        if not self.task_queue:
            return None
        
        # Find task with highest priority (lowest number)
        highest_priority_task = min(self.task_queue, key=lambda t: t.priority)
        self.task_queue.remove(highest_priority_task)
        return highest_priority_task
    
    def undo_last_task(self):
        """
        Undo the last processed task (LIFO - Last In First Out)
        Moves task from processed stack back to queue
        """
        if not self.processed_stack:
            print("⚠️  No processed tasks to undo!")
            return None
        
        task = self.processed_stack.pop()  # Pop from stack
        task.status = "Pending"
        self.task_queue.append(task)  # Add back to queue
        
        print(f"↩️  Task undone: {task}")
        return task
    
    def display_all_tasks(self):
        """Display all tasks in the system"""
        if not self.all_tasks:
            print("\n📋 No tasks in the system")
            return
        
        print("\n" + "="*70)
        print("📋 ALL TASKS IN SYSTEM")
        print("="*70)
        
        for task in self.all_tasks:
            status_icon = "✅" if task.status == "Completed" else "⏳"
            print(f"{status_icon} {task}")
        
        print("="*70)
    
    def display_pending_tasks(self):
        """Display only pending tasks"""
        pending = [t for t in self.all_tasks if t.status == "Pending"]
        
        if not pending:
            print("\n⏳ No pending tasks")
            return
        
        print("\n" + "="*70)
        print("⏳ PENDING TASKS")
        print("="*70)
        
        # Sort by priority for display
        pending_sorted = sorted(pending, key=lambda t: t.priority)
        for task in pending_sorted:
            print(f"   {task}")
        
        print("="*70)
    
    def display_completed_tasks(self):
        """Display completed tasks (from stack)"""
        if not self.processed_stack:
            print("\n✅ No completed tasks")
            return
        
        print("\n" + "="*70)
        print("✅ COMPLETED TASKS (Stack - Last to First)")
        print("="*70)
        
        # Display in reverse order (most recent first - top of stack)
        for task in reversed(self.processed_stack):
            print(f"   {task}")
        
        print("="*70)
    
    def get_statistics(self):
        """Display system statistics"""
        total = len(self.all_tasks)
        pending = len([t for t in self.all_tasks if t.status == "Pending"])
        completed = len(self.processed_stack)
        
        print("\n" + "="*70)
        print("📊 TASK STATISTICS")
        print("="*70)
        print(f"Total Tasks: {total}")
        print(f"Pending Tasks: {pending}")
        print(f"Completed Tasks: {completed}")
        print(f"Tasks in Queue: {len(self.task_queue)}")
        print(f"Tasks in Stack: {len(self.processed_stack)}")
        print("="*70)


def demo():
    """Demonstrate the Smart Task Manager System"""
    print("\n🚀 SMART TASK MANAGER SYSTEM")
    print("=" * 70)
    
    manager = SmartTaskManager()
    
    # Add tasks with different priorities
    print("\n1️⃣  ADDING TASKS")
    print("-" * 70)
    manager.add_task("Complete project documentation", 1)  # High
    manager.add_task("Review pull requests", 2)  # Medium
    manager.add_task("Update team on progress", 1)  # High
    manager.add_task("Organize files", 3)  # Low
    manager.add_task("Schedule team meeting", 2)  # Medium
    
    # Display all tasks
    manager.display_all_tasks()
    manager.display_pending_tasks()
    
    # Process tasks (dequeue)
    print("\n2️⃣  PROCESSING TASKS (Dequeue)")
    print("-" * 70)
    manager.dequeue_task()  # Should process high priority first
    manager.dequeue_task()
    manager.dequeue_task()
    
    # Display status
    manager.display_pending_tasks()
    manager.display_completed_tasks()
    
    # Undo last task
    print("\n3️⃣  UNDO OPERATIONS (Stack)")
    print("-" * 70)
    manager.undo_last_task()  # Undo last processed
    manager.undo_last_task()  # Undo second last
    
    # Display status after undo
    manager.display_pending_tasks()
    manager.display_completed_tasks()
    
    # Process remaining tasks
    print("\n4️⃣  PROCESSING REMAINING TASKS")
    print("-" * 70)
    while manager.task_queue:
        manager.dequeue_task()
    
    # Final statistics
    manager.get_statistics()
    manager.display_all_tasks()
    
    print("\n✨ Demo completed!\n")


def interactive_mode():
    """Interactive command-line interface"""
    manager = SmartTaskManager()
    
    print("\n🚀 SMART TASK MANAGER - INTERACTIVE MODE")
    print("=" * 70)
    
    while True:
        print("\n📌 MENU:")
        print("1. Add Task (Enqueue)")
        print("2. Process Task (Dequeue)")
        print("3. Undo Last Task")
        print("4. Display All Tasks")
        print("5. Display Pending Tasks")
        print("6. Display Completed Tasks")
        print("7. Show Statistics")
        print("8. Exit")
        
        choice = input("\nEnter your choice (1-8): ").strip()
        
        if choice == "1":
            desc = input("Enter task description: ").strip()
            print("Priority: 1=High, 2=Medium, 3=Low")
            try:
                priority = int(input("Enter priority (1-3): ").strip())
                manager.enqueue_task(desc, priority)
            except ValueError:
                print("❌ Invalid input! Please enter a number.")
        
        elif choice == "2":
            manager.dequeue_task()
        
        elif choice == "3":
            manager.undo_last_task()
        
        elif choice == "4":
            manager.display_all_tasks()
        
        elif choice == "5":
            manager.display_pending_tasks()
        
        elif choice == "6":
            manager.display_completed_tasks()
        
        elif choice == "7":
            manager.get_statistics()
        
        elif choice == "8":
            print("\n👋 Thank you for using Smart Task Manager!")
            break
        
        else:
            print("❌ Invalid choice! Please enter 1-8.")


if __name__ == "__main__":
    # Run demo first
    demo()
    
    # Ask if user wants interactive mode
    response = input("Would you like to try interactive mode? (y/n): ").strip().lower()
    if response == 'y':
        interactive_mode()
