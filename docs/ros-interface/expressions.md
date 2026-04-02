# Facial Expressions

## Overview

The Navi robot displays facial expressions on its screen (the Robot Display Screen connected via Ethernet). Expressions are controlled by setting the `/smiley_param` ROS parameter, which is read by the facial display module and rendered on screen.

## ROS Parameter

```bash
rosparam set /smiley_param <expression_name>
```

## Available Expressions

| Expression Name | Description |
|---|---|
| `smiling` | Default friendly smile |
| `receiving_audio` | Listening / hearing indicator |
| `thinking` | Processing / thinking state |
| `talking` | Speaking indicator |
| `waving` | Greeting gesture |
| `lowbattery` | Low battery warning |
| `charging` | Charging in progress |

## Examples

### Smiling

```bash
rosparam set /smiley_param smiling
```

### Listening

```bash
rosparam set /smiley_param receiving_audio
```

### Thinking

```bash
rosparam set /smiley_param thinking
```

### Talking

```bash
rosparam set /smiley_param talking
```

### Low Battery

```bash
rosparam set /smiley_param lowbattery
```

## Feedback to Tablet

The facial expression state is also published to the companion tablet via the `/feedback_to_tablet` topic, allowing the app to mirror the robot's current expression state:

```bash
rostopic echo /feedback_to_tablet
```

## Verifying the Current Expression

```bash
rosparam get /smiley_param
```
