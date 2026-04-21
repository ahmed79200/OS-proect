class Process:
    def __init__(self, pid, burst, ptype):
        self.pid = pid
        self.burst = burst
        self.remaining = burst
        self.ptype = ptype
        self.finish_time = 0


def hybrid_scheduler(processes):
    time = 0
    queue = sorted(processes, key=lambda x: x.burst)
    finished = []

    print("=== Hybrid Smart Scheduler ===")

    while queue:
        p = queue.pop(0)

        if p.ptype == "IO":
            quantum = 2
        else:
            quantum = 5

        if p.remaining > quantum:
            print(f"P{p.pid} runs from {time} to {time + quantum}")
            time += quantum
            p.remaining -= quantum
            queue.append(p)
            queue.sort(key=lambda x: x.remaining)
        else:
            print(f"P{p.pid} runs from {time} to {time + p.remaining} (Finished)")
            time += p.remaining
            p.remaining = 0
            p.finish_time = time
            finished.append(p)

    calculate_metrics(finished)


def calculate_metrics(processes):
    total_waiting = 0
    total_turnaround = 0

    print("\n--- Metrics ---")

    for p in processes:
        turnaround = p.finish_time
        waiting = turnaround - p.burst

        total_waiting += waiting
        total_turnaround += turnaround

        print(f"P{p.pid}: Waiting={waiting}, Turnaround={turnaround}")

    n = len(processes)
    print(f"\nAverage Waiting Time = {total_waiting / n}")
    print(f"Average Turnaround Time = {total_turnaround / n}")


processes = [
    Process(1, 10, "CPU"),
    Process(2, 5, "IO"),
    Process(3, 8, "CPU"),
    Process(4, 6, "IO"),
]


hybrid_scheduler(processes)
