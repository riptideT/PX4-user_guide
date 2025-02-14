# Simulate Failsafes

[Failsafes](../config/safety.md) define the safe limits/conditions under which you can safely use PX4, and the action that will be performed if a failsafe is triggered (for example, landing, holding position, or returning to a specified point).

In SITL some failsafes are disabled by default to enable easier simulation usage.
This topic explains how you can test safety-critical behavior in SITL simulation before attempting it in the real world.

:::note
You can also test failsafes using [HITL simulation](../simulation/hitl.md).
HITL uses the normal configuration parameters of your flight controller.
:::


## Data Link Loss

The *Data Link Loss* failsafe (unavailability of external data via MAVLink) is enabled by default.
This makes the simulation only usable with a connected GCS, SDK, or other MAVLink application.

Set the parameter [NAV_DLL_ACT](../advanced_config/parameter_reference.md#NAV_DLL_ACT) to the desired failsafe action to change the behavior. 
For example, set to `0` to disable it.

:::note
All parameters in SITL including this one get reset when you do `make clean`.
:::

## RC Link Loss

The *RC Link Loss* failsafe (unavailability of data from a remote control) is enabled by default.
This makes the simulation only usable with either an active MAVLink or remote control connection.

Set the parameter [NAV_RCL_ACT](../advanced_config/parameter_reference.md#NAV_RCL_ACT) to the desired failsafe action to change the behavior. 
For example, set to `0` to disable it.

:::note
All parameters in SITL including this one get reset when you do `make clean`.
:::

## Low Battery

The simulated battery is implemented to never run out of energy, and by default only depletes to 50% of its capacity and hence reported voltage.
This enables testing of battery indication in GCS UIs without triggering low battery reactions that might interrupt other testing.

To change this minimal battery percentage value use the parameter [SIM_BAT_MIN_PCT](../advanced_config/parameter_reference.md#SIM_BAT_MIN_PCT).

To control how fast the battery depletes to the minimal value use the parameter [SIM_BAT_DRAIN](../advanced_config/parameter_reference.md#SIM_BAT_DRAIN).

:::tip
By changing [SIM_BAT_MIN_PCT](../advanced_config/parameter_reference.md#SIM_BAT_MIN_PCT) in flight, you can also test regaining capacity to simulate inaccurate battery state estimation or in-air charging technology.
:::

## Sensor/System Failure

System failure injection can be used to simulate different types of failures in many sensors and systems (e.g. GPS, barometer, gyro, avoidance etc.):
1. Enable the parameter [SYS_FAILURE_EN](../advanced_config/parameter_reference.md#SYS_FAILURE_EN).
1. For example, to simulate losing and regaining GPS you might enter the following commands on the SITL instance *pxh shell* (or MAVLink console).
   ```bash
   # Turn (all) GPS off
   failure gps off
   
   # Turn (all) GPS on
   failure gps ok
   ```

The full syntax of the [failure](../modules/modules_command.md#failure) command is:
```
failure <component> <failure_type> [-i <instance_number>]
```
where:
- _component_: `gyro` | `accel` | `mag` | `baro` | `gps` | `optical_flow` | `vio` | `distance_sensor` | `airspeed` | `battery` | `motor` | `servo` | `avoidance` | `rc_signal` | `mavlink_signal`
- _failure_type_: `ok` | `off` | `stuck` | `garbage` | `wrong` | `slow` | `delayed` | `intermittent`
- _instance number_ (optional): Instance number of affected sensor.
   0 (default) indicates all sensors of specified type.   
