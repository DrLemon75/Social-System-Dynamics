# Social-System-Dynamics
Using Pyhton to build social system dynamics 
I intend to explore Pyhton potential for social system science 

# The following example explore the politicial social dynamics argentine presidential campaign.  A simple mathematical model assess the Argentine President Election campaign by using social dynamics principles. By this means, 29.595.039 electors (86, 2% of 34.332.992 that pertains to non-Peronism, that is, JxC and Libertad Avanza) choose among two antagonist ideas: A-C and B-D. A & B is spread by Javier Milei and are defined as follows: A: All “Casta” should be thrown out of the state; B: Dollarization shall prevail. In turns, C and D are spread by JxC: C: “Casta” are elected politicians needed to grant order; D: Dollarization is not feasible. Ideas A and C activate and spread  in society while ideas B and D are born as a change of appreciation from the initial positive reaction (switching support), that is to say that population initially agree with A and C ideas, but during the 1001 days duration of the campaign may switch and support B and D instead. The speed of time at which A and C is initially supported and B and C reaction emerge as a switching support feature is the key pivotal variable to predict the election outcome. 
# The model uses time-shape functions principles of DYNAMO built by Forrester, but using Pyhton language. By this means, sensitivity analysis is based on: campaign efforts parameters, reactions paramters, switching rates parameters, initial conditions, total electors, campaign duration, functional forms, campaign strategies, external events, feedback loop strength, and policy interventions.  
# Formulae values and forms shall be reshaped according to real prevailing conditions during the campaign. 

import numpy as np
import matplotlib.pyplot as plt

# Constants
total_electors = 29595039
campaign_days = 100
time = np.arange(0, campaign_days + 1)
initial_support = np.zeros(4)  # Initial support for A, B, C, D
initial_proportions = np.ones(4)  # Initial proportions of support

# Define custom functions for campaign efforts, reactions, and switching rates
def pulse(t, onset, duration, amplitude):
    return amplitude if onset <= t < onset + duration else 0

def ramp(t, onset, slope):
    return max(0, (t - onset) * slope)

def step(t, onset, amplitude):
    return amplitude if t >= onset else 0

# Define campaign efforts, reactions, and switching rates using the custom functions
def campaign_efforts(t):
    # Example using custom functions:
    return np.array([
        pulse(t, onset=20, duration=10, amplitude=0.1),
        ramp(t, onset=30, slope=0.01),
        step(t, onset=40, amplitude=0.15),
        pulse(t, onset=50, duration=15, amplitude=0.25),
    ])

def reactions(t):
    # Example using custom functions:
    return np.array([0.03, 0.02, 0.025, 0.035])

def switching_rates(t):
    # Example using custom functions:
    return np.array([0.005, 0.004, 0.005, 0.004])

# Initialize arrays to store support and proportions
support_history = np.zeros((len(time), 4))
proportion_history = np.zeros((len(time), 4))

support = initial_support.copy()
proportions = initial_proportions.copy()

# Simulation loop
for i, t in enumerate(time):
    # Calculate support and proportions at time t
    support_history[i] = support
    proportion_history[i] = proportions

    # Update support and proportions based on campaign efforts, reactions, and switching rates
    support += campaign_efforts(t) * proportions - reactions(t) * support
    proportions += switching_rates(t) * proportions

# Calculate the sum of A and C, and the sum of B and D
sum_A_C = support_history[:, 0] + support_history[:, 2]
sum_B_D = support_history[:, 1] + support_history[:, 3]

# Plot the results in two quadrants
plt.figure(figsize=(10, 6))
plt.subplot(2, 1, 1)  # First quadrant
plt.plot(time, sum_A_C, label='A + C')
plt.ylim(0, 1)
plt.legend()
plt.grid(True)
plt.title('Sum of Support for Ideas A and C')

plt.subplot(2, 1, 2)  # Second quadrant
plt.plot(time, sum_B_D, label='B + D')
plt.ylim(0, 1)
plt.legend()
plt.grid(True)
plt.title('Sum of Support for Ideas B and D')

plt.xlabel('Time (days)')
plt.tight_layout()
plt.show()
