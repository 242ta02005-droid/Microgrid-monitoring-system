# Microgrid-monitoring-system
import time
import random

# -----------------------------
# Microgrid Parameters
# -----------------------------
BATTERY_CAPACITY = 5000       # Wh
battery = 2500                # Initial battery energy
LOW_BATTERY = 20              # %
HIGH_BATTERY = 90             # %

def solar_power():
    irradiance = random.uniform(200, 1000)  # W/m²
    panel_area = 5                           # m²
    efficiency = 0.20

    power = irradiance * panel_area * efficiency
    return power

def wind_power():
    wind_speed = random.uniform(2, 12)       # m/s

    if wind_speed < 3:
        return 0

    import math

    air_density = 1.225
    radius = 1
    cp = 0.35
    generator_efficiency = 0.85

    area = math.pi * radius ** 2

    power = (
        0.5
        * air_density
        * area
        * cp
        * wind_speed ** 3
        * generator_efficiency
    )

    return power

def display_status(solar, wind, load, battery, grid):
    battery_percent = (battery / BATTERY_CAPACITY) * 100

    print("\n" + "=" * 45)
    print("          MICROGRID MONITORING SYSTEM")
    print("=" * 45)

    print(f"Solar Generation : {solar:8.2f} W")
    print(f"Wind Generation  : {wind:8.2f} W")
    print(f"Total Generation : {solar + wind:8.2f} W")
    print(f"Load Demand      : {load:8.2f} W")
    print(f"Battery Level    : {battery_percent:8.1f} %")
    print(f"Grid Power       : {grid:8.2f} W")

    if battery_percent <= LOW_BATTERY:
        print("System Status    : LOW BATTERY")

    elif battery_percent >= HIGH_BATTERY:
        print("System Status    : BATTERY FULL")

    elif grid > 0:
        print("System Status    : IMPORTING FROM GRID")

    else:
        print("System Status    : NORMAL")

# -----------------------------
# Main Monitoring Loop
# -----------------------------

for cycle in range(20):

    solar = solar_power()
    wind = wind_power()

    # Simulated electrical load
    load = random.uniform(200, 1000)

    renewable_power = solar + wind

    # Difference between generation and load
    surplus = renewable_power - load

    grid_power = 0

    # -----------------------------
    # Battery Management
    # -----------------------------
    if surplus > 0:

        # Charge battery
        battery += surplus / 60

        if battery > BATTERY_CAPACITY:
            battery = BATTERY_CAPACITY

    else:

        # Discharge battery
        required_power = abs(surplus)

        available_power = battery * 60

        if available_power >= required_power:
            battery -= required_power / 60

        else:
            # Remaining demand supplied by grid
            grid_power = required_power - available_power
            battery = 0

    display_status(
        solar,
        wind,
        load,
        battery,
        grid_power
    )

    time.sleep(2)
