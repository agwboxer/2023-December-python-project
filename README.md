# 2023-December-python-project

Simulation of a simple pendulum in motion. Added option to graph and animate the pendulum with various parameters. 

Graded by Royal Holloway University of London Physics department. Grade: 80%

>## Dynamic Showcase Of The Simple Pendulum
>### Alexander Boxer PH2150 PS10 Report
>#### December 9, 2023

### 1 Abstract

The study of classical mechanics gives us insight to how a pendulum oscillates in the real world, wether that be with a damping and/or driving force the motion of a pendulum can be measured using Newtons laws of motion. In this report I will demonstrate two methods of estimating the motion of a pendulum given different circumstances that simulate the real world motion of the pendulum. Using ipywidgets the input values can be dynamic and change as requested to display a range of circumstances in which a pendulum may oscillate within.

### 2 Theory

It is widely known from Newton's second law of motion that \(F = m \cdot a\), this gives us the basis in which we can start to construct our equation for the motion of the pendulum. To derive the motion of a free pendulum, we require two forces acting upon the pendulum:
1. The force of gravity
2. The tension in the string

[force_diagram](https://github.com/user-attachments/assets/508e2087-3f8f-430d-a088-c7281cd875ab)


Fig 1 - Pendulum diagram depicting the forces acting upon it in free motion [1]
* $\(mg\)$: Force of gravity acting downward on the mass.
* $\(T\)$: Tension in the string.

The net force acting on the mass $F_{\text{net}}$ can be expressed as the vector sum of these forces: $$F_{\text{net}} = T - mg$$
This net force is responsible for the acceleration of the mass: $$a(t) = \frac{d^{2}\theta}{d^{2}t}$$
Resolving these forces in the $x$ and $y$ direction we retrieve: $$ F_{x} = -T * sin(\theta) $$ $$F_{y} = T * cos(\theta) - mg$$
Substituting this into $F = ma$ we can recover the differential equation for a pendulum in free motion [1]: $$\theta '' = -\frac{g}{L}sin(\theta)$$
By including a damping term which is proportional to the angular velocity of the pendulum and a constant driving force we can recover a full differential equation for the motion of the pendulum: $$\theta '' = - \frac{\beta}{m}\theta ' -\frac{g}{L}sin(\theta) + F_{d}$$ We can assume small angles of $\theta$ for $sin(\theta) \approx \theta$ this gives us our final differential equation: $$\theta '' + \frac{\beta}{m}\theta ' + \frac{g}{L}\theta = F_{d}$$ With this differential equation we can use the trial solution $\theta = e^{r\theta}$ and $\theta_p = \frac{L}{g} F_d$ as the particular integral for this nonhomogeneous differential equation: $$\theta = e^{at}(Asin(bt) + Bcos(bt)) + \frac{L}{g}F_d$$ Finding that $$ r = a + bi$$ $$a = -\frac{1}{2} \frac{\beta}{m}$$ $$b = \frac{1}{2}\sqrt{(\frac{\beta}{m})^{2} - 4\frac{g}{L}}$$
Where:
* $\beta$ = Damping constant
* $\omega$ = $\frac{d\theta}{dt}$
* $F_{d}$ = Driving force constant

With this information at hand I am tasked with solving this differential equation through two numerical methods, this required me to use the Euler and Runge Kutta method in my approach to determine the angular displacement and velocity of the pendulum. For the Euler method the general solution [2] $$\theta_{i}=\theta + dt * \frac{d^{2}\theta}{dt^{2}}$$ where $dt$ is a small change in time $(t)$. To compare this I used the Runge Kutta method, this involves using a step at the midpoint of an interval $(dt)$ to cancel out lower order error terms. The general formula used is [2] $$k_{1} = dt * f(\theta_n, \omega_n)$$ $$k_2 = dt * f(\theta_n + \frac{1}{2}dt, \omega_n + \frac{1}{2}k_1)$$ $$k_3 = dt * f(\theta_n + \frac{1}{2}dt, \omega_n + \frac{1}{2}k_2)$$ $$k_4 = dt * f(\theta_n + dt, \omega_n + k_3)$$ $$\theta_{n+1} = \theta_n + dt * (\frac{1}{6}k_1 + \frac{1}{3}k_2 + \frac{1}{3}k_3 + \frac{1}{6}k_4)$$ With these formulae we can now solve for the angular velocity and displacement of the pendulum.

### 3 Back end code

The back end is dedicated to handle the calculations of data and return tuples to be processed by the front end functions. The back end contains three classes all with its own unique function of which plays a role in preparing data for visulalisation on the front end. The main parameters we will be working with are:
* $theta_0$ (float): Initial angular position of the pendulum
* $omega_0$ (float): Initial angular velocity of the pendulum
* $L$ (float): Length of the pendulum
* $n$ (int): Number of time steps for simulation
* $damping$ (float): Damping factor affecting the motion
* $driving_force$ (float): External driving force applied

These variables are what the user can change through a slider on the front end (more on that later) these variables define the initial conditions and conditions in which the pendulum oscillates in which in turn determine further values of the angular displacement $(\theta)$ and angular velocity $(\omega)$. Calling upon the function *derivatives* with the initial conditions will be able to calculate the angular acceleration and set the constant terms within the differential equation. 

The *SimplePendulum* class contains methods to solve for the $\omega$ and $\theta$ therms, these include: 1. the Euler method and 2. the Runge Kutta method . Both methods within the *SimplePendulum* class will return values for $\theta$ and $\omega$ in a tuple which can be used to graphically represent the methods an enable a comparison between the three methods. In both numerical methods an empty list of length $n$ is created using the *np.zeros* command, which creates the empty lists *time_, omega_, theta_* all of which will contain the values of their respective names for all instances of zero. These tuples are then used within the iterative methods of numerical differentiation to return a full list each containing their respective values for all values of n.

The *PendulumAnimate* class takes use of the matplotlib.animation library where it is possible to create a live animation of the data that is calculated in *SimplePendulum* visually. This is able to plot the results of both the Euler and Runge Kutta method. Breaking the class down by each method. The *pendulum_position* method returns the Cartesian coordinates of the pendulum due to the fact that *matplotlib* displays plots in Cartesian coordinates, this uses the conversion from polar to Cartesian equations $$x = r * sin(\theta)$$ $$y = r * sin(\theta)$$ since we would like the pendulum to be at the bottom of the plot $y$ becomes $-y$ so it is more in line with real world observations. The *initial_pendulum* method dictates the initial position of the pendulum and plots a pendulum bob and string onto a graph in the initial position it also initialises the size and shape of the plot in which the animation will be in. The *animate* methods use the numerical methods to return a tuples containing the values of theta, these values are converted into cartesian coordinates and used to update the position of the pendulum bob and string. The *animate_plot* methods use the matplotlib.animation.FuncAnimation to retrieve all frames of the pendulum and convert it into a usable animation format which can be displayed on the front end.

### 4 Frond end code

The front end code contains all the necessary elements to present a user friendly interface that can be used to visualise the different methods of solving the pendulum equation. This cell, once executed, will display a drop-down in which the user can select between the types of method that will be displayed and animated, this drop down also updates the output and can change from Euler to Runge Kutta animations, for example, as requested. Once a method is selected the user will be shown a slider, here they will select the initial conditions in which the pendulum will oscillate, **Theta_0, Omega_0, L, n, damping, and driving force**. Once selected from a provided interval for each term, the user is prompted to run the code using a toggle button, once pressed a graph representing the movement of the pendulum will appear depicting both numerical methods, an animation of the pendulum with being solved using the selected method in the drop down, a value will be outputted as the average relative error between the two methods to determine how similar they are over the time interval selecetd.

### References
[1] Chakraborty A. Differential Equations; Accessed 10/12/2023. Available from: https://www.isical.ac.in/~arnabc/numana/diff1.html#:~:text=We%20can%20write%20the%20differential,y%E2%80%B2(t)).

[2] Casey A. PH2150 ODE. Accessed: 10/12/2023 Available from: PH2150 Moodle. 
