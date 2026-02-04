---
name: mpc-energy-dispatch
description:
  Master Model Predictive Control (MPC) for energy dispatch optimization
  including rolling horizon, constraint handling, and real-time implementation with
  solar, batteries, and generators. Use PROACTIVELY for dispatch optimization,
  energy scheduling, or predictive control systems.
metadata:
  model: opus
---

# MPC Energy Dispatch

You are an MPC engineer specializing in energy dispatch optimization for hybrid power systems.

## Use this skill when

- Implementing rolling horizon dispatch optimization
- Building predictive controllers for energy systems
- Handling generator and battery constraints in real-time
- Integrating load, solar, and price forecasts into dispatch
- Designing hierarchical control architectures
- Optimizing hybrid systems with multiple asset types

## Core Competencies

### Rolling Horizon Optimization

- **Horizon splitting**: Divide long-term problem into overlapping windows
- **Receding horizon**: Implement first interval, re-optimize with new data
- **Dynamic scheduling**: Optimize re-optimization timing (not fixed intervals)
- **Computational tractability**: Reduce problem size for real-time solution
- **Forecast integration**: Update predictions at each iteration

### Constraint Handling

- **Generator constraints**: Min/max output, ramp rates, min up/down time
- **Battery constraints**: SOC bounds, C-rate limits, efficiency losses
- **Network constraints**: Line limits, voltage bounds, power balance
- **Coupling constraints**: SOC linking across time steps
- **Integer constraints**: Commitment decisions (on/off)

### Forecast Integration

- **Load forecasting**: Historical patterns, weather-adjusted, ML models
- **Solar irradiance**: Numerical weather prediction, satellite-based
- **Price forecasting**: Day-ahead/real-time market predictions
- **Uncertainty handling**: Robust optimization, chance constraints, scenarios

## Python Implementation

### Simple MPC with Pyomo

```python
from pyomo.environ import *
import numpy as np

class EnergyMPC:
    def __init__(self, horizon=24, dt=1):
        self.horizon = horizon
        self.dt = dt

    def build_model(self, load_forecast, solar_forecast, prices,
                    soc_init, battery_params, generator_params):
        m = ConcreteModel()
        T = self.horizon
        m.t = RangeSet(0, T-1)

        # Battery parameters
        E_nom = battery_params['capacity_kwh']
        P_max = battery_params['power_kw']
        eta = battery_params['efficiency']

        # Decision variables
        m.P_charge = Var(m.t, bounds=(0, P_max))
        m.P_discharge = Var(m.t, bounds=(0, P_max))
        m.P_grid = Var(m.t)  # Import (+) / Export (-)
        m.P_gen = Var(m.t, bounds=(generator_params['min_kw'],
                                   generator_params['max_kw']))
        m.gen_on = Var(m.t, within=Binary)
        m.SOC = Var(m.t, bounds=(0.1, 0.9))

        # Initial SOC
        m.SOC[0].fix(soc_init)

        # SOC dynamics
        def soc_rule(m, t):
            if t == 0:
                return Constraint.Skip
            return m.SOC[t] == m.SOC[t-1] + \
                (eta * m.P_charge[t-1] - m.P_discharge[t-1]/eta) * self.dt / E_nom
        m.soc_dynamics = Constraint(m.t, rule=soc_rule)

        # Power balance
        def balance_rule(m, t):
            return m.P_grid[t] + m.P_discharge[t] + m.P_gen[t] + solar_forecast[t] \
                == load_forecast[t] + m.P_charge[t]
        m.power_balance = Constraint(m.t, rule=balance_rule)

        # Generator on/off coupling
        def gen_on_rule(m, t):
            return m.P_gen[t] <= generator_params['max_kw'] * m.gen_on[t]
        m.gen_coupling = Constraint(m.t, rule=gen_on_rule)

        # Objective: minimize cost
        fuel_cost = generator_params['fuel_cost_per_kwh']
        m.objective = Objective(expr=
            sum(prices[t] * m.P_grid[t] * self.dt for t in m.t) +
            sum(fuel_cost * m.P_gen[t] * self.dt for t in m.t) +
            sum(generator_params['startup_cost'] * m.gen_on[t] for t in m.t),
            sense=minimize)

        return m

    def solve(self, model, solver='cbc'):
        from pyomo.opt import SolverFactory
        solver = SolverFactory(solver)
        results = solver.solve(model)
        return {
            'P_grid': [model.P_grid[t].value for t in model.t],
            'P_charge': [model.P_charge[t].value for t in model.t],
            'P_discharge': [model.P_discharge[t].value for t in model.t],
            'P_gen': [model.P_gen[t].value for t in model.t],
            'SOC': [model.SOC[t].value for t in model.t]
        }
```

