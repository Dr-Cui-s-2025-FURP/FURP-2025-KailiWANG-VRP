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

1. First import the required libraries   

<pre>
<code class="language-python">
!pip install pulp  
import pulp  
import numpy as np

</code>
</pre>

pulp: An optimization library for solving linear programming (LP) and integer linear programming (MILP) problems.

2. Next, define the parameters of the drone and autonomous vehicle：

<pre>
<code class="language-python">
drone_speed = 10  # velocity of drone (km/h)
vehicle_speed = 30  # velocity of autonomous vehicle (km/h)
drone_power = 50  # Drone power consumption (watts)
vehicle_efficiency = 0.2  # Fuel efficiency of autonomous vehicles (L/km)
fuel_eff = 1  # Assumed fuel consumption (unit: L/km)
distance_drone = [10, 20, 15]  # Distance flown by the drone (in kilometers)
distance_vehicle = [5, 10, 12]  # Distance travelled by car (in kilometers)

</code>
</pre>

（1）Speed: Drones are slower (10 km/h), autonomous vehicles are faster (30 km/h).

（2）Energy consumption: Drones calculate energy consumption based on power × time (Watt-hours).
Vehicles calculate energy consumption based on fuel efficiency × distance (liters/L).

（3）Mission: Drones and vehicles each have 3 optional paths.


3. Calculate time cost

<pre>
<code class="language-python">
time_drone = [d / drone_speed for d in distance_drone]  # flight time of drone
time_vehicle = [d / vehicle_speed for d in distance_vehicle]  # travel time of UAVs
</code>
</pre>

4. Calculating energy consumption

<pre>
<code class="language-python">
energy_drone = [drone_power * t for t in time_drone]  # energy consumption of drone（Watt-hour）
energy_vehicle = [d * vehicle_efficiency for d in distance_vehicle]  # energy consumption of UAVs（L）

</code>
</pre>

5. Creating a MILP Planning Problem

<pre>
<code class="language-python">
model = pulp.LpProblem("Vehicle_Routing_Problem", pulp.LpMinimize)
</code>
</pre>

The goal is to minimize the time-energy cost, so set LpMinimize

6. Defining decision variables

<pre>
<code class="language-python">
x = pulp.LpVariable.dicts("drone", range(len(distance_drone)), cat="Binary")
y = pulp.LpVariable.dicts("vehicle", range(len(distance_vehicle)), cat="Binary")

</code>
</pre>

(1) x[i]: If the drone selects the i-th path, then x[i] = 1, otherwise x[i] = 0.

(2) y[j]: If the vehicle selects the j-th path, then y[j] = 1, otherwise y[j] = 0.

(3) Binary variable (cat="Binary"), meaning that each path can only be selected or not.

7. Setting the objective function

<pre>
<code class="language-python">
alpha = 0.5  # weight of time
beta = 0.5  # weight of energy
total_time = pulp.lpSum([x[i] * time_drone[i] for i in range(len(distance_drone))])
total_energy = pulp.lpSum([x[i] * energy_drone[i] for i in range(len(distance_drone))])
total_vehicle_time = pulp.lpSum([y[j] * time_vehicle[j] for j in range(len(distance_vehicle))])
total_vehicle_energy = pulp.lpSum([y[j] * energy_vehicle[j] for j in range(len(distance_vehicle))])

model += alpha * (total_time + total_vehicle_time) + beta * (total_energy + total_vehicle_energy), "Total_Cost"
</code>
</pre>

(1) total_time: the time taken by all selected paths.

(2) total_energy: the energy consumption of all selected paths.

(3) $\alpha$ and $\beta$ control the weights of time and energy consumption.

Objective function: 
$ \min \left( \alpha \sum_{i} x_i t_{\text{drone},i} + \beta \sum_{i} x_i E_{\text{drone},i} + \alpha \sum_{j} y_j t_{\text{vehicle},j} + \beta \sum_{j} y_j E_{\text{vehicle},j} \right) $

8. Setting constraints

<pre>
<code class="language-python">
for i in range(len(distance_drone)):
    model += x[i] <= 1, f"Drone_Path_Constraint_{i}"

for j in range(len(distance_vehicle)):
    model += y[j] <= 1, f"Vehicle_Path_Constraint_{j}"

model += pulp.lpSum([x[i] for i in range(len(distance_drone))]) + pulp.lpSum([y[j] for j in range(len(distance_vehicle))]) == 1, "Task_Assignment"

</code>
</pre>

Each task can only be executed by one drone or one car at most.

pulp.lpSum(x) + pulp.lpSum(y) == 1 ensures that all tasks must be executed.

9. Solve and output the results

<pre>
<code class="language-python">
model.solve()

print("Status:", pulp.LpStatus[model.status])
print("Total cost: ", pulp.value(model.objective))

for i in range(len(distance_drone)):
    print(f"Whether the drone uses path {i}: {x[i].varValue}")

for j in range(len(distance_vehicle)):
    print(f"Whether the car uses the road {j}: {y[j].varValue}")

</code>
</pre>

The core of this code is to solve the hybrid drone-autonomous driving VRP problem and minimize the time and energy cost. You can adjust alpha and beta or the number of targets to change the focus of the optimization target, and the selection results and total cost will also be different.


The first figure shows the goal allocation of drones and UAVs after changing the original time and energy weights of 0.5 to 0.7 and 0.3 respectively.
![column chart](./images/Column%20Chart.png)


The second figure demonstrates the total cost increases as the number of targets increases. 
![line chart](./images/Line%20Chart.png)

Key findings

(1) The more tasks there are, the higher the total cost will be, but the growth rate may not be completely linear due to factors such as path optimization and resource scheduling.

(2) The weight adjustment of time (alpha) and energy (beta) will affect the scheduling decision, for example:

1. The increase of alpha (time is more important) → vehicles take on tasks first (due to high speed).

2. The increase of beta (energy is more important) → drones participate more (due to energy saving).

The MILP solution can dynamically allocate tasks to ensure the optimal cost.