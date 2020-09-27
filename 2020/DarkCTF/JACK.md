The challenge has some output depending of the input like "wrong" or "correct"... the input must be 16 plus '\n'. 
In cases like this angr should works well... so a first script WITHOUT any kinda of optimization gave me the flag... so even if it could be optimized, it works!

```python
import angr, claripy

base_addr = 0x0

proj = angr.Project("./a.out", main_opts={'base_addr': base_addr})

input_len = 16
inp = [claripy.BVS('flag_%d' %i, 8) for i in range(input_len)]
flag = claripy.Concat(*inp + [claripy.BVV(b'\n')])


# find_addr = 0x0000000000001482
# avoid_addr = [0x0000000000001461, 0x00000000000012AC]

state = proj.factory.full_init_state(stdin=flag)

for k in inp:
    state.solver.add(k < 0x7f)
    state.solver.add(k > 0x20)

simgr = proj.factory.simulation_manager(state)
simgr.run()
for i in simgr.deadended:
    print(i.solver.eval(flag, cast_to=bytes))
#simgr.explore(find=find_addr, avoid=avoid_addr)
```

The script produces 5 output, one is the correct flag:
```shell
b'(0("0$!!"$((0$0"\n'
b'n0_50@@@@@@@@@@0\n'
b'n0_5ymb0`00@@@@@\n'
b'n0_5ymb0l1c,3x30\n'
b'n0_5ymb0l1c,0@@!\n'
```

# FLAG
`darkCTF{n0_5ymb0l1c,3x30}`
