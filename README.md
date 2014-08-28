"""
Hoja de Trabajo 5
Simulacion de Procesador

Peter Bennett 13243

"""
import random

import simpy


RANDOM_SEED = 42
NEW_PROCESS = 200 # Numero total de procesos
INTERVAL_PROCESS = 10.0  # Genera los nuevos procesos cada X tiempo
MIN_INSTRUCTIONS = 1  # Min. number of instructions
MAX_INSTRUCTIONS = 10  # Max. number of instructions
MIN_RAM = 1  # Min. number of ram required
MAX_RAM = 10 # Max. number of ram required

def source(env, number, interval, CPU):
    """Source genera los procesos de forma aleatoria"""
        
    for i in range(number):
        c = prcs(env, 'Proceso%02d' % i, CPU, time_in_process=10.0)
        env.process(c)
        t = random.expovariate(1.0 / interval)
        yield env.timeout(t)

def prcs(env, name, CPU, time_in_process):
    global totalWait
    global totalRAM
    """Llega el nuevo proceso."""
    arrive = env.now
    

    with CPU.request() as req:

        
        cantRAM = random.uniform(MIN_RAM, MAX_RAM)
        

        yield RAM.get(cantRAM)
        totalRAM = totalRAM + cantRAM
        
        
        instructions = random.uniform(MIN_INSTRUCTIONS, MAX_INSTRUCTIONS)
        
        yield req

        wait = env.now - arrive
        totalWait = totalWait + wait
        
        while instructions > 0:
            
            instructions = instructions - 3

            # We got to the counter
            
           
            

            tip = random.expovariate(1.0 / time_in_process)

            if instructions < 0:
                
                yield env.timeout(tip)

                archi=open('datos.txt','a')
                archi.write(str(wait))
                archi.write('\n')

                archi.close()
                print('%7.4f %s: Nuevo Proceso:' % (arrive, name))
                print ('Cantidad de RAM requerida:', cantRAM)
                print ('Total RAM:', totalRAM)
                print('%7.4f %s: Espero %6.3f' % (env.now, name, wait))
                print('%7.4f %s: Terminado' % (env.now, name))
                print('----------------------------------------------------')
                totalRAM = totalRAM - cantRAM
                yield RAM.put(cantRAM)





# Setup and start the simulation
print('Procesador PB')
random.seed(RANDOM_SEED)
env = simpy.Environment()

totalWait = 0
totalRAM = 0

# Start processes and run
CPU = simpy.Resource(env, capacity=2)
RAM= simpy.Container(env, init=100, capacity=100)
env.process(source(env, NEW_PROCESS, INTERVAL_PROCESS, CPU))
env.run()

print ('tiempo total de espera ', totalWait)
print ('el promedio de tiempo de espera es ', totalWait/ NEW_PROCESS)
