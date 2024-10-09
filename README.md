# CI2024_lab1 - Report

## Summary
- Strategies ( and rapid explaination )
- My choice and why


## Strategies 

### 1. Hill Climbing with single modification tweak function  
Here the tweak function selects ( or removes ) an element
- PRO  
Simple tweak function  
Easy to reach a Local Optima

- CONS  
You need to start from a valid solution
Get Stuck to Local Optima

### 2. Hill Climbing with multiple modification tweak function    
Here the tweak function selects ( and/or remomes ) m elements, where m is the value k * UNIVERSE_SIZE  
 
- PRO  
Still simple tweak function  
It can escape from Local Optima  
You don't need to start from a valid solution is m is big enough

- CONS  
Takes more steps to reach a good solution than single modification tweak version  
Less efficient than single modifcation tweak version

### 3. Hill Climbing with Simulated Annealing
Here we select a new solution computing a probability p and comparing this probability to a random value r in the range (0, 1): if r < p we select the solution

- PRO  
It can escape from Local Optima ( as you can climb down based on the probability p )  
You don't need to start from a valid solution is

- CONS  
Slower than the single modification tweak version because you have to compute the probability p at each iteraction  
If the value to compute the probability does not adapt, it is difficult to obtain a better result than the other two models above

### 4. Hill Climbing with Simulated Annealing and self-adapting temperature

In this solution the parameter of the temperature is part of the tweak function. If we have a big value of temperature we're EXPLORING the solution space. Temperature become smaller when we find better solutions, and when temperature is small we're EXPLOITING the solution space

- PRO  
It can escape from Local Optima ( as you can climb down based on the probability p )  
You don't need to start from a valid solution

- CONS  
There are a lot of iterations ( for each iteration there are few internal steps )  
Initial value of T is not defined and it is very important for efficiency  

Plus I couldn't quite finish optimizing it properly (I needed to better balance the exploration and exploitation phases)


## My Choice and Why

After trying all these four models, i think a simple approach like the 'Hill Climbing with single modification tweak function' is a good compromise.  
It’s true that it gets stuck in the local optimal solution and needs a valid starting point, but it is the fastest (it doesn’t require too many calculations) and produces good results with few steps.   

Below are the analysis results with the various instances ( cost decimals are truncated for clarity )

1. Universe size: 100, Number of sets: 10, Density: 0.2    
![image 1](https://github.com/imEmaa/CI2024_lab1/blob/main/images/image-1.png)  
Initial cost: -316, Final cost: -316 ( last step: 0 )  
<br><br>

2. Universe size: 1'000, Number of sets: 100, Density: 0.2  
![image 2](https://github.com/imEmaa/CI2024_lab1/blob/main/images/image-2.png)  
Initial cost: -34070, Final cost: -7603 ( last step: 230 )  
<br><br>

3. Universe size: 10'000, Number of sets: 1'000, Density: 0.2  
![image 3](https://github.com/imEmaa/CI2024_lab1/blob/main/images/image-3.png)
Initial cost: -4279700, Final cost: -137692 ( last step: 4899 )  
<br><br>

4. Universe size: 100'000, Number of sets: 10'000, Density: 0.1  
![image 4](https://github.com/imEmaa/CI2024_lab1/blob/main/images/image-4.png)  
Initial cost: -34070, Final cost: -7603 ( last step: 230 )  
<br><br>

5. Universe size: 100'000, Number of sets: 10'000, Density: 0.2  
![image 5](https://github.com/imEmaa/CI2024_lab1/blob/main/images/image-5.png)
Initial cost: -538376501, Final cost: -2100487 ( last step: 66860 )  
<br><br>

6. Universe size: 100'000, Number of sets: 10'000, Density: 0.3  
![image 6](https://github.com/imEmaa/CI2024_lab1/blob/main/images/image-6.png)
Initial cost: -841012864, Final cost: -2188389 ( last step: 62922 )  

### Algorithm

## Hill Climbing with single mutation


- Utility  

        def valid(solution):
            """Checks wether solution is valid (ie. covers all universe)"""
            phenotype = np.all(np.logical_or.reduce(SETS[solution]))
            return phenotype

        def cardinality(solution):
            phenotype = np.logical_or.reduce(SETS[solution])
            return np.sum(phenotype)

        def cost(solution):
            """Returns the cost of a solution (to be minimized)"""
            return COSTS[solution].sum()

        def fitness(solution: np.ndarray):
            return (cardinality(solution), -cost(solution))

    
- Tweak function ( single_mutation )  

        def single_mutation(solution: np.ndarray) -> np.ndarray:
        new_solution = solution.copy()
        index = rng.integers(0, NUM_SETS)
        new_solution[index] = not new_solution[index]
        return new_solution  

- Algorithm  

        solution = rng.random(NUM_SETS) < 1
        sol_fitness = fitness(solution)
        ic ( sol_fitness)
        history = [sol_fitness[1]]

        tweak = single_mutation
        last_step = 0

        for steps in range(100_000):
            new_solution = tweak(solution)
            new_fitness = fitness(new_solution)
            history.append(new_fitness[1])
            
            if new_fitness > sol_fitness:
                solution = new_solution
                sol_fitness = new_fitness
                last_step = steps

        plt.figure(figsize=(14,8))
        plt.plot(
            range(len(history)),
            list(accumulate(history, max)),
            color='red'
        )
        _ = plt.scatter(range(len(history)), history, marker='.')

        ic (fitness(solution), last_step)
