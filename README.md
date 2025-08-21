# Simulation-of-bidirectional-power-flow-between-electric-vehicles-and-the-grid
## AIM
To Simulate bidirectional power flow between electric vehicles and the grid in MATLAB 

## APPARATUS REQUIRED
•	MATLAB

## MATLAB CODING

% Bidirectional Power Flow Simulation between EV and Grid

clc; clear; close all;

%% Parameters

total_time = 24; % simulation time in hours

dt = 1/60; % time step in hours (1 minute)

time = 0:dt:total_time; % simulation time vector

battery_capacity_kWh = 60; % EV battery capacity

V_batt = 400; % EV nominal battery voltage (V)

SOC_initial = 0.5; % Initial state of charge (50%)

SOC = SOC_initial; % Initialize SOC

SOC_array = zeros(size(time));

P_ev = zeros(size(time)); % Power exchanged with grid

% SOC limits

SOC_min = 0.2; % Minimum SOC for V2G

SOC_max = 0.9; % Maximum SOC for charging

% Grid load demand profile (simulated)

P_load = 3e3 + 2e3 * sin(2 * pi * time / 24); % W

% Thresholds to decide mode

grid_demand_threshold = 4000; % above this, enable V2G

%% Simulation

for i = 1:length(time)

% Determine grid condition

if P_load(i) > grid_demand_threshold && SOC > SOC_min

% V2G mode (discharge EV to grid)

P_ev(i) = -3000; % discharge power in W

elseif P_load(i) < grid_demand_threshold && SOC < SOC_max

% G2V mode (charge EV from grid)

P_ev(i) = 3000; % charge power in W

else

P_ev(i) = 0; % idle

end

% Update SOC (convert power to energy: P × time)

energy_change_Wh = (P_ev(i) * dt); % Power (W) × Time (hr) = Energy (Wh)

delta_SOC = energy_change_Wh / (battery_capacity_kWh * 1000); % Normalize by total capacity in Wh

SOC = SOC + delta_SOC;

SOC = max(min(SOC, 1), 0); % Clamp between 0 and 1

SOC_array(i) = SOC; % store SOC

end

%% Plot Results

figure;

subplot(3,1,1);

plot(time, P_load/1000, 'k', 'LineWidth', 1.5);

ylabel('Grid Load (kW)');

title('Grid Load Profile'); grid on;

subplot(3,1,2);

plot(time, P_ev/1000, 'b', 'LineWidth', 1.5);

ylabel('EV Power (kW)');

title('EV Power Exchange (Positive = Charging, Negative = Discharging)');

grid on;

subplot(3,1,3);

plot(time, SOC_array * 100, 'r', 'LineWidth', 1.5);

ylabel('SOC (%)'); xlabel('Time (hours)');

title('EV Battery State of Charge');

grid on;

## Output

<img width="1600" height="801" alt="image" src="https://github.com/user-attachments/assets/8fbfed42-8aeb-4305-9c33-9532dc460bc1" />


## Result

The simulation successfully shows the EV exchanging power with the grid, charging during low demand and discharging during high demand, while maintaining the battery within safe SOC limits.