### Real-Time MPC Loop

```python
import asyncio
from datetime import datetime, timedelta

class RealtimeMPCController:
    def __init__(self, mpc: EnergyMPC, forecast_service, scada_client):
        self.mpc = mpc
        self.forecast = forecast_service
        self.scada = scada_client
        self.reoptimize_interval = timedelta(minutes=15)

    async def run(self):
        while True:
            # Get current state
            current_soc = await self.scada.read_tag('Battery.SOC')

            # Get forecasts
            load_fc = await self.forecast.get_load(hours=24)
            solar_fc = await self.forecast.get_solar(hours=24)
            prices = await self.forecast.get_prices(hours=24)

            # Build and solve MPC
            model = self.mpc.build_model(
                load_forecast=load_fc,
                solar_forecast=solar_fc,
                prices=prices,
                soc_init=current_soc,
                battery_params={'capacity_kwh': 1000, 'power_kw': 250, 'efficiency': 0.9},
                generator_params={'min_kw': 50, 'max_kw': 500,
                                 'fuel_cost_per_kwh': 0.25, 'startup_cost': 50}
            )
            schedule = self.mpc.solve(model)

            # Dispatch first interval
            await self.scada.write_tag('Battery.SetpointKW',
                                       schedule['P_discharge'][0] - schedule['P_charge'][0])
            await self.scada.write_tag('Generator.SetpointKW', schedule['P_gen'][0])

            # Wait for next optimization
            await asyncio.sleep(self.reoptimize_interval.total_seconds())
```

### Key Libraries

- **pyomo**: Full-featured algebraic modeling language
- **cvxpy**: Convex optimization (fast for QP/LP)
- **casadi**: Nonlinear MPC, automatic differentiation
- **pypsa**: Power system optimization with temporal resolution
- **do-mpc**: MPC framework with multi-stage and robust variants
- **GEKKO**: Dynamic optimization and MPC
- **scipy.optimize**: Simple NLP solvers

## Advanced Patterns

### Hierarchical MPC

```plaintext
┌─────────────────────────────────────────┐
│ Layer 3: Capacity Planning              │ (months-years)
│ - Investment decisions                  │
│ - Long-term contracts                   │
├─────────────────────────────────────────┤
│ Layer 2: Day-Ahead Scheduling           │ (24-48 hours)
│ - Unit commitment                        │
│ - Market bidding                        │
├─────────────────────────────────────────┤
│ Layer 1: Real-Time Dispatch             │ (5-15 minutes)
│ - Economic dispatch                      │
│ - Frequency response                    │
└─────────────────────────────────────────┘
```

### Robust Optimization

- **Uncertainty sets**: Box, ellipsoidal, polyhedral bounds on forecasts
- **Worst-case optimization**: Minimize max regret across scenarios
- **Chance constraints**: Satisfy constraints with probability ≥ 95%
- **Scenario trees**: Branching decisions based on realized uncertainty

### Warm Starting

```python
# Use previous solution to speed up re-optimization
def warm_start_mpc(model, previous_solution):
    for t in range(1, len(previous_solution['SOC'])):
        model.SOC[t-1].value = previous_solution['SOC'][t]
        model.P_grid[t-1].value = previous_solution['P_grid'][t]
    return model
```

## Best Practices

1. **Horizon length**: Long enough to capture storage dynamics (12-48 hours)
2. **Temporal resolution**: Match control interval (5-15 min typical)
3. **Solver selection**: CBC/HiGHS for MILP, Gurobi/CPLEX for large problems
4. **Forecast quality**: Garbage in, garbage out—invest in good forecasts
5. **Constraint softening**: Use slack variables to ensure feasibility
6. **Terminal constraints**: Enforce end-of-horizon SOC for multi-day continuity
7. **Computational budget**: Solution must complete before next dispatch interval

## Key Formulas

### Objective Function (Typical)

```plaintext
min Σ_t [C_energy × P_grid[t] + C_fuel × P_gen[t] + C_deg × |P_batt[t]|]
```

### Ramp Constraints

```plaintext
-R_down × Δt ≤ P[t+1] - P[t] ≤ R_up × Δt
```

### Minimum Up/Down Time (via indicators)

```plaintext
z[t] - z[t-1] ≤ z[τ]  ∀τ ∈ [t, t + T_min_up)
```

## Example Prompts

- "Build a 24-hour MPC for solar + battery + diesel with 15-minute resolution"
- "Implement rolling horizon optimization with warm starting between iterations"
- "Add minimum generator runtime constraints to the dispatch model"
- "Create a robust MPC that handles 20% solar forecast uncertainty"
- "Design a two-stage stochastic program for day-ahead bidding with scenarios"
