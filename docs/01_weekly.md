Week 1: Formulate time-energy joint cost function

    Outline: The goal of this week is to optimize the Vehicle Routing Problem (VRP) by minimizing a joint cost function. The cost function includes both time and energy consumption, which means that the impact of both needs to be considered simultaneously and their balance needs to be found.

        Step 1: Define the time cost function

            Time cost usually refers to the time it takes for a vehicle (autonomous cars and drones) to get from one point to another. It usually depends on distance, speed, traffic conditions, etc.
            
            (1)Drone: The flight time can be calculated by the relationship between the flight distance and the flight speed.
                
                ![1](./images/1.jpg)
            
            Plus: Drone flight time may be affected by flight altitude, wind speed and other external environmental factors.

            (2)Autonomous Vehicle: Driving time is usually calculated by driving distance and vehicle speed, but the speed may vary due to the influence of urban traffic.

                ![2](./images/2.jpg)

            Plus: For autonomous vehicles in cities, it may also be necessary to consider traffic models (for example, using SUMO for traffic simulation) to dynamically adjust the vehicle speed.

        Step 2: Define energy cost function

            (1)Drone: Energy consumption of drone is related to flight time as well as factors such as air resistance, load, and flight altitude.

                ![3](./images/3.jpg)

            Among them, P is the power consumption of the drone, and T is the flight time.

            (2)Autonomous Vehicle: Energy consumption is related to speed, load, etc. The energy model may be:

                ![4](./images/4.jpg)

        Step 3: Define combined cost function

            Combining time and energy costs into a comprehensive cost function allows both factors to be considered when optimizing the path. A weighting factor can be used to balance the importance of time and energy:

                ![5](./images/5.jpg)

            α and β are weight coefficients used to balance the importance of time and energy. 