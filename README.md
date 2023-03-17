
import time
import board
import analogio

analog_pin = analogio.AnalogIn(board.A0)

every_value = set()

pot_pins = [analogio.AnalogIn(board.A0), analogio.AnalogIn(board.A1), analogio.AnalogIn(board.A2)]

min_values = [397]
max_values = [53381]

def clip(value, min, max):
    if (value > max):
        return max
    elif (value < min):
        return min
    else:
        return value

def normalize_value(pot_num):
    return clip((pot_pins[pot_num].value - min_values[pot_num]) / (max_values[pot_num] - min_values[pot_num]), 0, 1)


while True:

    current_val = analog_pin.value
    every_value.add(current_val)
    #print(f'{analog_pin.value} - {len(every_value)}')
    print(normalize_value(0))
    time.sleep(0.1)
