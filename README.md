Hoja-de-Trabajo-5
=================
"""
Bank renege example

Covers:

- Resources: Resource
- Condition events

Scenario:
  A counter with a random service time and customers who renege. Based on the
  program bank08.py from TheBank tutorial of SimPy 2. (KGM)

"""
import random

import simpy


RANDOM_SEED = 42
NEW_PROCESS = 30  # Numero total de procesos
INTERVAL_PROCESS = 10.0  # Genera los nuevos procesos cada X tiempo
MIN_INSTRUCTIONS = 1  # Min. number of instructions
MAX_INSTRUCTIONS = 10  # Max. number of instructions
MIN_RAM = 1  # Min. number of ram required
MAX_RAM = 10 # Max. number of ram required

def source(env, number, interval, counter):
    """Source genera los procesos de forma aleatoria"""

    with RAM.request() as req:
        cantRAM = random.uniform(MIN_RAM, MAX_RAM)
        
        yield req
        
        for i in range(number):
            c = prcs(env, 'Process%02d' % i, counter, time_in_process=10.0)
            env.process(c)
            t = random.expovariate(1.0 / interval)
            yield env.timeout(t)

def prcs(env, name, counter, time_in_process):
    global totalWait
    """Customer arrives, is served and leaves."""
    arrive = env.now

    print('%7.4f %s: Nuevo Proceso:' % (arrive, name))

    with CPU.request() as req:
        instructions = random.uniform(MIN_INSTRUCTIONS, MAX_INSTRUCTIONS)
        
        yield req

        wait = env.now - arrive
        totalWait = totalWait + wait
        
        while instructions > 0:
            
            instructions = instructions - 3

            # We got to the counter
            print('%7.4f %s: Waited %6.3f' % (env.now, name, wait))

            tip = random.expovariate(1.0 / time_in_process)

            if instructions < 0:
                
                yield env.timeout(tip)
                print('%7.4f %s: Finished' % (env.now, name))
                print('----------------------------------------------------')
                

# Setup and start the simulation
print('Bank renege')
random.seed(RANDOM_SEED)
env = simpy.Environment()

totalWait = 0

# Start processes and run
CPU = simpy.Resource(env, capacity=1)
RAM= simpy.Container(env, init=100, capacity=100)
env.process(source(env, NEW_PROCESS, INTERVAL_PROCESS, CPU))
env.run()

print ('tiempo total de espera ', totalWait)
print ('el promedio de tiempo de espera es ', totalWait/ NEW_PROCESS)
