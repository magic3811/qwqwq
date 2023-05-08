from random import shuffle, randint
import matplotlib.pyplot as plt

# problem parameters
num_jobs = 8
num_machines = 3
num_employed = 10
num_onlooker = 10
num_iterations = 100
num_trials = 10
shuffle_prob = 0.5
scout_limit = 10

jobs = [
    [(0, 3), (1, 2), (2, 2)],
    [(0, 2), (2, 1), (1, 4)],
    [(1, 4), (2, 3)]
]

# data structures
class Solution:
    def __init__(self, jobs):
        self.jobs = jobs
        self.makespan = None

def init_population(num_employed):
    population = []
    for i in range(num_employed):
        jobs = []
        for j in range(num_machines):
            job_order = list(range(num_jobs))
            shuffle(job_order)
            jobs.append(job_order)
        population.append(Solution(jobs))
    return population

def evaluate_solution(solution):
    start_times = [[0] * num_jobs for _ in range(num_machines)]
    end_times = [[0] * num_jobs for _ in range(num_machines)]
    for machine in range(num_machines):
        for job_index, job in enumerate(solution.jobs[machine]):
            job_machine, job_time = jobs[job]
            start_times[machine][job_index] = max(end_times[machine-1][job_index], end_times[machine][job_index-1])
            end_times[machine][job_index] = start_times[machine][job_index] + job_time
    makespan = end_times[-1][-1]
    solution.makespan = makespan
    for machine in range(num_machines):
        for job in solution.jobs[machine]:
            start_times[machine][job] = max(end_times[machine - 1][job], end_times[machine][job - 1]) if machine > 0 and job > 0 else 0
            end_times[machine][job] = start_times[machine][job] + jobs[job][machine][1]
    solution.makespan = max(end_times[machine][job] for machine in range(num_machines) for job in range(num_jobs))
    return makespan

def employed_bee_phase(population):
    for solution in population:
        machine = randint(0, num_machines - 1)
        job1, job2 = sorted([randint(0, num_jobs - 1), randint(0, num_jobs - 1)])
        if job1 != job2:
            new_solution = solution.jobs.copy()
            new_solution[machine][job1], new_solution[machine][job2] = new_solution[machine][job2], new_solution[machine][job1]
            new_solution = Solution(new_solution)
            evaluate_solution(new_solution)
            if new_solution.makespan < solution.makespan:
                solution.jobs = new_solution.jobs
                solution.makespan = new_solution.makespan

def onlooker_bee_phase(population):
    fitnesses = [1/solution.makespan for solution in population]
    total_fitness = sum(fitnesses)
    probabilities = [fitness/total_fitness for fitness in fitnesses]
    for i in range(num_onlooker):
        index1 = index2 = -1
        while index1 == index2:
            index1, index2 = [randint(0, num_employed - 1) for _ in range(2)]
        p1, p2 = population[index1], population[index2]
        machine = randint(0, num_machines - 1)
        job1, job2 = sorted([randint(0, num_jobs - 1), randint(0, num_jobs - 1)])
        if job1 != job2:
            new_solution = p1.jobs.copy()
            new_solution[machine][job1], new_solution[machine][job2] = new_solution[machine][job2], new_solution[machine][job1]
            new_solution = Solution(new_solution)
            evaluate_solution(new_solution)
            if new_solution.makespan < p1.makespan:
                p1.jobs = new_solution.jobs
                p1.makespan = new_solution.makespan
            else:
                new_solution = p2.jobs.copy()
                new_solution[machine][job1], new_solution[machine][job2] = new_solution[machine][job2], new_solution[machine][job1]
                new_solution = Solution(new_solution)
                evaluate_solution(new_solution)
                if new_solution.makespan < p2.makespan:
                    p2.jobs = new_solution.jobs
                    p2.makespan = new_solution.makespan

def scout_bee_phase(population):
    best_solution = min(population, key=lambda x: x.makespan)
    limit_count = 0
    for solution in population:
        if solution.makespan == best_solution.makespan:
            new_solution = Solution(init_population(1)[0].jobs)
            evaluate_solution(new_solution)
            solution.jobs = new_solution.jobs
            solution.makespan = new_solution.makespan
            limit_count += 1
            if limit_count >= scout_limit:
                break

# main algorithm
population = init_population(num_employed)
for _ in range(num_iterations):
    employed_bee_phase(population)
    onlooker_bee_phase(population)
    scout_bee_phase(population)

# display Gantt chart
start_times = [[0] * num_jobs for _ in range(num_machines)]
end_times = [[0] * num_jobs for _ in range(num_machines)]
for machine in range(num_machines):
    for job_index, job in enumerate(population[0].jobs[machine]):
        job_machine, job_time = jobs[job]
        start_times[machine][job_index] = max(end_times[machine-1][job_index], end_times[machine][job_index-1])
        end_times[machine][job_index] = start_times[machine][job_index] + job_time

fig, ax = plt.subplots()
colors = ['r', 'g', 'b']
for machine in range(num_machines):
    for job_index, job in enumerate(population[0].jobs[machine]):
        job_color = colors[job_machine]
        start_time = start_times[machine][job_index]
        duration = jobs[job][1]
        ax.broken_barh([(start_time, duration)], ((machine+1)*10, 9), facecolors=job_color)
        ax.text(start_time+(duration/2), (machine+1)*10+5, f'Job {job}', ha='center', va='center', color='w')
ax.set_ylim(0, (num_machines+1)*10)
ax.set_xlim(0, max(end_times[-1]))

plt.show()
