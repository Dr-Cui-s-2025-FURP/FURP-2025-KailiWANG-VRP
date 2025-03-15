<span style="font-size:20px;">Vehicle Routing Problem (VRP)</span>

VRP is a classic problem in operations research and logistics optimization, which aims to optimize the routes of multiple vehicles to deliver goods from one or more warehouses (distribution centers) to multiple customer points. The goal is usually to minimize the total driving distance, total cost, total time or carbon emissions, while satisfying vehicle load, time window, path constraints and other conditions.

（1）VRP has many variants, which can be divided into the following categories according to different application scenarios and constraints:

1. Basic VRP (CVRP, Capacitated VRP)：
The vehicle has a fixed load capacity and must ensure that the total demand does not exceed the load.
2. Time Window VRP (VRPTW, VRP with Time Windows)：
The vehicle must arrive at the customer point within a specified time window.
3. Multi-Depot VRP (MDVRP, Multi-Depot VRP)：
The vehicle can depart from multiple warehouses instead of a single distribution center.
4. Dynamic VRP (DVRP, Dynamic VRP)：
Order demand or traffic conditions may change during the distribution process, and the route needs to be adjusted dynamically.
5. VRP with Drones：
Combining UAVs and ground vehicles for last-mile delivery optimization (such as your project).

（2）Solution
VRP is an NP-hard problem that cannot be solved efficiently by a simple greedy algorithm, so the following methods are usually used:

1. Mathematical optimization (MILP, Mixed Integer Linear Programming): Applicable to small-scale problems.
2. Metaheuristics: Such as genetic algorithm (GA), simulated annealing (SA), ant colony optimization (ACO), taboo search (TS), etc., applicable to large-scale problems.
3. Reinforcement learning (RL) & deep learning (DL): used to solve more complex VRP variants in recent years.

The current research "VRP Problem Combining UAVs and Autonomous Vehicles" belongs to multi-modal VRP, involving UAV-ground vehicle collaborative optimization and time-energy joint optimization, which is a cutting-edge optimization problem.

<span style="font-size:20px;">Week 1: Formulate time-energy joint cost function</span>

<span style="font-size:18px;">Outline</span>:     The goal of this week is to optimize the Vehicle Routing Problem (VRP) by minimizing a joint cost function. The cost function includes both time and energy consumption, which means that the impact of both needs to be considered simultaneously and their balance needs to be found.

        Step 1: Define the time cost function

Time cost usually refers to the time it takes for a vehicle (autonomous cars and drones) to get from one point to another. It usually depends on distance, speed, traffic conditions, etc.
            
(1) Drone: The flight time can be calculated by the relationship between the flight distance and the flight speed.
$$
t_{\text{drone}} = \frac{d_{\text{drone}}}{v_{\text{drone}}}
$$            
Plus: Drone flight time may be affected by flight altitude, wind speed and other external environmental factors.

(2) Autonomous Vehicle: Driving time is usually calculated by driving distance and vehicle speed, but the speed may vary due to the influence of urban traffic.

$$
t_{\text{vehicle}} = \frac{d_{\text{vehicle}}}{v_{\text{vehicle}}}
$$

Plus: For autonomous vehicles in cities, it may also be necessary to consider traffic models (for example, using SUMO for traffic simulation) to dynamically adjust the vehicle speed.

        Step 2: Define energy cost function

(1)Drone: Energy consumption of drone is related to flight time as well as factors such as air resistance, load, and flight altitude.

$$
E_{\text{drone}} = P_{\text{drone}} \cdot t_{\text{drone}}
$$

Among them, P is the power consumption of the drone, and T is the flight time.

(2)Autonomous Vehicle: Energy consumption is related to speed, load, etc. The energy model may be:

$$
E_{\text{vehicle}} = \frac{d_{\text{vehicle}}}{\text{EnergyEff}}
$$


        Step 3: Define combined cost function

Combining time and energy costs into a comprehensive cost function allows both factors to be considered when optimizing the path. A weighting factor can be used to balance the importance of time and energy:

$$
C_{\text{total}} = \alpha \cdot C_{\text{time}} + \beta \cdot C_{\text{energy}}
$$

α and β are weight coefficients used to balance the importance of time and energy. 

        Step 4: Creating MILP Model

Through the above formula analysis, a joint time-energy optimization MILP model is established to determine whether the task should be performed by a drone or an autonomous vehicle to minimize the total cost. PuLP is used for modeling, and solve() is used to solve the MILP problem to obtain the optimal task allocation strategy. Finally, the model outputs the optimal path selection and total cost.

<span style="font-size:18px;">Variables & Constraints:</span>
                
(1) Decision variables:

        x[i] → Whether the UAV performs task i (0 or 1)

        y[i] → Whether the vehicle performs task i (0 or 1)

(2) Objective function:

$$
C_{\text{total}} = \alpha \cdot C_{\text{time}} + \beta \cdot C_{\text{energy}}
$$

α and β represent the importance weights of time and energy respectively.

Total_Time is obtained by summing the time required for task execution.

Total_Energy is calculated by energy consumption.

(3) Constraints:

1. Tasks must be assigned: 
                
    Task must be performed by drones or vehicles, and cannot be handled unmanned.
            
            x[i] + y[i] = 1, ∀i ∈ Tasks

2. Path selection constraints:

    The drone and vehicle paths for each task can only be selected 0 or 1 times.

            0 ≤ x[i] ≤ 1, 0 ≤ y[i] ≤ 1

3. Optimization constraints:

    a. Time windows can be added (tasks must be completed within a specific time range).
            
    b. Vehicle capacity constraints (a single transport cannot be overloaded).

    Plus: matplotlib is used to plot the number of tasks vs total cost line chart and a bar graph of different weights and task allocations to help intuitively understand the performance of the optimization model.


<span style="font-size:18px;">Specific code function analysis: </span>

   <pre>
<code class="language-python">
# 这是一个 Python 代码示例
def hello_world():
    print("Hello, World!")

hello_world()
</code>
</pre>
                 