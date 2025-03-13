Week 1: Formulate time-energy joint cost function

    Outline: The goal of this week is to optimize the Vehicle Routing Problem (VRP) by minimizing a joint cost function. The cost function includes both time and energy consumption, which means that the impact of both needs to be considered simultaneously and their balance needs to be found.

        Step 1: Define the time cost function

            Time cost usually refers to the time it takes for a vehicle (autonomous cars and drones) to get from one point to another. It usually depends on distance, speed, traffic conditions, etc.
            
            (1)Drone: The flight time can be calculated by the relationship between the flight distance and the flight speed.
                
![Drone Flight Time](./images/1.jpg "Drone Flight Time Calculation")
            
            Plus: Drone flight time may be affected by flight altitude, wind speed and other external environmental factors.

            (2)Autonomous Vehicle: Driving time is usually calculated by driving distance and vehicle speed, but the speed may vary due to the influence of urban traffic.

![Autonomous Vehicle Driving Time](./images/2.jpg "Autonomous Vehicle Driving Time")

            Plus: For autonomous vehicles in cities, it may also be necessary to consider traffic models (for example, using SUMO for traffic simulation) to dynamically adjust the vehicle speed.

        Step 2: Define energy cost function

            (1)Drone: Energy consumption of drone is related to flight time as well as factors such as air resistance, load, and flight altitude.

![Energy of Drone](./images/3.jpg "Energy of Drone")

            Among them, P is the power consumption of the drone, and T is the flight time.

            (2)Autonomous Vehicle: Energy consumption is related to speed, load, etc. The energy model may be:

![Energy of Autonomous Vehicle](./images/4.jpg "Energy of Autonomous Vehicle")

        Step 3: Define combined cost function

            Combining time and energy costs into a comprehensive cost function allows both factors to be considered when optimizing the path. A weighting factor can be used to balance the importance of time and energy:

![Weight of Total Cost](./images/5.jpg "Weight of Total Cost")

            α and β are weight coefficients used to balance the importance of time and energy. 