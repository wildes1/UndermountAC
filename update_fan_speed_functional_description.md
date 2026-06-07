# Functional Description of `update_fan_speed` Script

## Overview
The `update_fan_speed` script dynamically controls the blower fan speed in an ESPHome-based HVAC system for a van's under-mount air conditioning setup. It supports automatic proportional speed adjustment based on temperature differences, manual fixed-speed modes, and includes safety interlocks and cooldown mechanisms to prevent rapid changes that could cause audible noise or wear.

The script is triggered on every thermostat state change (`on_state`) and when compressor stages change (e.g., `compressor_speed_high` turn on/off).

## Key Components

### Temperature Difference Calculation (`diff`)
The script calculates the temperature difference (`diff`) only when the thermostat is actively demanding heating or cooling. This ensures fan speed responds to actual demand rather than setpoint proximity.

- **Heating Mode**: If `this_thermostat.action == CLIMATE_ACTION_HEATING` and `current_temperature < target_temperature_low`, then `diff = |current_temperature - target_temperature_low|`.
- **Cooling Mode**: If `this_thermostat.action == CLIMATE_ACTION_COOLING` and `current_temperature > target_temperature_high`, then `diff = |current_temperature - target_temperature_high|`.
- **Other Cases**: `diff = 0` (e.g., when idle, off, or within deadband).

This calculation focuses on the deviation from the relevant setpoint, providing a measure of how "hard" the system needs to work.

### Climate Action (`climate_action`)
`climate_action` is a custom global integer variable that mirrors and extends the ESPHome thermostat's action state. It is set in various automation scripts to track the system's operational mode.

- **0 (Off)**: System is off. Fan is turned off if cooldown allows.
- **2 (Cool)**: Cooling mode active.
- **3 (Heat)**: Heating mode active.
- **4 (Idle)**: System is idle (e.g., after a cycle, waiting for next action).
- **6 (Fan Only)**: Fan-only mode, blower runs without heating/cooling.

Note: While ESPHome provides `CLIMATE_ACTION_*` constants (e.g., `CLIMATE_ACTION_OFF`, `CLIMATE_ACTION_COOLING`), `climate_action` is a custom variable set in lambdas (e.g., `id(climate_action) = 2;` in cooling start actions). It may not always perfectly align with the thermostat's `action` due to custom logic.

### Hysteresis and Proportional Mapping
Fan speed uses a hysteresis band to avoid oscillations:
- **Base Difference (`supplemental_diff`)**: Set to `${supplemental_heating_diff}` or `${supplemental_cooling_diff}` depending on mode (from substitutions).
- **Lower Bound**: `supplemental_diff - ${fan_hysteresis}` (default 0.4°C).
- **Upper Bound**: `supplemental_diff + ${fan_hysteresis}`.

Speed mapping:
- If `diff <= lower_bound`, target_speed = 0%.
- If `diff >= upper_bound`, target_speed = 100%.
- Else, linear interpolation: `target_speed = round(((diff - lower_bound) / (upper_bound - lower_bound)) * 100)`.

This provides smooth, proportional control within the band.

### Supplemental Cooling Control
For cooling only (`CLIMATE_ACTION_COOLING`):
- Enable `supplemental_enabled` if `diff > ${supplemental_cooling_diff}`.
- Disable if `diff < ${supplemental_cooling_diff} - 0.25`.

This allows staged cooling (e.g., compressor speed high) based on demand.

### Safety Interlocks
- **Compressor Active**: If `air_cond` (compressor) is on, ensures `current_fan >= 20` and blower is on.
- **Minimum Speed**: Prevents fan from dropping below 20% when compressor is running.

### Change Cooldown
To avoid audible speed changes:
- `fan_change_cooldown_minutes` (substitution, default 3 min).
- `last_fan_change_time` (global timestamp).
- Speed only changes if `current_time - last_fan_change_time >= cooldown`.

### Manual Fan Modes
When `fan_mode != AUTO`:
- **LOW**: 15%
- **MEDIUM**: 40%
- **HIGH**: 100%
- Cooldown applies to prevent rapid switches.

### Other Variables
- **`current_fan`**: Current fan speed (0-100).
- **`last_fan_change_time`**: Timestamp of last speed change.
- **`supplemental_diff`**: Current base diff for hysteresis (set in heating/cooling actions).
- **`MIN_BLOWER`**: 20 (minimum when compressor on).
- **`can_change`**: Boolean for cooldown check.

### Flow Summary
1. Compute `diff` based on action and temperatures.
2. Check safety (compressor interlock).
3. If `climate_action == 0`, turn off fan (with cooldown).
4. Else, calculate `target_speed` proportionally.
5. Handle supplemental cooling.
6. Apply change only if different and cooldown elapsed.
7. Handle manual modes similarly.
8. Update diagnostics and log.

This design balances responsiveness with stability, preventing hunting while providing fine-grained control.