
import time
import board
import analogio
from joystick_xl.inputs import Axis, Button, Hat, VirtualInput
from joystick_xl.joystick import Joystick

every_value = set()

pot_pins = [analogio.AnalogIn(board.A0), analogio.AnalogIn(board.A1), analogio.AnalogIn(board.A2)]

num_axes = 3

min_values = [397, 397, 397]
max_values = [53381, 53381, 53381]
raw_current_val = [398, 20500, 53000]
normalized_current_val = [0] * num_axes

dumb_wrappers = [VirtualInput(32768), VirtualInput(32768), VirtualInput(32768)]

joystick = Joystick()

def clip(value, min=0, max=1.0):
    if (value > max):
        return max
    elif (value < min):
        return min
    else:
        return value

def normalize_value(val, min, max):
    return clip((val - min) / (max - min))

def range_map(x, in_min, in_max, out_min=0, out_max=(1 << 16) - 1):
    return (x - in_min) * (out_max - out_min) // (in_max - in_min) + out_min

flip_flop = False

#joystick.add_input(Axis(dumb_wrappers[0]))
#joystick.add_input(Axis(dumb_wrappers[1]))
#joystick.add_input(Axis(dumb_wrappers[2]))

joystick.add_input(
   Axis(dumb_wrappers[0]),
   Axis(dumb_wrappers[1]),
   Axis(dumb_wrappers[2]),
)

while True:
    for i, pin in enumerate(pot_pins):
        raw_current_val[i] = pin.value
        normalized_current_val[i] = normalize_value(raw_current_val[i], min_values[i], max_values[i])
        dumb_wrappers[i].value = int(range_map(normalized_current_val[i], 0, 1.0, 0, (1 << 16) - 1))
        #every_value.add(current_val)
        #print(f'{analog_pin.value} - {len(every_value)}')
        #print(normalize_value(0))

    print(f'{raw_current_val}')


    joystick.update()
    time.sleep(0.1)
