# Bankers-algor
import numpy as np

def is_safe():
    """Check if the system is in a safe state."""
    work = available.copy()
    finish = [False] * n_processes
    safe_sequence = []

    while len(safe_sequence) < n_processes:
        allocated = False
        for i in range(n_processes):
            if not finish[i] and all(need[i] <= work):
                work += allocation[i]
                safe_sequence.append(i)
                finish[i] = True
                allocated = True
        if not allocated:
            return False, []
    
    return True, safe_sequence

def request_resources(process_id, request):
    """Request resources for a process dynamically."""
    global available, allocation, need

    if any(request > need[process_id]):
        print("Error: Process exceeded its maximum claim!")
        return False

    if any(request > available):
        print("Error: Resources not available!")
        return False

    # Temporarily allocate resources
    available -= request
    allocation[process_id] += request
    need[process_id] -= request

    # Check if the system is still in a safe state
    safe, sequence = is_safe()
    if safe:
        print(f"Request granted. Safe sequence: {sequence}")
        return True
    else:
        # Rollback allocation
        available += request
        allocation[process_id] -= request
        need[process_id] += request
        print("Error: Request would leave system in an unsafe state!")
        return False

# User input for number of processes and resources
n_processes = int(input("Enter number of processes: "))
n_resources = int(input("Enter number of resources: "))

# Input Available Resources
available = np.array(list(map(int, input(f"Enter available resources ({n_resources} values): ").split())))

# Input Maximum Demand Matrix
maximum = []
print("\nEnter maximum demand matrix (each row should have", n_resources, "values):")
for i in range(n_processes):
    maximum.append(list(map(int, input(f"Process {i}: ").split())))
maximum = np.array(maximum)

# Input Allocation Matrix
allocation = []
print("\nEnter allocation matrix (each row should have", n_resources, "values):")
for i in range(n_processes):
    allocation.append(list(map(int, input(f"Process {i}: ").split())))
allocation = np.array(allocation)

# Compute Need Matrix
need = maximum - allocation

# Real-time processing loop
while True:
    print("\nAvailable resources:", available)
    print("Allocation:\n", allocation)
    print("Need:\n", need)
    
    try:
        process_id = int(input("\nEnter process ID (0 to " + str(n_processes-1) + ") to request resources (or -1 to exit): "))
        if process_id == -1:
            break

        request = list(map(int, input("Enter resource request (e.g., 1 0 2): ").split()))

        if len(request) != n_resources:
            print("Error: Incorrect number of resources requested.")
            continue

        request_resources(process_id, np.array(request))

    except ValueError:
        print("Invalid input! Enter valid numbers.")

print("Exiting program.")
